[PRD]
# PRD: Hands-Free v2.17.0 — Loop Completion Summary

## Overview

When the completion promise is met, hands-free currently just announces the promise output and hands off to finishing-a-development-branch. This release adds a structured completion summary block, printed immediately when the promise evaluates to true, that consolidates all loop run statistics in one place before the user decides how to merge.

## Goals

- Output a rich completion summary when the loop promise is met
- Summary includes: iterations used, stories completed, commits made, final health score, velocity trend sparkline
- Bump version to 2.17.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.17.0`
- `grep "\[2.17.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify completion summary format in Loop-Aware Behavior

**Description:** As a skill user, I want a structured summary when the loop promise is met so that I can quickly review what was accomplished before merging.

**Acceptance Criteria:**
- [ ] New `### Loop Completion Summary` sub-section added under `## Ralph Loop Integration` (after `### Loop Auto-Stop Conditions`)
- [ ] Summary block format specified:
  ```
  [hands-free] Loop complete — promise met at iteration N
  ┌─────────────────────────────────────────────┐
  │  Iterations used : N / max                  │
  │  Stories done    : total_stories_completed  │
  │  Commits made    : total_commits            │
  │  Final health    : score/100 (T:x S:x V:x C:x) arrow  │
  │  Velocity trend  : [a,b,c,d,e] sparkline   │
  └─────────────────────────────────────────────┘
  ```
- [ ] Summary is output once immediately when the promise evaluates to true
- [ ] If checkpoint data is unavailable for any field, substitute `N/A` rather than omitting the line
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Align /hands-free metrics sparkline with v2.16.0 standard

**Description:** As a skill user, I want the sparkline in /hands-free metrics to use the same 8-level rule as the iteration announcement.

**Acceptance Criteria:**
- [ ] `## /hands-free metrics` sparkline mapping updated from the 6-category rule to the v2.16.0 standardized 8-level rule: value 0 → ▁; max value → █; others scaled linearly; all equal non-zero → all █
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.17.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the completion summary feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.16.0` to `2.17.0`
- [ ] New `## [2.17.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: loop completion summary block, sparkline alignment in /hands-free metrics
- [ ] `grep "^version:" SKILL.md` shows `2.17.0`
- [ ] `grep "\[2.17.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: Completion summary fires once per loop run, immediately when the promise evaluates to true
- FR-2: Summary draws data from the checkpoint: `iteration`, `max_iterations`, `metrics.total_stories_completed`, `metrics.total_commits`, `health_score`, `health_history`, `metrics.velocity_trend`
- FR-3: Velocity sparkline in summary uses the 8-level formula from v2.16.0
- FR-4: Completion summary precedes the mandatory pre-push review checkpoint

## Non-Goals

- Sending the completion summary to external notification channels
- Persisting the summary beyond the session log
- Showing the summary for non-promise completions (e.g., max-iterations exhausted)

## Technical Considerations

- US-001 adds sub-section after `### Loop Auto-Stop Conditions` in SKILL.md
- US-002 modifies the `## /hands-free metrics` sparkline mapping description
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Completion summary format specified with all fields and fallback behavior
- /hands-free metrics sparkline aligned with v2.16.0
- Even code-fence count, version 2.17.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
