[PRD]
# PRD: Hands-Free v2.13.0 — Loop Health Score

## Overview

The loop currently emits pass/fail signals but never synthesises them into a single summary health signal. This release adds a composite Loop Health Score (0-100) computed from four pillars: Test Health, Security Health, Velocity Health, and Commit Health. The score is stored in the checkpoint, displayed in the iteration announcement, and surfaced in `/hands-free status`.

## Goals

- Compute a 0-100 health score from four measurable pillars each iteration
- Store the score in the checkpoint as `health_score`
- Display the score in the iteration start announcement
- Show the score in `/hands-free status`
- Surface the score in DEGRADED pre-flight state output
- Bump version to 2.13.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.13.0`
- `grep "\[2.13.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify health score computation and checkpoint storage

**Description:** As a skill user, I want the checkpoint to record a health score each iteration so that I can track overall loop health at a glance.

**Acceptance Criteria:**
- [ ] New `### Loop Health Score` sub-section added under `## Ralph Loop Integration`
- [ ] Four pillars with exact scoring: Test (failed==0→100, proportional otherwise, null→50), Security (A=100,B=80,C=60,D=40,F=0,null=50), Velocity (0 zeros in last 3→100, 1→60, 2→30, 3/stall→0, insufficient data→100), Commit (commits>0→100, 0→50)
- [ ] Overall = integer average of four pillars, rounded to nearest int
- [ ] `health_score` field added to checkpoint JSON schema
- [ ] Field definitions table updated with `health_score` row
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Display health score in iteration announcement and `/hands-free status`

**Description:** As a skill user, I want to see the health score in the iteration announcement and status output so that I can monitor loop health without running separate commands.

**Acceptance Criteria:**
- [ ] Iteration announcement updated: add `health     : 87/100 (T:100 S:80 V:100 C:80)` line after `security` line
- [ ] If no prior health score: omit health line from announcement
- [ ] `/hands-free status` updated: add `Loop health: 87/100 (T:100 S:80 V:100 C:80)` line; show `Loop health: — (no data)` if no checkpoint
- [ ] Format: `N/100 (T:T_score S:S_score V:V_score C:C_score)`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Surface health score in DEGRADED pre-flight and version bump

**Description:** As a skill user, I want the DEGRADED pre-flight state to include the health score, and the version and changelog updated.

**Acceptance Criteria:**
- [ ] DEGRADED pre-flight announcements append `[Health: N/100]`
- [ ] Example: `[hands-free] DEGRADED — build failed. Routing to systematic-debugging. [Health: 35/100]`
- [ ] `version:` field changed from `2.12.0` to `2.13.0`
- [ ] New `## [2.13.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] `grep "^version:" SKILL.md` shows `2.13.0`
- [ ] `grep "\[2.13.0\]" CHANGELOG.md` matches
- [ ] `grep -c '^```' SKILL.md` returns even number

## Functional Requirements

- FR-1: Health score computed at checkpoint write time using checkpoint data
- FR-2: Integer arithmetic; pillar scores are ints 0-100; average rounded to nearest int
- FR-3: Missing data contributes 50 (neutral) to avoid penalising fresh loops
- FR-4: Score stored in `health_score` reflects end-of-iteration state; displayed in next iteration's announcement
- FR-5: Score is informational only; never used to auto-abort the loop

## Non-Goals

- Historical health score trend
- Configurable pillar weights
- Emoji or colour-coding
- Auto-abort on low health score

## Technical Considerations

- US-001 adds `### Loop Health Score` section and updates checkpoint JSON schema
- US-002 modifies iteration announcement format and `/hands-free status` output
- US-003 modifies pre-flight DEGRADED announcements, frontmatter, and CHANGELOG.md

## Success Metrics

- Four-pillar scoring rules fully specified with formulas
- `health_score` in checkpoint schema
- Iteration announcement and status show health
- Even code-fence count, version 2.13.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
