[PRD]
# PRD: Hands-Free v2.50.0 — Loop Pre-Iteration Hook

## Overview

Some projects need to run a setup or validation command at the start of every loop iteration — for example, checking that a database is up, pulling the latest seed data, or verifying environment variables are set. This release adds a `Loop pre-iteration hook: <cmd>` directive that runs a specified shell command at the start of each iteration before any work begins. If the command exits non-zero, a HARD STOP is fired.

## Goals

- Add `Loop pre-iteration hook: <cmd>` CLAUDE.md directive
- When set, run the command at the start of each iteration before any work
- If exit code is non-zero, fire a HARD STOP
- Add directive to Available Persistent Settings table
- Bump version to 2.50.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.50.0`
- `grep "\[2.50.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop pre-iteration hook in Ralph Loop Integration

**Description:** As a skill user, I want to run a validation or setup command before each iteration so I can ensure the environment is ready before work begins.

**Acceptance Criteria:**
- [ ] New `### Loop Pre-Iteration Hook` sub-section added under `## Ralph Loop Integration` (after `### Loop Branch Guard`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Documents that the command runs at the start of each iteration, before the build state health check
- [ ] Documents that exit code 0 = proceed; non-zero = HARD STOP
- [ ] HARD STOP message documented: `[hands-free] HARD STOP — Pre-iteration hook failed (exit <N>): <cmd>. Run /hands-free resume to retry or skip the hook for this iteration.`
- [ ] Documents that after `/hands-free resume`, the iteration proceeds without re-running the hook
- [ ] Documents that the command is run with cwd-scope rules (same as other shell commands)
- [ ] If absent, no pre-iteration hook is run
- [ ] Default: absent
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop pre-iteration hook

**Description:** As a skill user, I want to configure the pre-iteration hook in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop pre-iteration hook: <cmd>` row added to Available Persistent Settings table
- [ ] Effect description notes exit code semantics and resume behavior
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.50.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the pre-iteration hook feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.49.0` to `2.50.0`
- [ ] New `## [2.50.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: pre-iteration hook directive, exit code semantics, resume behavior
- [ ] `grep "^version:" SKILL.md` shows `2.50.0`
- [ ] `grep "\[2.50.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop pre-iteration hook: <cmd>` specifies a shell command to run at the start of each iteration
- FR-2: Command runs before the build state health check (first thing in each iteration)
- FR-3: Exit code 0 = proceed; non-zero = HARD STOP
- FR-4: HARD STOP message: `[hands-free] HARD STOP — Pre-iteration hook failed (exit <N>): <cmd>. Run /hands-free resume to retry or skip the hook for this iteration.`
- FR-5: After `/hands-free resume`, the iteration proceeds without re-running the hook
- FR-6: Command follows cwd-scope classification rules
- FR-7: If absent, no pre-iteration hook runs

## Non-Goals

- Multiple pre-iteration hooks (single command only)
- Post-iteration hooks (only pre-iteration scope)
- Retry logic for failed hooks (single attempt per iteration)
- Capturing hook output for use in subsequent steps (only exit code checked)

## Technical Considerations

- US-001 adds `### Loop Pre-Iteration Hook` after `### Loop Branch Guard` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Hook timing (before build health check) documented
- Exit code semantics documented
- Resume behavior (skip hook, proceed) documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.50.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
