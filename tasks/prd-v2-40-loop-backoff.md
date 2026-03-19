[PRD]
# PRD: Hands-Free v2.40.0 — Loop Backoff

## Overview

When a loop repeatedly encounters failures, retrying immediately with the same approach rarely helps. This release adds a `Loop backoff: on` directive that automatically increases the cooldown between iterations when consecutive failures are detected, giving the system time before trying again.

## Goals

- Add `Loop backoff: on/off` CLAUDE.md directive
- When enabled, double the effective wait between iterations after each consecutive failure (starting at 30s, capped at 5 minutes)
- Reset backoff to zero after any iteration that passes tests
- Add directive to Available Persistent Settings table
- Bump version to 2.40.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.40.0`
- `grep "\[2.40.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop backoff in Ralph Loop Integration

**Description:** As a skill user, I want automatic increasing delays when failures repeat so the loop doesn't hammer the same broken path.

**Acceptance Criteria:**
- [ ] New `### Loop Backoff` sub-section added under `## Ralph Loop Integration` (after `### Loop Session Stats`)
- [ ] Backoff sequence documented: 0s → 30s → 60s → 120s → 240s → 300s (cap)
- [ ] Announce when backoff is applied: `[hands-free] Backoff: waiting <X>s after <N> consecutive failures`
- [ ] Backoff resets to 0s after any iteration where all tests pass
- [ ] Works alongside `Loop cooldown: N` — effective wait = max(cooldown, backoff)
- [ ] Default: off
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop backoff

**Description:** As a skill user, I want to configure backoff in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop backoff: on/off` row added to Available Persistent Settings table
- [ ] Effect description notes the doubling sequence and reset condition
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.40.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the backoff feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.39.0` to `2.40.0`
- [ ] New `## [2.40.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: backoff directive, doubling sequence, reset condition, cooldown interaction
- [ ] `grep "^version:" SKILL.md` shows `2.40.0`
- [ ] `grep "\[2.40.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop backoff: on` enables exponential backoff on consecutive failures
- FR-2: Backoff sequence: 0s after pass, 30s after 1st failure, 60s after 2nd, 120s after 3rd, 240s after 4th, 300s cap
- FR-3: Backoff resets to 0s after any iteration where all tests pass
- FR-4: Effective wait = max(Loop cooldown value, current backoff value)
- FR-5: Announce the wait when backoff is applied

## Non-Goals

- Configurable backoff sequence (the doubling sequence is fixed)
- Backoff based on error type rather than failure count
- Jitter (randomized backoff variation)

## Technical Considerations

- US-001 adds `### Loop Backoff` after `### Loop Session Stats` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Backoff sequence documented explicitly
- Reset condition clear
- Cooldown interaction documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.40.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
