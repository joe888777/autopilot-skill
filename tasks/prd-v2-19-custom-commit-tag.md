[PRD]
# PRD: Hands-Free v2.19.0 — Custom Auto-Commit Tag Prefix

## Overview

The `[ralph #N]` prefix is hardcoded on all auto-commits in loop mode. Some users use a different project prefix convention or want to suppress the tag entirely. This release adds a `Loop tag:` CLAUDE.md directive that allows users to customize the tag format or disable it.

## Goals

- Allow customizing the auto-commit tag prefix via CLAUDE.md
- Support disabling the tag entirely (`Loop tag: off`)
- Bump version to 2.19.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.19.0`
- `grep "\[2.19.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Specify Loop tag directive in CLAUDE.md Override Reference

**Description:** As a skill user, I want to customize the auto-commit tag format so that commit messages match my project's naming conventions.

**Acceptance Criteria:**
- [ ] `Loop tag:` directive added to the "Available Persistent Settings" table in `## CLAUDE.md Override Reference`
- [ ] `- Loop tag: off` disables the tag entirely (commits have no `[...]` prefix from hands-free)
- [ ] `- Loop tag: [loop #N]` uses a custom prefix where `N` is replaced with the iteration number
- [ ] Default behavior when directive is absent: `[ralph #N]` (existing behavior unchanged)
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-002: Document Loop tag behavior in Iteration-Aware Auto-Commits section

**Description:** As a skill user, I want the tag customization described next to where auto-commits are documented.

**Acceptance Criteria:**
- [ ] `### Iteration-Aware Auto-Commits` or equivalent section updated with a note: "Tag format is configurable via `Loop tag:` in CLAUDE.md; defaults to `[ralph #N]`; use `Loop tag: off` to suppress"
- [ ] `grep -c '^```' SKILL.md` returns even number

### US-003: Bump version to 2.19.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the custom tag feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.18.0` to `2.19.0`
- [ ] New `## [2.19.0] — 2026-03-19` section added at top of CHANGELOG.md
- [ ] CHANGELOG entry summarises: Loop tag CLAUDE.md directive, off disables tag, custom prefix with N substitution
- [ ] `grep "^version:" SKILL.md` shows `2.19.0`
- [ ] `grep "\[2.19.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: `Loop tag: off` → commits have no loop tag prefix (message starts with `feat:` / `fix:` directly)
- FR-2: `Loop tag: [loop #N]` → `N` is replaced with the current iteration number; tag prepended to commit message
- FR-3: Default (no directive) → `[ralph #N]` (existing behavior unchanged)
- FR-4: Tag format validation: if the directive value does not contain `N`, use it as-is (e.g., `Loop tag: [auto]` → all commits tagged `[auto] feat: ...`)

## Non-Goals

- Changing the tag format mid-session without CLAUDE.md
- Validating that the tag format is valid git commit message syntax
- Supporting per-story or per-batch tag customization

## Technical Considerations

- US-001 adds a row to the "Available Persistent Settings" table in `## CLAUDE.md Override Reference`
- US-002 finds the auto-commit tag specification in `## Ralph Loop Integration` and adds the note
- US-003 modifies frontmatter and CHANGELOG.md

## Success Metrics

- Loop tag directive documented in Available Persistent Settings table
- Auto-commit section notes configurability
- Even code-fence count, version 2.19.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
