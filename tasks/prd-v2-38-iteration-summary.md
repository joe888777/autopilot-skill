[PRD]
# PRD: Hands-Free v2.38.0 — Loop Iteration Summary

## Overview

After each iteration, users often want a quick summary of what happened — how many tests passed, how many commits were made, what warnings fired. This release adds a `Loop iteration summary: on` directive that outputs a brief structured summary at the end of each iteration.

## Goals

- Add `Loop iteration summary: on/off` CLAUDE.md directive
- Output a brief summary after each iteration completes
- Include: iteration number, status, commits made, test results, warnings fired
- Add directive to Available Persistent Settings table
- Bump version to 2.38.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.38.0`
- `grep "\[2.38.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop iteration summary in Ralph Loop Integration

**Description:** As a skill user, I want a brief summary after each iteration so I can track progress at a glance.

**Acceptance Criteria:**
- [ ] New `### Loop Iteration Summary` sub-section added under `## Ralph Loop Integration` (after `### Loop Cooldown`)
- [ ] Summary output format documented in the section
- [ ] Summary fires after each iteration completes (including skipped iterations, which show status: skipped)
- [ ] Default: off
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for iteration summary

**Description:** As a skill user, I want to configure iteration summaries in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop iteration summary: on/off` row added to Available Persistent Settings table
- [ ] Effect description notes the per-iteration summary output
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.38.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the iteration summary feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.37.0` to `2.38.0`
- [ ] New `## [2.38.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: iteration summary directive, output format, per-iteration
- [ ] `grep "^version:" SKILL.md` shows `2.38.0`
- [ ] `grep "\[2.38.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop iteration summary: on` enables per-iteration summary output
- FR-2: Summary fires after each iteration completes (or is skipped/stopped)
- FR-3: Summary includes: iteration N, status (completed/skipped/paused/hard-stop), commits, tests, warnings
- FR-4: Summary is output to the conversation (not to a file — use Loop notes for file output)
- FR-5: Summary for skipped iterations shows status: skipped with reason

## Non-Goals

- Writing summary to a file (use `Loop notes: on` for that)
- Custom summary fields
- Aggregated multi-iteration summaries (that is a separate feature)

## Technical Considerations

- US-001 adds `### Loop Iteration Summary` after `### Loop Cooldown` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- The summary format uses a code block (adds 2 fences)

## Success Metrics

- Iteration summary documented with output format
- Distinction from Loop notes (conversation vs file) clear
- Available Persistent Settings table updated
- Even code-fence count, version 2.38.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
