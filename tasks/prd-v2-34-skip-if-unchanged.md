[PRD]
# PRD: Hands-Free v2.34.0 — Loop Skip-if-Unchanged

## Overview

When a ralph-loop iteration makes no changes to the working tree, subsequent iterations will often repeat the same no-op work. This release adds a `Loop skip-if-unchanged: on` directive that detects a clean working tree at the start of each iteration and automatically skips it, advancing to the next without wasted compute.

## Goals

- Add `Loop skip-if-unchanged: on/off` CLAUDE.md directive
- At iteration start, check git status; if clean, skip the iteration and advance
- Document the "clean" definition and suggest combining with stall detection for coverage
- Add directive to Available Persistent Settings table
- Bump version to 2.34.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.34.0`
- `grep "\[2.34.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop skip-if-unchanged in Ralph Loop Integration

**Description:** As a skill user, I want the loop to skip iterations where the working tree is already clean to avoid redundant work.

**Acceptance Criteria:**
- [ ] New `### Loop Skip-if-Unchanged` sub-section added under `## Ralph Loop Integration` (after `### Loop On-Complete Hook`)
- [ ] Check runs at the start of each iteration (before any work)
- [ ] If working tree is clean: announce `[hands-free] Skip-if-unchanged: working tree clean — skipping iteration N, advancing to next` and advance
- [ ] "Clean" defined: git status reports no modified, staged, or untracked files (ignoring .gitignore'd files)
- [ ] Note: combine with Loop max failures or stall detection for comprehensive coverage
- [ ] Default: off
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for skip-if-unchanged

**Description:** As a skill user, I want to configure skip-if-unchanged in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop skip-if-unchanged: on/off` row added to Available Persistent Settings table
- [ ] Effect description notes the "clean working tree" detection
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.34.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the skip-if-unchanged feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.33.0` to `2.34.0`
- [ ] New `## [2.34.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: skip-if-unchanged directive, clean definition, stall-detection note
- [ ] `grep "^version:" SKILL.md` shows `2.34.0`
- [ ] `grep "\[2.34.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop skip-if-unchanged: on` enables working-tree check at each iteration start
- FR-2: If git status reports no modified/staged/untracked files, skip the iteration
- FR-3: Announce the skip before advancing
- FR-4: The skipped iteration is logged in the session log
- FR-5: The check runs before any brainstorming, planning, or execution

## Non-Goals

- Checking only specific files (entire working tree is checked)
- Pausing for user confirmation on clean-skip (auto-skips without asking)
- Comparing to a specific baseline commit (uses current working tree only)

## Technical Considerations

- US-001 adds `### Loop Skip-if-Unchanged` after `### Loop On-Complete Hook` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Skip-if-unchanged documented with clean definition
- Auto-skip behavior clear (no user confirmation needed)
- Stall-detection combination suggested
- Available Persistent Settings table updated
- Even code-fence count, version 2.34.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
