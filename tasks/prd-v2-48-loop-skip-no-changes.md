[PRD]
# PRD: Hands-Free v2.48.0 — Loop Skip on No Changes

## Overview

When a loop iteration completes without producing any file changes, the current behavior is to silently skip auto-commit (as documented in the auto-commit edge cases table). However, this silent skip can be confusing — the user cannot tell from the session log whether an iteration was productive or not. This release adds a `Loop skip on no changes: warn` directive that emits a visible warning (instead of a silent skip) when an iteration produces no changes, helping users identify stalled or unnecessary iterations.

## Goals

- Add `Loop skip on no changes: warn` CLAUDE.md directive
- When set to `warn`, emit a visible notice if the iteration ends with no file changes
- Notice: `[hands-free] Loop skip: no changes produced in this iteration`
- Add directive to Available Persistent Settings table
- Bump version to 2.48.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.48.0`
- `grep "\[2.48.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop skip on no changes in Ralph Loop Integration

**Description:** As a skill user, I want a visible notice when an iteration produces no changes so I can identify stalled iterations quickly.

**Acceptance Criteria:**
- [ ] New `### Loop Skip on No Changes` sub-section added under `## Ralph Loop Integration` (after `### Loop Iteration Timeout`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Documents that "no changes" = `git status --short` produces no output
- [ ] Documents the notice: `[hands-free] Loop skip: no changes produced in this iteration`
- [ ] Documents that the notice is emitted in place of the silent skip
- [ ] Documents that the session log always captures the skip event regardless of this setting
- [ ] Documents that when `Loop quiet mode: on` is active, the warning is suppressed by quiet mode rules
- [ ] Default: absent — silent skip (existing behavior)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop skip on no changes

**Description:** As a skill user, I want to configure the no-changes behavior in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop skip on no changes: warn` row added to Available Persistent Settings table
- [ ] Effect description notes the visible notice and that session log always captures the event
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.48.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the skip-on-no-changes feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.47.0` to `2.48.0`
- [ ] New `## [2.48.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: warn mode, no-changes detection, session log capture
- [ ] `grep "^version:" SKILL.md` shows `2.48.0`
- [ ] `grep "\[2.48.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop skip on no changes: warn` emits a visible notice when an iteration ends with no file changes
- FR-2: "No changes" = `git status --short` produces empty output (no staged, unstaged, or untracked files)
- FR-3: Notice text: `[hands-free] Loop skip: no changes produced in this iteration`
- FR-4: The session log always captures the skip event regardless of this setting
- FR-5: When `Loop quiet mode: on` is active, the notice is suppressed (quiet mode governs routine announcements)
- FR-6: If absent, the existing silent skip behavior is unchanged

## Non-Goals

- Hard stopping or aborting the loop on no changes (only warn mode; no halt mode)
- Counting consecutive no-change iterations (single-iteration scope only)
- Differentiating between "truly no work done" and "changes were reverted" (only git status output is checked)

## Technical Considerations

- US-001 adds `### Loop Skip on No Changes` after `### Loop Iteration Timeout` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- No-changes detection method (git status --short empty) documented
- Session log capture behavior documented
- Quiet mode interaction documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.48.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
