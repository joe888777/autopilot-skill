[PRD]
# PRD: Hands-Free v2.52.0 — Loop Dry Run

## Overview

When experimenting with a new loop configuration or testing a loop prompt against a codebase, it's useful to see what changes the loop would make without permanently committing anything. This release adds a `Loop dry run: on` directive that executes all planning, editing, and test-running steps but skips auto-commit and auto-push at the end of each iteration. At the end of each iteration, a summary of what would have been committed is printed instead.

## Goals

- Add `Loop dry run: on` CLAUDE.md directive
- When set, execute all loop work (planning, editing, testing) but skip auto-commit and auto-push
- At the end of each iteration, print a dry-run summary instead of committing
- Add directive to Available Persistent Settings table
- Bump version to 2.52.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.52.0`
- `grep "\[2.52.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop dry run in Ralph Loop Integration

**Description:** As a skill user, I want to preview what the loop would commit without actually changing git history so I can validate the loop configuration before enabling commits.

**Acceptance Criteria:**
- [ ] New `### Loop Dry Run` sub-section added under `## Ralph Loop Integration` (after `### Loop Post-Iteration Hook`, before `### What Hands-Free Does NOT Do in Loop Mode`)
- [ ] Documents that all editing, planning, and test-running steps execute normally
- [ ] Documents that auto-commit is skipped and replaced with a dry-run summary
- [ ] Dry-run summary format documented: `[hands-free] Dry run: would commit N files — <list of changed files>`
- [ ] Documents that auto-push is skipped (nothing to push without a commit)
- [ ] Documents that working tree changes remain staged (not committed or reverted)
- [ ] Documents that the completion promise is still evaluated normally
- [ ] If absent, normal auto-commit behavior applies
- [ ] Default: absent (off)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop dry run

**Description:** As a skill user, I want to configure dry run mode in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop dry run: on` row added to Available Persistent Settings table
- [ ] Effect description notes skipped auto-commit/push, dry-run summary, and that changes remain staged
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.52.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the dry run feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.51.0` to `2.52.0`
- [ ] New `## [2.52.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: dry run mode, skipped auto-commit/push, dry-run summary, staged changes retained
- [ ] `grep "^version:" SKILL.md` shows `2.52.0`
- [ ] `grep "\[2.52.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop dry run: on` causes auto-commit to be skipped at the end of each iteration
- FR-2: Auto-push is also skipped (there is nothing to push without a commit)
- FR-3: At the end of each iteration, instead of committing, print: `[hands-free] Dry run: would commit N files — <list of changed files from git status --short>`
- FR-4: Working tree changes remain staged (not committed, not reverted)
- FR-5: All editing, planning, test-running, and hook steps execute normally
- FR-6: The completion promise is evaluated normally
- FR-7: If absent, normal auto-commit/auto-push behavior applies

## Non-Goals

- Reverting staged changes at the end of dry run iterations
- Running tests but skipping edits (dry run applies only to git operations)
- Per-iteration dry run toggle (single on/off setting)

## Technical Considerations

- US-001 adds `### Loop Dry Run` after `### Loop Post-Iteration Hook` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- What executes vs what is skipped clearly documented
- Dry-run summary format documented
- Staged-but-not-committed state documented
- Completion promise evaluation behavior documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.52.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
