[PRD]
# PRD: Hands-Free v2.36.0 — Loop Pre-Iteration Hook

## Overview

Users sometimes need to run setup commands before each iteration begins — pulling fresh data, checking external dependencies, or resetting state. This release adds a `Loop pre-iteration: <command>` CLAUDE.md directive that runs a shell command at the very start of each iteration, before any work begins.

## Goals

- Add `Loop pre-iteration: <command>` CLAUDE.md directive
- Run the command at the start of each iteration before any brainstorming, planning, or execution
- Apply the same cwd-scope and HARD STOP rules as all other shell commands
- If the command fails, fire a HARD STOP before proceeding with the iteration
- Add directive to Available Persistent Settings table
- Bump version to 2.36.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.36.0`
- `grep "\[2.36.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop pre-iteration hook in Ralph Loop Integration

**Description:** As a skill user, I want a hook that runs before each iteration so I can set up the environment or pull fresh state.

**Acceptance Criteria:**
- [ ] New `### Loop Pre-Iteration Hook` sub-section added under `## Ralph Loop Integration` (after `### Loop On-Failure Hook`)
- [ ] Hook runs at the start of each iteration before any brainstorming, planning, or execution
- [ ] Announce: `[hands-free] Pre-iteration hook: running '<command>'`
- [ ] Same cwd-scope and HARD STOP rules apply as all other shell commands
- [ ] If the command fails (non-zero exit), fire a HARD STOP before proceeding: `[hands-free] HARD STOP — pre-iteration hook failed: <error>. Fix the hook and run /hands-free resume to continue.`
- [ ] Default: absent
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for pre-iteration hook

**Description:** As a skill user, I want to configure the pre-iteration hook in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop pre-iteration: <command>` row added to Available Persistent Settings table
- [ ] Effect description notes that it runs before each iteration, failure fires HARD STOP
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.36.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the pre-iteration hook feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.35.0` to `2.36.0`
- [ ] New `## [2.36.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: pre-iteration hook, runs before work, failure fires HARD STOP
- [ ] `grep "^version:" SKILL.md` shows `2.36.0`
- [ ] `grep "\[2.36.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop pre-iteration: <command>` registers a hook that runs before each iteration
- FR-2: Hook runs before any brainstorming, planning, or execution in the iteration
- FR-3: Same shell auto-pass/HARD STOP rules apply as any other shell command
- FR-4: Hook failure (non-zero exit) fires a HARD STOP — iteration does not proceed
- FR-5: After `/hands-free resume`, the hook runs again at the start of the next iteration

## Non-Goals

- On-complete hook (v2.33.0) and on-failure hook (v2.35.0)
- Passing iteration context as arguments to the hook command
- Multiple pre-iteration hooks

## Technical Considerations

- US-001 adds `### Loop Pre-Iteration Hook` after `### Loop On-Failure Hook` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Pre-iteration hook documented with timing and failure behavior
- HARD STOP on failure is explicit (distinguishes from on-failure/on-complete which are advisory)
- Security rules noted
- Available Persistent Settings table updated
- Even code-fence count, version 2.36.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
