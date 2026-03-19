[PRD]
# PRD: Hands-Free v2.21.0 — Iteration Timestamp Logging

## Overview

The session log records what happened but not when. In long loop runs it can be difficult to tell which events occurred at what time of day, making post-run analysis harder. This release adds optional wall-clock timestamp prefixes to session log entries when `Loop timestamps: on` is set in CLAUDE.md.

## Goals

- Add `Loop timestamps: on/off` CLAUDE.md directive
- When enabled, prefix session log entries with `[HH:MM]` (24h local time)
- Document updated session log format in `## Session Log` section
- Add `Loop timestamps:` row to Available Persistent Settings table
- Bump version to 2.21.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.21.0`
- `grep "\[2.21.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify Loop timestamps directive in CLAUDE.md Override Reference

**Description:** As a skill user, I want to enable timestamp prefixes on session log entries so that I can review the timing of loop events after a run.

**Acceptance Criteria:**
- [ ] `Loop timestamps: on/off` row added to Available Persistent Settings table in `## CLAUDE.md Override Reference`
- [ ] Effect: when `on`, all session log entries are prefixed with `[HH:MM]` in 24h local time; default is `off`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Update session log format with timestamp example

**Description:** As a skill user, I want the session log documentation to show what timestamped entries look like.

**Acceptance Criteria:**
- [ ] The session log example in `## Session Log` section updated to show a timestamped variant (either as an additional example or a note below the existing example)
- [ ] Timestamped format shown: `[14:23] [auto-commit] feat: add validation (3 files)`, `[14:31] [review-checkpoint] writing-plans → executing-plans — user chose [C] Continue`
- [ ] Note added: timestamps use 24h local time; when `Loop timestamps: off` (default) entries appear without prefix as before
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.21.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the timestamp logging feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.20.0` to `2.21.0`
- [ ] New `## [2.21.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: Loop timestamps directive, timestamped session log format, default off
- [ ] `grep "^version:" SKILL.md` shows `2.21.0`
- [ ] `grep "\[2.21.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop timestamps: on` enables `[HH:MM]` prefix on all session log entries
- FR-2: `Loop timestamps: off` (default) leaves session log entries unchanged
- FR-3: Timestamps use 24h format, local system time, hours and minutes only (no seconds)
- FR-4: Prefix format: `[HH:MM] ` (bracket, time, bracket, space) prepended to the existing entry text
- FR-5: Timestamps apply to all event types: brainstorming, writing-plans, executing-plans, auto-commit, review-checkpoint, hard-stop, user-override, auto-stop

## Non-Goals

- Persisting timestamps to disk across sessions
- Sub-minute precision (seconds)
- Timezone conversion — always system local time

## Technical Considerations

- US-001 adds one row to the Available Persistent Settings table after `Loop failure repeat: N`
- US-002 updates the `## Session Log` section by appending a timestamped example block or note
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- `Loop timestamps:` directive documented with clear on/off behavior
- Session log example updated with `[HH:MM]` prefix demonstration
- Even code-fence count, version 2.21.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
