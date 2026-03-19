[PRD]
# PRD: Hands-Free v2.24.0 — Loop Branch Guard

## Overview

When ralph-loop runs on a protected branch (e.g., `main` or `master`), hands-free has no mechanism to warn the user that loop work is committing directly to a branch that should not receive direct commits. This release adds a loop branch guard that announces a warning at iteration start when the active branch is in the protected list, and documents the `Loop protected branches:` and `Loop branch guard:` CLAUDE.md directives.

## Goals

- Detect when loop-aware mode is active and the current branch is a configured protected branch
- Announce a warning (not a hard stop) at the start of each iteration on a protected branch
- Add `Loop protected branches: <list>` CLAUDE.md directive to configure which branches are protected
- Add `Loop branch guard: off` CLAUDE.md directive to disable the guard entirely
- Add both directives to the Available Persistent Settings table
- Bump version to 2.24.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.24.0`
- `grep "\[2.24.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop branch guard behavior in Ralph Loop Integration

**Description:** As a skill user, I want hands-free to warn me when the loop is running on a protected branch so I can redirect work to a feature branch before making further commits.

**Acceptance Criteria:**
- [ ] New `### Loop Branch Guard` sub-section added under `## Ralph Loop Integration` (before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Section describes: at the start of each iteration, if loop-aware mode is active AND the current branch (`git branch --show-current`) matches any branch in the protected list, announce:
  `[hands-free] Warning: loop is running on protected branch '<branch>' — consider switching to a feature branch`
- [ ] Note: the warning does NOT stop the iteration; it is advisory only
- [ ] Note: the warning fires once per session (not every iteration) to avoid repetition
- [ ] Default protected branches: `main`, `master`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md overrides for loop branch guard

**Description:** As a skill user, I want to configure which branches are protected and optionally disable the guard.

**Acceptance Criteria:**
- [ ] `Loop protected branches: main,master,release` in CLAUDE.md configures the protected branch list (comma-separated)
- [ ] `Loop branch guard: off` in CLAUDE.md disables the guard entirely
- [ ] Both rows added to Available Persistent Settings table in `## CLAUDE.md Override Reference`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.24.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the loop branch guard.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.23.0` to `2.24.0`
- [ ] New `## [2.24.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: loop branch guard, protected branch detection, advisory warning, CLAUDE.md overrides
- [ ] `grep "^version:" SKILL.md` shows `2.24.0`
- [ ] `grep "\[2.24.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: At iteration start in loop-aware mode, run `git branch --show-current` to detect the active branch
- FR-2: Compare the active branch against the protected branch list (default: `main`, `master`)
- FR-3: If the branch matches, announce the warning once per session (not every iteration)
- FR-4: The warning is advisory; the iteration proceeds normally regardless
- FR-5: `Loop branch guard: off` suppresses the check entirely for the session

## Non-Goals

- Blocking the loop from running on a protected branch (advisory only)
- Auto-creating a feature branch on the user's behalf
- Remote branch protection rule enforcement

## Technical Considerations

- US-001 adds `### Loop Branch Guard` section in `## Ralph Loop Integration`
- US-002 adds two rows to the Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Loop branch guard documented with default protected branch list
- Advisory warning format specified
- Once-per-session firing behavior documented
- CLAUDE.md overrides in Available Persistent Settings table
- Even code-fence count, version 2.24.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
