[PRD]
# PRD: Hands-Free v2.9.0 — Test Regression Guard

## Overview

The auto-commit flow currently commits at every milestone without verifying that the change didn't break previously-passing tests. This release adds a test regression guard: before any auto-commit, compare the current test results against the checkpoint's `test_summary`; if new failures appeared, block the commit, announce the regressions, and route to systematic-debugging. A `/hands-free test-baseline` command lets users manually record the current test pass/fail count as the new baseline.

## Goals

- Block auto-commits that introduce new test failures compared to the last checkpoint baseline
- Surface the delta clearly: which count went from pass to fail (N new failures)
- Route to systematic-debugging automatically when regression is detected
- Provide `/hands-free test-baseline` to set a fresh baseline after intentional test changes
- Bump version to 2.9.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.9.0`
- `grep "\[2.9.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify pre-commit test regression check

**Description:** As a skill user, I want auto-commit to block when new test failures appear compared to the checkpoint baseline so that regressions are never silently committed.

**Acceptance Criteria:**
- [ ] New `### Test Regression Guard` sub-section added under `## Auto-Commit`
- [ ] Specifies that before staging files for auto-commit, hands-free runs the project's test command (same detection logic as build state health check: `cargo test`, `pytest`, `npm test`, etc.)
- [ ] Compares result to `test_summary.failed` in the current checkpoint; if `current_failed > checkpoint_failed` → regression detected
- [ ] On regression: announce `[auto-commit] Blocked — N new test failure(s) detected. Routing to systematic-debugging.`; do NOT stage or commit; route to systematic-debugging
- [ ] On no checkpoint (first iteration): skip regression check, allow commit
- [ ] On stale/missing checkpoint: skip regression check, allow commit (same rule as checkpoint staleness)
- [ ] If no test runner detected: skip regression check, allow commit with announcement `[auto-commit] No test runner detected — skipping regression check`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Specify `/hands-free test-baseline` command

**Description:** As a skill user, I want a `/hands-free test-baseline` command that records the current test results as the new baseline so that I can reset after intentional test changes.

**Acceptance Criteria:**
- [ ] `/hands-free test-baseline` added to Commands block in SKILL.md
- [ ] `/hands-free test-baseline` added to Quick Reference table in SKILL.md
- [ ] New `## /hands-free test-baseline` section documenting behavior
- [ ] Command runs the project's test suite, records `passed`/`failed` counts in the checkpoint's `test_summary` field, and announces outcome
- [ ] Announce format: `[test-baseline] Baseline set — N passed / M failed (2026-03-19T14:00:00Z)`
- [ ] If checkpoint file doesn't exist, creates a minimal one with just `test_summary` and `written_at`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.9.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the test regression guard feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.8.0` to `2.9.0`
- [ ] New `## [2.9.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: pre-commit test regression check, `/hands-free test-baseline` command
- [ ] `grep "^version:" SKILL.md` shows `2.9.0`
- [ ] `grep "\[2.9.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: Regression check runs after deciding to auto-commit but before `git add`
- FR-2: Regression check only blocks if `current_failed > checkpoint_failed` (not equal; equal or fewer is OK)
- FR-3: Test command detection follows same project-type logic as build health check
- FR-4: `/hands-free test-baseline` works in all modes (not just loop mode)
- FR-5: Regression check is skipped when checkpoint is missing, stale, or malformed
- FR-6: Routing to systematic-debugging includes the regression delta in the brief

## Non-Goals

- Identifying which specific test cases are new failures (count delta only)
- Storing full test output in the checkpoint (counts only)
- Automatically fixing failing tests

## Technical Considerations

- US-001 adds a sub-section under `## Auto-Commit` (Safety Rules area)
- US-002 modifies Commands code block (~line 62) and Quick Reference table (~line 28), adds new `## /hands-free test-baseline` section after `## /hands-free context`
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Test regression check specified with project-type detection and delta logic
- Auto-commit blocked on regression with systematic-debugging routing
- `/hands-free test-baseline` in Commands, Quick Reference, and dedicated sub-section
- Even code-fence count, version 2.9.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
