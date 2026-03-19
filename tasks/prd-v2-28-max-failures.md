[PRD]
# PRD: Hands-Free v2.28.0 — Loop Max Consecutive Failures Guard

## Overview

In long ralph-loop runs, a persistent bug can cause every iteration to end with failing tests, spinning indefinitely without making progress. This release adds a `Loop max failures: N` CLAUDE.md directive that causes hands-free to issue a HARD STOP after N consecutive iterations all end with failing tests, preventing wasted compute and alerting the user to intervene.

## Goals

- Add `Loop max failures: N` CLAUDE.md directive
- Track consecutive failing-test iterations; fire a HARD STOP when the threshold is reached
- Document the announcement format and reset condition
- Add directive to Available Persistent Settings table
- Bump version to 2.28.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.28.0`
- `grep "\[2.28.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop max consecutive failures in Ralph Loop Integration

**Description:** As a skill user, I want hands-free to halt the loop after N consecutive failing iterations so I can intervene before more compute is wasted.

**Acceptance Criteria:**
- [ ] New `### Loop Max Consecutive Failures` sub-section added under `## Ralph Loop Integration` (after `### Loop Quiet Mode`)
- [ ] Section describes: when `Loop max failures: N` is set, hands-free tracks how many consecutive iterations ended with test failures; on reaching N, announce HARD STOP: `[hands-free] LOOP AUTO-STOP: <N> consecutive iterations ended with test failures. Resolve the failing tests and run /hands-free resume to continue.`
- [ ] Failure count resets to zero when an iteration completes with all tests passing
- [ ] If test result is indeterminate for an iteration, the failure count is not incremented
- [ ] Default: absent (no failure tracking); `Loop max failures: 0` or non-positive value disables the guard
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for max consecutive failures

**Description:** As a skill user, I want to configure the consecutive failure threshold in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop max failures: N` row added to Available Persistent Settings table
- [ ] Effect: fires HARD STOP after N consecutive iterations with test failures; absent by default
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.28.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the consecutive failure guard feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.27.0` to `2.28.0`
- [ ] New `## [2.28.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: max-failures directive, HARD STOP announcement, count-reset condition
- [ ] `grep "^version:" SKILL.md` shows `2.28.0`
- [ ] `grep "\[2.28.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop max failures: N` enables consecutive failure tracking; N is a positive integer
- FR-2: Failure count increments when an iteration ends with one or more failing tests (confirmed via systematic-debugging output or test runner result)
- FR-3: Failure count resets to zero when an iteration ends with all tests passing
- FR-4: On reaching N consecutive failures, announce a HARD STOP and await `/hands-free resume`
- FR-5: If test outcome is indeterminate (no test output available), do not increment the failure count

## Non-Goals

- Per-test tracking (only whole-iteration pass/fail)
- Auto-rollback on failure (user intervenes manually)
- Silencing the HARD STOP message

## Technical Considerations

- US-001 adds `### Loop Max Consecutive Failures` after `### Loop Quiet Mode` in `## Ralph Loop Integration`
- US-002 adds one row to the Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Consecutive failure guard documented with HARD STOP format
- Reset condition specified
- Indeterminate result handling clear
- Available Persistent Settings table updated
- Even code-fence count, version 2.28.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
