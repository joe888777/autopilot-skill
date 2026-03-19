[PRD]
# PRD: Hands-Free v2.32.0 — Loop Max Duration

## Overview

The iteration time budget (v2.25.0) limits how long a single iteration runs. This release adds a complementary `Loop max duration: N` directive that sets a wall-clock time limit for the entire loop session. When the loop has been running for more than N minutes, hands-free fires a HARD STOP before the next iteration begins, giving the user a chance to review progress or end the loop.

## Goals

- Add `Loop max duration: <minutes>` CLAUDE.md directive
- At the start of each iteration, check total elapsed loop time; if exceeded, fire HARD STOP
- Document the announcement format and the in-iteration non-interruption rule
- Add directive to Available Persistent Settings table
- Bump version to 2.32.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.32.0`
- `grep "\[2.32.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop max duration in Ralph Loop Integration

**Description:** As a skill user, I want a wall-clock time limit on the entire loop so it pauses for review after running too long.

**Acceptance Criteria:**
- [ ] New `### Loop Max Duration` sub-section added under `## Ralph Loop Integration` (after `### Loop Checkpoint Tags`)
- [ ] Check fires at the **start** of each iteration (before any work begins); does NOT interrupt in-progress iterations
- [ ] Announcement: `[hands-free] LOOP MAX DURATION: Loop has been running for <X> minutes (limit: <N> min). Pausing before next iteration. Run /hands-free resume to continue or /hands-free loop-stop to end.`
- [ ] Default: absent (no time limit)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop max duration

**Description:** As a skill user, I want to configure the loop wall-clock limit in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop max duration: N` row added to Available Persistent Settings table
- [ ] Effect: fires HARD STOP at start of next iteration when total elapsed time exceeds N minutes; absent by default
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.32.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the max duration feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.31.0` to `2.32.0`
- [ ] New `## [2.32.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: max duration directive, start-of-iteration check, no mid-iteration interruption
- [ ] `grep "^version:" SKILL.md` shows `2.32.0`
- [ ] `grep "\[2.32.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop max duration: N` enables wall-clock tracking from session start
- FR-2: Check runs at the start of each iteration before any brainstorming, planning, or execution
- FR-3: If elapsed time ≥ N minutes, fire HARD STOP with resume/stop instructions
- FR-4: In-progress iterations are never interrupted mid-work by this check
- FR-5: `/hands-free resume` continues to the next iteration; the duration check re-runs at the next iteration start

## Non-Goals

- Interrupting mid-iteration (only checked at iteration boundaries)
- Per-phase time limits (use Loop iteration budget for that)
- Auto-stopping the loop (only pauses; user decides via resume or loop-stop)

## Technical Considerations

- US-001 adds `### Loop Max Duration` after `### Loop Checkpoint Tags` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Max duration directive documented with HARD STOP format
- Start-of-iteration check timing explicit
- No mid-iteration interruption guarantee clear
- Available Persistent Settings table updated
- Even code-fence count, version 2.32.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
