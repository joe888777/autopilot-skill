[PRD]
# PRD: Hands-Free v2.49.0 — Loop Branch Guard

## Overview

When running a loop on a long-lived development branch, it's easy to accidentally start the loop on the wrong branch (e.g., `main` instead of `feature/my-feature`). This release adds a `Loop branch guard: <branch>` directive that fires a HARD STOP at the start of each iteration if the current git branch does not match the specified branch name, preventing unintended work on the wrong branch.

## Goals

- Add `Loop branch guard: <branch>` CLAUDE.md directive
- When set, check the current git branch at the start of each iteration
- If the branch does not match, fire a HARD STOP
- Add directive to Available Persistent Settings table
- Bump version to 2.49.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.49.0`
- `grep "\[2.49.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop branch guard in Ralph Loop Integration

**Description:** As a skill user, I want the loop to stop if I accidentally started it on the wrong branch so I can switch branches before any changes are made.

**Acceptance Criteria:**
- [ ] New `### Loop Branch Guard` sub-section added under `## Ralph Loop Integration` (after `### Loop Skip on No Changes`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Documents that the check uses `git branch --show-current` to get the current branch
- [ ] HARD STOP message documented: `[hands-free] HARD STOP — Branch guard: current branch is '<actual>' but Loop branch guard requires '<expected>'. Switch to the correct branch and run /hands-free resume.`
- [ ] Documents that the check runs at the start of each iteration, before any work begins
- [ ] Documents that detached HEAD state causes the same HARD STOP (with actual branch shown as `(detached HEAD)`)
- [ ] If absent, no branch check is performed
- [ ] Default: absent — no limit
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop branch guard

**Description:** As a skill user, I want to configure the branch guard in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop branch guard: <branch>` row added to Available Persistent Settings table
- [ ] Effect description notes the HARD STOP trigger and detached HEAD behavior
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.49.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the branch guard feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.48.0` to `2.49.0`
- [ ] New `## [2.49.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: branch guard directive, HARD STOP trigger, detached HEAD behavior
- [ ] `grep "^version:" SKILL.md` shows `2.49.0`
- [ ] `grep "\[2.49.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop branch guard: <branch>` specifies the required git branch for loop execution
- FR-2: Check runs at the start of each iteration, before any work begins
- FR-3: Branch detected via `git branch --show-current`
- FR-4: If branch does not match, fire HARD STOP: `[hands-free] HARD STOP — Branch guard: current branch is '<actual>' but Loop branch guard requires '<expected>'. Switch to the correct branch and run /hands-free resume.`
- FR-5: Detached HEAD state causes the same HARD STOP with actual branch shown as `(detached HEAD)`
- FR-6: If absent, no branch check is performed

## Non-Goals

- Wildcard or pattern matching for branch names (exact match only)
- Auto-switching to the required branch (only stops; does not switch)
- Multiple allowed branches (single branch specification only)

## Technical Considerations

- US-001 adds `### Loop Branch Guard` after `### Loop Skip on No Changes` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Branch detection method documented
- HARD STOP message format documented
- Detached HEAD behavior documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.49.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
