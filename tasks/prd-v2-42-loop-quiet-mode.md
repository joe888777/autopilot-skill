[PRD]
# PRD: Hands-Free v2.42.0 — Loop Quiet Mode

## Overview

In long-running ralph-loops, hands-free produces frequent per-iteration announcements (loop-aware status lines, backoff notices, cooldown notices, auto-commit tags, auto-push confirmations). For CI-like environments or experienced users who don't need verbose output, this creates noise. This release adds a `Loop quiet mode: on` directive that suppresses routine per-iteration announcements while preserving all warnings, HARD STOPs, and mandatory checkpoint output.

## Goals

- Add `Loop quiet mode: on/off` CLAUDE.md directive
- When enabled, suppress routine per-iteration announcements (loop-aware status line, cooldown/backoff waits, auto-commit announcements, auto-push confirmations)
- Preserve all warnings, HARD STOPs, stall detection, iteration warnings (final 3), and mandatory review checkpoints — these always print regardless of quiet mode
- Add directive to Available Persistent Settings table
- Bump version to 2.42.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.42.0`
- `grep "\[2.42.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop quiet mode in Ralph Loop Integration

**Description:** As a skill user, I want to suppress verbose per-iteration output so long-running loops don't flood the conversation with routine announcements.

**Acceptance Criteria:**
- [ ] New `### Loop Quiet Mode` sub-section added under `## Ralph Loop Integration` (after `### Loop Backoff`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Suppressed announcements listed: iteration-start status line, cooldown wait, backoff wait, auto-commit message, auto-push success confirmation
- [ ] Always-printed output listed: warnings (stall, max-failures, final-3-iterations), HARD STOPs, mandatory review checkpoints, completion promise output
- [ ] Default: off
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop quiet mode

**Description:** As a skill user, I want to configure quiet mode in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop quiet mode: on/off` row added to Available Persistent Settings table
- [ ] Effect description notes what is suppressed and what is preserved
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.42.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the quiet mode feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.41.0` to `2.42.0`
- [ ] New `## [2.42.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: quiet mode, suppressed vs preserved output
- [ ] `grep "^version:" SKILL.md` shows `2.42.0`
- [ ] `grep "\[2.42.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop quiet mode: on` suppresses per-iteration routine announcements
- FR-2: Suppressed in quiet mode: `[hands-free] Iteration N/M — ...` status line, `[hands-free] Cooldown: waiting Xs`, `[hands-free] Backoff: waiting Xs after N failures`, `[auto-commit] ...` per-iteration commit messages, `[hands-free] Auto-push: pushed successfully`
- FR-3: Always printed regardless of quiet mode: `[hands-free] Warning: ...` lines, HARD STOP announcements, mandatory review checkpoint blocks, `[hands-free] LOOP AUTO-STOP: ...`, completion promise output, pre-push announcement (so failures have context), iteration-count warnings (final 3 iterations)
- FR-4: `/hands-free log` still captures all events in the session log; quiet mode only affects what is printed to the conversation
- FR-5: `/hands-free status` shows `Loop quiet mode: on` when active

## Non-Goals

- Suppressing user-requested output like `/hands-free log` or `/hands-free status`
- Per-announcement granular control (quiet mode is all-or-nothing for routine output)
- Quiet mode outside of loop context

## Technical Considerations

- US-001 adds `### Loop Quiet Mode` after `### Loop Backoff` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Suppressed vs preserved output clearly distinguished
- Session log still captures all events
- Available Persistent Settings table updated
- Even code-fence count, version 2.42.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
