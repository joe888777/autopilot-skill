[PRD]
# PRD: Hands-Free v2.11.0 — Branch Safety Guard

## Overview

In loop mode, there is nothing preventing the loop from committing and pushing directly to `main`/`master`. This is a significant risk: an unreviewed loop run could land untested or partially-complete work on the default branch. This release adds a branch safety guard: in loop mode, warn when on main/master, auto-create a feature branch (derived from the active epic or plan), and block `git push` to protected branches unless explicitly overridden via CLAUDE.md.

## Goals

- Detect when the loop is operating on a protected branch (main, master, trunk, develop) and warn
- Auto-create a feature branch (derived from active epic title or plan name) when on a protected branch
- Block `git push` to protected branches in loop mode (can be overridden via CLAUDE.md)
- Add `branch` field to the iteration checkpoint
- Bump version to 2.11.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.11.0`
- `grep "\[2.11.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify branch safety guard in loop mode

**Description:** As a skill user, I want hands-free to detect when the loop is on a protected branch and auto-create a feature branch so that loop work never lands directly on main without review.

**Acceptance Criteria:**
- [ ] New `### Branch Safety Guard` sub-section added under `## Ralph Loop Integration`
- [ ] Protected branch list: `main`, `master`, `trunk`, `develop`, `production`, `release` (configurable via CLAUDE.md)
- [ ] At start of iteration pre-flight: check if current branch is protected
- [ ] If on protected branch: announce `[hands-free] On protected branch '<name>' — creating feature branch`; auto-create branch named `ralph/loop-<date>-<N>` (e.g., `ralph/loop-20260319-1`)
- [ ] If auto-branch creation succeeds: announce `[hands-free] Switched to ralph/loop-20260319-1`; continue iteration on new branch
- [ ] `git push` to protected branches blocked in loop mode with announcement: `[hands-free] Push to protected branch '<name>' blocked in loop mode. Merge via PR after review.`
- [ ] Override via CLAUDE.md: `- Loop protected branches: main, master` (explicit list)
- [ ] Override to disable guard: `- Loop branch guard: off`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add `branch` field to iteration checkpoint

**Description:** As a skill user, I want the checkpoint to record the branch being worked on so that context is preserved across iterations.

**Acceptance Criteria:**
- [ ] `branch` field added to the checkpoint JSON schema in `### Iteration Context Checkpoint`
- [ ] `branch` value: current git branch name at time of checkpoint write
- [ ] Checkpoint annotation: if `branch` differs from previous iteration (branch changed), note in iteration announcement
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.11.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the branch safety guard feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.10.0` to `2.11.0`
- [ ] New `## [2.11.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] `grep "^version:" SKILL.md` shows `2.11.0`
- [ ] `grep "\[2.11.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: Branch check runs as part of the pre-flight sequence (before build health check)
- FR-2: Auto-branch name format: `ralph/loop-YYYYMMDD-N` where N increments if the name already exists
- FR-3: Branch creation uses `git checkout -b` (auto-pass command in all modes)
- FR-4: Protected branch push block applies only in loop mode; outside loop mode it follows normal mode rules
- FR-5: Adding a `loop_branch_guard: off` CLAUDE.md directive disables both the branch check and the push block

## Non-Goals

- Managing branch lifecycle (deletion, merging) — that's the user's responsibility
- Detecting branch protection rules from the remote (e.g., GitHub branch protection API)
- Auto-merging feature branches

## Technical Considerations

- US-001 adds sub-section under `## Ralph Loop Integration`, and also adds pre-flight table row
- US-002 modifies the `### Iteration Context Checkpoint` JSON schema example
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Protected branch detection and auto-branch creation specified
- Push block for protected branches in loop mode
- `branch` field in checkpoint schema
- Even code-fence count, version 2.11.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
