[PRD]
# PRD: Hands-Free v2.16.0 — Velocity Sparkline in Iteration Announcement

## Overview

The iteration announcement currently shows `velocity_trend` as a raw array (e.g. `[3,2,4,0,1]`). This release renders it as a Unicode sparkline (▁▂▃▄▅▆▇█) appended to the `velocity` line in the announcement, making trend direction instantly visible at a glance. The sparkline is also added to the `/hands-free status` loop section.

## Goals

- Render `velocity_trend` as a Unicode block sparkline in the iteration announcement
- Display the sparkline in `/hands-free status` loop section
- Bump version to 2.16.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.16.0`
- `grep "\[2.16.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Add velocity sparkline to iteration announcement

**Description:** As a skill user, I want to see a sparkline of velocity_trend in the iteration announcement so that I can gauge momentum at a glance.

**Acceptance Criteria:**
- [ ] `### Loop-Aware Behavior` iteration announcement updated to include sparkline after velocity value
- [ ] Sparkline rendering rule specified: map each `velocity_trend` value to a Unicode block character using 8 levels (▁▂▃▄▅▆▇█); 0 → ▁; max value in array → █; others scaled proportionally
- [ ] If `velocity_trend` is empty or missing → sparkline omitted (no error)
- [ ] Example announcement line: `velocity    : [3,2,4,0,1] ▄▃▆▁▂`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add velocity sparkline to /hands-free status loop section

**Description:** As a skill user, I want the sparkline visible in /hands-free status so I can check momentum without waiting for the next announcement.

**Acceptance Criteria:**
- [ ] `/hands-free status` loop section updated: `Loop velocity:` line shows sparkline after the trend array
- [ ] Example: `Loop velocity:          [3,2,4,0,1] ▄▃▆▁▂`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.16.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the velocity sparkline feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.15.0` to `2.16.0`
- [ ] New `## [2.16.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: velocity sparkline rendering, Unicode block mapping (8 levels), announcement and status display
- [ ] `grep "^version:" SKILL.md` shows `2.16.0`
- [ ] `grep "\[2.16.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: Sparkline maps velocity values to 8 Unicode block levels: ▁▂▃▄▅▆▇█; value 0 always maps to ▁; max value in the array maps to █; intermediate values scaled linearly
- FR-2: If all values in `velocity_trend` are 0 → render all as ▁ (e.g. `▁▁▁▁▁`)
- FR-3: If all values are equal and non-zero → render all as █ (constant high velocity)
- FR-4: Sparkline is appended on the same line as the velocity value in the announcement; no newline
- FR-5: Missing or empty `velocity_trend` → sparkline omitted silently

## Non-Goals

- Plotting velocity as a separate ASCII chart or graph
- Showing sparkline for health_history (covered by trend arrow in v2.14.0)
- Color-coding the sparkline characters

## Technical Considerations

- US-001 modifies the iteration announcement format block in `### Loop-Aware Behavior`
- US-002 modifies the `/hands-free status` output example in `## /hands-free status`
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Sparkline rendering rule specified with 8-level Unicode block mapping
- Announcement and status both updated
- Even code-fence count, version 2.16.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
