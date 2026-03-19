[PRD]
# PRD: Hands-Free v2.10.0 — PR Auto-Description

## Overview

When the `finishing-a-development-branch` phase is reached in loop mode, hands-free has all the data needed to compose a PR description: completed stories from the checkpoint, test summary, security grade, and commit count. This release adds a PR auto-description generator that emits a ready-to-paste PR title and body as part of the finishing-branch phase output, and a `/hands-free pr-description` command for on-demand generation.

## Goals

- Auto-generate a PR title and body when the finishing-branch phase is reached in loop mode
- Include: completed stories, test summary, security grade, iteration count, commit list
- Provide `/hands-free pr-description` for on-demand generation outside the loop
- Bump version to 2.10.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.10.0`
- `grep "\[2.10.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify PR auto-description in finishing-branch phase

**Description:** As a skill user, I want hands-free to emit a ready-to-paste PR title and body when the finishing-branch phase is reached so that creating the PR requires minimal effort.

**Acceptance Criteria:**
- [ ] New `### PR Auto-Description` sub-section added under `## Ralph Loop Integration`
- [ ] Specifies that when `finishing-a-development-branch` phase starts in loop mode, generate PR description using checkpoint data
- [ ] PR title format: derived from the epic title or last meaningful commit message (strip `[ralph #N]` prefix)
- [ ] PR body sections: Summary (completed stories list), Test Results (passed/failed from checkpoint), Security (grade from posture), Iteration Stats (N iterations, M commits), Auto-generated footer
- [ ] Output is announced as a code block the user can copy
- [ ] If no checkpoint available: emit minimal PR body with just the commit list from `git log --oneline origin/main..HEAD`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add `/hands-free pr-description` command

**Description:** As a skill user, I want to trigger PR description generation on demand, outside the finishing-branch flow.

**Acceptance Criteria:**
- [ ] `/hands-free pr-description` added to Commands block in SKILL.md
- [ ] `/hands-free pr-description` added to Quick Reference table in SKILL.md
- [ ] New `## /hands-free pr-description` section documenting behavior
- [ ] Command reads checkpoint and git log, generates the same PR body as the finishing-branch auto-generation
- [ ] If no commits since origin/main: announce `[pr-description] No new commits since origin/main — nothing to describe`
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.10.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the PR auto-description feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.9.0` to `2.10.0`
- [ ] New `## [2.10.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: PR auto-description in finishing-branch, `/hands-free pr-description` command
- [ ] `grep "^version:" SKILL.md` shows `2.10.0`
- [ ] `grep "\[2.10.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: PR title strips `[ralph #N]` prefix and `feat:`/`fix:` conventional commit prefix from the source string
- FR-2: PR body uses a standard markdown structure with H2 section headings
- FR-3: Story list comes from `completed_stories` in checkpoint; if empty, use "See commit history"
- FR-4: Test results come from checkpoint `test_summary`; if unavailable, omit section
- FR-5: Security grade comes from `.claude/security-posture.json`; if unavailable, omit section
- FR-6: `/hands-free pr-description` works in all modes

## Non-Goals

- Actually creating the PR (that's a `gh pr create` command requiring explicit user action)
- Tracking PR review status
- Pushing the branch (still a HARD STOP in non-crazy-workspace modes)

## Technical Considerations

- US-001 adds sub-section under `## Ralph Loop Integration`
- US-002 adds Commands entry, Quick Reference entry, and `## /hands-free pr-description` section after `## /hands-free test-baseline`
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- PR description auto-generated in finishing-branch phase with all data sources used
- `/hands-free pr-description` produces same output on demand
- Even code-fence count, version 2.10.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
