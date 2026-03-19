[PRD]
# PRD: Hands-Free v2.53.0 — Loop Commit Message Template

## Overview

Auto-commits in loop mode use a hardcoded `[ralph #N] feat: <description>` format. While `Loop commit prefix` (v2.45.0) allows customising the tag, the commit type (`feat:`) and message body format are always inferred. This release adds a `Loop commit template: <template>` directive that lets users specify the full commit message format with placeholders.

## Goals

- Add `Loop commit template: <template>` CLAUDE.md directive
- Support placeholders: `{N}` (iteration number), `{type}` (inferred conventional commit type), `{msg}` (inferred commit description), `{files}` (comma-separated list of changed files)
- When set, render the template to produce the full commit message
- Add directive to Available Persistent Settings table
- Bump version to 2.53.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.53.0`
- `grep "\[2.53.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document Loop commit template in Ralph Loop Integration

**Description:** As a skill user, I want to configure the full format of auto-commit messages in loop mode.

**Acceptance Criteria:**
- [ ] New `### Loop Commit Template` sub-section added under `## Ralph Loop Integration` (after `### Loop Commit Prefix`, before `### Loop Max Changes`)
- [ ] Documents supported placeholders: `{N}` (iteration number), `{type}` (inferred type e.g. `feat`), `{msg}` (inferred description), `{files}` (comma-separated changed filenames)
- [ ] Documents that if `Loop commit template` is set, `Loop commit prefix` is ignored
- [ ] Example: `Loop commit template: [iter-{N}] {type}: {msg}` → `[iter-3] feat: add validation`
- [ ] Example: `Loop commit template: chore({files}): iteration {N}` → `chore(main.rs,lib.rs): iteration 5`
- [ ] Documents that `{files}` is truncated to the first 3 filenames followed by `...` if more than 3 files changed
- [ ] Default: absent — `Loop commit prefix` behavior (or default `[ralph #N]`) applies
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Add CLAUDE.md override for loop commit template

**Description:** As a skill user, I want to configure the commit template in CLAUDE.md.

**Acceptance Criteria:**
- [ ] `Loop commit template: <template>` row added to Available Persistent Settings table
- [ ] Effect description notes placeholders and that it overrides `Loop commit prefix` when set
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.53.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the commit template feature.

**Acceptance Criteria:**
- [ ] `version:` field changed from `2.52.0` to `2.53.0`
- [ ] New `## [2.53.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: commit template directive, placeholders, overrides prefix
- [ ] `grep "^version:" SKILL.md` shows `2.53.0`
- [ ] `grep "\[2.53.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop commit template: <template>` specifies the full commit message format for auto-commits in loop mode
- FR-2: Placeholders: `{N}` → iteration number; `{type}` → inferred conventional commit type (feat/fix/refactor/test/docs/chore); `{msg}` → inferred commit description (same as current inference); `{files}` → comma-separated list of changed files (first 3, then `...` if more)
- FR-3: When `Loop commit template` is set, `Loop commit prefix` is ignored
- FR-4: If a placeholder is absent from the template, it is simply not rendered (no error)
- FR-5: If absent, default behavior (`Loop commit prefix` or `[ralph #N]`) applies

## Non-Goals

- Custom commit body (only subject line)
- Per-file templates
- Validation of placeholder syntax

## Technical Considerations

- US-001 adds `### Loop Commit Template` after `### Loop Commit Prefix` in `## Ralph Loop Integration`
- US-002 adds one row to Available Persistent Settings table
- US-003 modifies frontmatter and CHANGELOG.md
- No new code fences needed

## Success Metrics

- Placeholders documented with examples
- Override relationship with Loop commit prefix documented
- {files} truncation documented
- Available Persistent Settings table updated
- Even code-fence count, version 2.53.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
