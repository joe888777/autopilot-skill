[PRD]
# PRD: Hands-Free v2.14.0 — Health Score History & Trend Analysis

## Overview

v2.13.0 computes a health score per iteration but discards it after the announcement. This release persists the last 5 health scores in the checkpoint as `health_history`, computes a trend direction (improving/stable/declining), appends a trend arrow to the iteration announcement, and fires a regression warning if the current score drops 20+ points below the historical peak.

## Goals

- Persist `health_history` (FIFO int[] of last 5 health scores) in the checkpoint
- Compute trend direction from `health_history` on each iteration
- Display trend arrow (↑ / → / ↓) appended to the `health` line in the iteration announcement
- Warn when health regression of ≥ 20 points below peak is detected
- Bump version to 2.14.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.14.0`
- `grep "\[2.14.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Add `health_history` field to checkpoint schema and update JSON example

**Description:** As a skill user, I want the checkpoint to record the last 5 health scores so that trend analysis is possible without requiring external storage.

**Acceptance Criteria:**
- [ ] `health_history` field added to checkpoint JSON schema example (after `health_score`)
- [ ] `health_history` is an int[] containing up to 5 entries, oldest first, newest last
- [ ] Update rule: append `health_score` to `health_history`, then prune to last 5 (FIFO)
- [ ] Field definitions table row added for `health_history`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Specify trend computation and regression warning in `### Loop Health Score`

**Description:** As a skill user, I want trend direction computed from `health_history` and a regression warning when health drops sharply so that I have early warning of deteriorating workspace health.

**Acceptance Criteria:**
- [ ] `### Loop Health Score` sub-section updated with trend direction rules: `len(health_history) >= 2` AND `last > first` → improving (↑); `last < first` → declining (↓); `last == first` or `len < 2` → stable (→)
- [ ] Regression warning rule: if `health_score < max(health_history) − 20` (and `len(health_history) >= 2`) → fire warning: `[hands-free] Warning: Health regression — score dropped N points from peak (peak: X, current: Y). Review failing pillars.`
- [ ] Iteration announcement `health` line updated to show trend arrow: `health: 72/100 (T:80 S:80 V:60 C:100) ↓`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.14.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the health history and trend feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.13.0` to `2.14.0`
- [ ] New `## [2.14.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: `health_history` FIFO array, trend direction (↑/→/↓), regression warning (−20 from peak), trend arrow in announcement
- [ ] `grep "^version:" SKILL.md` shows `2.14.0`
- [ ] `grep "\[2.14.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `health_history` is updated on every checkpoint write (append `health_score`, prune to 5)
- FR-2: Trend is computed only when `len(health_history) >= 2`; if shorter, trend is stable (→)
- FR-3: Regression warning fires once per iteration (not repeatedly); it does not block or pause the loop
- FR-4: Trend arrow is appended to the `health` line in the iteration announcement, not on a separate line
- FR-5: `health_history` is omitted from the checkpoint if `health_score` is null

## Non-Goals

- Plotting health history as a chart or graph
- Storing more than 5 historical scores
- Auto-stopping the loop based on health regression (separate feature)
- Regression alerts via external notification channels

## Technical Considerations

- US-001 modifies `### Iteration Context Checkpoint` JSON schema and field table
- US-002 modifies `### Loop Health Score` sub-section and the iteration announcement format block
- US-003 modifies frontmatter and CHANGELOG.md
- The trend arrow must not break the existing announcement line format — append with a space

## Success Metrics

- `health_history` persisted in checkpoint schema with FIFO update rule
- Trend direction computed and displayed with arrow
- Regression warning defined with clear threshold (−20 from peak)
- Even code-fence count, version 2.14.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
