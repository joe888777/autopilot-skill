[PRD]
# PRD: Hands-Free v2.12.0 — Loop Metrics & Velocity Tracking

## Overview

The iteration checkpoint captures point-in-time state but provides no longitudinal view of how the loop is performing across iterations. This release adds a `metrics` object to the checkpoint that tracks per-iteration velocity (stories and commits), maintains a rolling velocity trend window, enhances stall detection to use the trend, adds a `/hands-free metrics` on-demand command, and includes a Metrics section in the finishing-branch completion output.

## Goals

- Record per-iteration stories and commit counts in the checkpoint
- Maintain a rolling 5-iteration velocity trend array
- Provide `/hands-free metrics` for on-demand velocity reporting
- Enhance stall detection with trend-based zero-velocity detection
- Include a Metrics summary in the finishing-branch phase output
- Bump version to 2.12.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.12.0`
- `grep "\[2.12.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Add metrics object to iteration checkpoint schema

**Description:** As a skill user, I want the checkpoint to record per-iteration velocity data so that the loop can detect trends and stalls without relying solely on test output.

**Acceptance Criteria:**
- [ ] New `### Metrics Tracking` sub-section added under `## Ralph Loop Integration`
- [ ] Specifies `metrics` object added to `.claude/iteration-checkpoint.json` schema
- [ ] `metrics.stories_completed_this_iteration`: integer count of stories closed in the current iteration
- [ ] `metrics.commits_this_iteration`: integer count of commits tagged `[ralph #N]` in the current iteration
- [ ] `metrics.velocity_trend`: array of last 5 `stories_completed_this_iteration` values (oldest first, newest last); pruned to 5 entries after each write
- [ ] `metrics.total_stories_completed`: running total across all iterations
- [ ] `metrics.total_commits`: running total commits across all iterations
- [ ] JSON schema example updated to include the `metrics` object
- [ ] Field definitions table updated with `metrics.*` fields
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add `/hands-free metrics` command

**Description:** As a skill user, I want a `/hands-free metrics` read-only command so that I can inspect loop velocity at any point without modifying state.

**Acceptance Criteria:**
- [ ] `/hands-free metrics` added to Commands block in SKILL.md
- [ ] `/hands-free metrics` added to Quick Reference table in SKILL.md
- [ ] New `## /hands-free metrics` section documenting output format
- [ ] Output format: velocity summary table (iteration, stories, commits per row for last 5) + trend sparkline (▁▂▃▄█ characters based on relative counts)
- [ ] If no checkpoint: `[metrics] No checkpoint found — run at least one loop iteration first`
- [ ] If checkpoint missing metrics field: `[metrics] No velocity data yet — metrics tracked from next iteration`
- [ ] Shows total_stories_completed and total_commits as summary footer
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Add Metrics section to finishing-branch output and enhance stall detection

**Description:** As a skill user, I want the finishing-branch completion output to include a velocity summary, and I want stall detection to use the velocity trend so that zero-progress loops are caught reliably.

**Acceptance Criteria:**
- [ ] `### PR Auto-Description` updated: PR body includes `## Metrics` section with average velocity (total_stories / iterations) and total counts
- [ ] Stall detection rule updated: if last 3 entries in `velocity_trend` are all `0`, trigger stall warning
- [ ] Stall warning announcement updated to reference velocity_trend: `[hands-free] Warning: Velocity stall — 0 stories completed in last 3 iterations (trend: 0, 0, 0). Pausing for user input.`
- [ ] Existing stall detection checks remain; velocity_trend is an additional trigger
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-004: Bump version to 2.12.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the metrics feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.11.0` to `2.12.0`
- [ ] New `## [2.12.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: metrics object in checkpoint, `/hands-free metrics` command, velocity-trend stall detection, Metrics in finishing-branch output
- [ ] `grep "^version:" SKILL.md` shows `2.12.0`
- [ ] `grep "\[2.12.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `velocity_trend` is a FIFO array capped at 5 entries; on each checkpoint write, append current iteration's story count and drop oldest if length > 5
- FR-2: `/hands-free metrics` is read-only; it never modifies the checkpoint
- FR-3: Stall detection using velocity_trend fires when ALL of: `len(velocity_trend) >= 3` AND last 3 values are all `0`
- FR-4: The sparkline maps 0 → ▁, 1 → ▂, 2-3 → ▃, 4-5 → ▄, 6+ → █
- FR-5: Metrics section in finishing-branch PR body uses checkpoint data; if no metrics, omits the section entirely
- FR-6: `total_stories_completed` and `total_commits` are additive across iterations; never reset within a loop run

## Non-Goals

- Persisting metrics history beyond the checkpoint's rolling 5-iteration window
- Visualising metrics in a UI or dashboard
- Per-story timing/duration tracking
- Cross-loop comparison (each loop run starts fresh)

## Technical Considerations

- US-001 modifies the checkpoint JSON schema and adds `### Metrics Tracking` sub-section
- US-002 adds Commands entry, Quick Reference entry, and `## /hands-free metrics` section
- US-003 modifies `### PR Auto-Description` and stall detection paragraph
- US-004 modifies frontmatter and CHANGELOG.md

## Success Metrics

- `metrics` object present in checkpoint schema with all 5 fields
- `/hands-free metrics` in Commands, Quick Reference, and dedicated section
- Stall detection references velocity_trend
- Even code-fence count, version 2.12.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
