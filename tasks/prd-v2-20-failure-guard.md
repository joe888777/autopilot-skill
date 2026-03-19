[PRD]
# PRD: Hands-Free v2.20.0 — Consecutive Failure Guard

## Overview

The loop currently stops on health floor breaches and zero-progress stalls, but has no mechanism to detect when the *same specific failure* recurs unchanged across consecutive iterations — a sign that the agent is stuck in a repair loop without making real progress. This release adds a consecutive-failure guard that fires when the identical failing test or build error appears in 3 consecutive iterations.

## Goals

- Detect when the same failure repeats unchanged for 3 consecutive iterations
- Halt the current iteration and notify the user with the failure identifier
- Allow the condition to be disabled or reconfigured via CLAUDE.md
- Log the auto-stop event in the session log
- Bump version to 2.20.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.20.0`
- `grep "\[2.20.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify consecutive-failure guard in Loop Auto-Stop Conditions

**Description:** As a skill user, I want hands-free to halt when the same failure repeats unchanged so that I am notified before the loop wastes further iterations on an unresolvable error.

**Acceptance Criteria:**
- [ ] New row added to the auto-stop conditions table in `### Loop Auto-Stop Conditions`:
  - Condition: Consecutive Failure
  - Trigger: same failing test name or build error string appears in `test_summary` (or build output) for 3 consecutive iterations
  - Announcement: `[hands-free] LOOP AUTO-STOP: Same failure repeated 3 iterations (<identifier>). Pausing for user review.`
- [ ] Tracking note: failure identifier tracked in session memory only; cleared when a different failure (or no failure) is observed
- [ ] Log entry: `[auto-stop] consecutive-failure: <identifier> — iteration N halted`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Specify CLAUDE.md overrides for consecutive-failure guard

**Description:** As a skill user, I want to disable or reconfigure the failure guard via CLAUDE.md.

**Acceptance Criteria:**
- [ ] `- Loop failure guard: off` in CLAUDE.md disables the consecutive-failure guard entirely
- [ ] `- Loop failure repeat: 5` in CLAUDE.md overrides the default consecutive count from 3 to the specified value
- [ ] Both override rows added to the Available Persistent Settings table in `## CLAUDE.md Override Reference`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.20.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the consecutive-failure guard feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.19.0` to `2.20.0`
- [ ] New `## [2.20.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: consecutive-failure guard (3 identical failures → halt), CLAUDE.md overrides (off / repeat count)
- [ ] `grep "^version:" SKILL.md` shows `2.20.0`
- [ ] `grep "\[2.20.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: Failure identifier extracted from `test_summary` field in iteration checkpoint; if unavailable, use the first line of the build error output
- FR-2: Guard tracks one failure identifier at a time; if the failure changes, the consecutive count resets
- FR-3: Guard fires only when the SAME identifier repeats for the configured consecutive count (default: 3)
- FR-4: Guard is independent of and additive to health floor and zero-progress guards
- FR-5: `Loop failure guard: off` disables only this guard; health floor and zero-progress remain active

## Non-Goals

- Storing failure history to the checkpoint (session-scoped tracking only)
- Automatically attempting a different fix strategy when the guard fires
- Tracking multiple simultaneous failure identifiers

## Technical Considerations

- US-001 adds a new row to the auto-stop conditions table in `### Loop Auto-Stop Conditions`
- US-001 also adds a tracking note after the table (parallel to the existing health-floor tracking note)
- US-002 adds rows to the Available Persistent Settings table in `## CLAUDE.md Override Reference`
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Consecutive-failure guard specified with exact trigger criteria and announcement format
- CLAUDE.md override reference updated
- Even code-fence count, version 2.20.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
