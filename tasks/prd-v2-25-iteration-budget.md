[PRD]
# PRD: Hands-Free v2.25.0 — Iteration Time Budget

## Overview

Long-running iterations can stall a loop for extended periods without any signal to the user. This release adds an optional iteration time budget: when an iteration runs longer than the configured number of minutes, hands-free announces a warning suggesting the user consider `/hands-free loop-skip` to abandon the slow iteration. The budget is configured via `Loop iteration budget: <minutes>` in CLAUDE.md.

## Goals

- Add `Loop iteration budget: <minutes>` CLAUDE.md directive
- When an iteration exceeds the budget, announce a time budget warning
- Document the warning format and suggested response
- Add the directive to the Available Persistent Settings table
- Bump version to 2.25.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.25.0`
- `grep "\[2.25.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document iteration time budget behavior in Ralph Loop Integration

**Description:** As a skill user, I want hands-free to warn me when an iteration is running too long so I can decide whether to skip it.

**Acceptance Criteria:**
- [ ] New `### Iteration Time Budget` sub-section added under `## Ralph Loop Integration` (after `### Loop Branch Guard`)
- [ ] Section describes: when `Loop iteration budget: N` is set in CLAUDE.md, hands-free tracks elapsed time since iteration start; if N minutes elapse without the iteration completing, announce:
  `[hands-free] Warning: iteration <N> has run for <X> minutes (budget: <N> min) — consider /hands-free loop-skip to advance or wait for natural completion`
- [ ] Warning fires once per iteration (not repeatedly) when the budget is exceeded
- [ ] Default: no budget configured (`Loop iteration budget:` absent → no time tracking)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for iteration budget

**Description:** As a skill user, I want to configure the iteration time budget in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop iteration budget: N` row added to Available Persistent Settings table in `## CLAUDE.md Override Reference`
- [ ] Effect: warns when an iteration exceeds N minutes; absent by default (no budget)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.25.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the iteration budget feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.24.0` to `2.25.0`
- [ ] New `## [2.25.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: iteration time budget directive, once-per-iteration warning, suggested use of loop-skip
- [ ] `grep "^version:" SKILL.md` shows `2.25.0`
- [ ] `grep "\[2.25.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop iteration budget: N` enables budget tracking; N is a positive integer (minutes)
- FR-2: Budget tracking starts when an iteration begins (loop-aware iteration start announcement)
- FR-3: Warning fires exactly once per iteration when budget is exceeded
- FR-4: The warning does not stop the iteration — it is advisory only
- FR-5: If `Loop iteration budget:` is absent from CLAUDE.md, no time tracking occurs

## Non-Goals

- Auto-skipping iterations that exceed the budget (the user must act)
- Sub-minute budget precision
- Persisting iteration timing data across sessions

## Technical Considerations

- US-001 adds `### Iteration Time Budget` after `### Loop Branch Guard` in `## Ralph Loop Integration`
- US-002 adds one row to the Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Iteration time budget directive documented with warning format
- Once-per-iteration firing behavior specified
- Advisory-only nature clear
- Available Persistent Settings table updated
- Even code-fence count, version 2.25.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
