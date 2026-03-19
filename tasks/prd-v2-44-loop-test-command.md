[PRD]
# PRD: Hands-Free v2.44.0 — Loop Test Command

## Overview

Hands-free detects the project type (Rust, Python, TypeScript, Go) and runs a default test command for iteration health checks. For projects with non-standard test setups or where a subset test is faster, this default may be wrong or too slow. This release adds a `Loop test command: <cmd>` directive that overrides the auto-detected test command for all loop health checks and iteration state assessments.

## Goals

- Add `Loop test command: <cmd>` CLAUDE.md directive
- When set, use the specified command instead of auto-detected test runner for iteration health checks
- Apply to: build state health check, completion promise test evaluation, regression detection, max-failures tracking, auto-stop conditions
- Add directive to Available Persistent Settings table
- Bump version to 2.44.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.44.0`
- `grep "\[2.44.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop test command in Ralph Loop Integration

**Description:** As a skill user, I want to specify a custom test command so hands-free uses the right test suite for my project without needing to rely on auto-detection.

**Acceptance Criteria:**
- [ ] New `### Loop Test Command` sub-section added under `## Ralph Loop Integration` (after `### Loop Abort on Regression`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Documents that the command overrides auto-detection for all loop health checks
- [ ] Lists where the override applies: build health check at iteration start, test-pass evaluation for completion promise, regression count tracking, max-failures tracking, auto-stop condition checks
- [ ] Documents that the command runs with the same cwd-scoped auto-pass rules as other shell commands
- [ ] If the command is absent, auto-detection proceeds as documented in the Build State Health Check section
- [ ] Default: absent — auto-detection is used
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop test command

**Description:** As a skill user, I want to configure the test command in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop test command: <cmd>` row added to Available Persistent Settings table
- [ ] Effect description notes the override applies to all loop health checks
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.44.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the test command override feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.43.0` to `2.44.0`
- [ ] New `## [2.44.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: test command override, applies to all health checks
- [ ] `grep "^version:" SKILL.md` shows `2.44.0`
- [ ] `grep "\[2.44.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop test command: <cmd>` specifies the shell command used for all loop test evaluations
- FR-2: The override applies to: build state health check, completion promise evaluation, regression count tracking, max-failures tracking, auto-stop health floor check
- FR-3: The command is run with cwd-scope rules (same classification as other shell commands in auto-pass rules)
- FR-4: If absent, auto-detection proceeds as documented in the Build State Health Check section
- FR-5: Exit code 0 = tests passing; non-zero = tests failing
- FR-6: The command's stdout/stderr output is used to extract failure count (best-effort; if count cannot be parsed, hands-free records the exit code only)

## Non-Goals

- Multiple test commands (only one override supported)
- Per-phase test commands (one command applies to all health checks)
- Parsing all possible test runner output formats (best-effort count extraction only)

## Technical Considerations

- US-001 adds `### Loop Test Command` after `### Loop Abort on Regression` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Override scope (all loop health checks) clearly documented
- Auto-detection fallback documented
- Exit code semantics documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.44.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
