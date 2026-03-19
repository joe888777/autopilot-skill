[PRD]
# PRD: Hands-Free v2.41.0 — Loop Auto-Push (Revised)

## Overview

The existing `Loop auto-push: on` directive (v2.30.0) silently continues to the next iteration when `git push` fails. This makes push failures invisible in long-running loops, leaving the remote out of sync without the operator knowing. This release revises auto-push to treat push failures as a HARD STOP, adds a pre-push announcement with commit count, and separates the success confirmation into its own message so operators always know exactly when and whether the push landed.

## Goals

- Upgrade push failure handling from silent-continue to HARD STOP so operators never miss a failed push
- Add a pre-push announcement (`pushing N commit(s)`) so the operator sees intent before execution
- Add a distinct success announcement (`pushed successfully`) so the operator sees confirmation after execution
- Bump version to 2.41.0 with CHANGELOG entry

## Quality Gates

These commands must pass for every user story:
- `grep -c '^```' SKILL.md` must return an even number
- `grep "^version:" SKILL.md` must show `2.41.0`
- `grep "\[2.41.0\]" CHANGELOG.md` must match

## User Stories

### US-001: Document loop auto-push behavior in Ralph Loop Integration section

**Description:** As a skill user, I want push failures to halt the loop so I can investigate and fix the issue before more iterations pile up locally without reaching the remote.

**Acceptance Criteria:**
- [ ] The existing `### Loop Auto-Push` sub-section (after `### Loop Backoff`) is updated in-place; no new sub-section is created
- [ ] The pre-push announcement is documented: `[hands-free] Auto-push: pushing N commit(s) to origin/<branch>`
- [ ] The success announcement is documented: `[hands-free] Auto-push: pushed successfully`
- [ ] Push failure behavior is changed from "announce error and continue" to HARD STOP: `[hands-free] HARD STOP — Auto-push failed: <error>. Resolve the issue and run /hands-free resume to continue.`
- [ ] The existing skip conditions remain unchanged: loop-skip, HARD STOP (from other causes), no new commits, `Auto-commit: off`
- [ ] The dependency on `Auto-commit: on` remains documented
- [ ] Default remains: off
- [ ] No new code fences are added or removed (code fence count parity preserved)
- [ ] `grep -c '^```' SKILL.md` returns an even number

### US-002: Add `Loop auto-push: on/off` row to Available Persistent Settings table

**Description:** As a skill user, I want the persistent settings table to reflect the revised push failure behavior so the HARD STOP semantics are visible at a glance.

**Acceptance Criteria:**
- [ ] The existing `Loop auto-push: on/off` row in the Available Persistent Settings table is updated in-place
- [ ] The Effect column reflects the new behavior: push failure triggers HARD STOP (not silent continue)
- [ ] The dependency on `Auto-commit: on` remains noted in the Effect column
- [ ] The skip conditions remain noted: loop-skip, HARD STOP, no new commits
- [ ] `grep -c '^```' SKILL.md` returns an even number

### US-003: Bump version to 2.41.0 and update CHANGELOG

**Description:** As a skill user, I want the version and changelog to reflect the revised auto-push feature.

**Acceptance Criteria:**
- [ ] `version:` field in SKILL.md frontmatter changed from `2.40.0` to `2.41.0`
- [ ] New `## [2.41.0] — 2026-03-19` section added at top of CHANGELOG.md (above `## [2.40.0]`)
- [ ] CHANGELOG entry summarises: revised auto-push with HARD STOP on push failure, pre-push announcement, success confirmation
- [ ] `grep "^version:" SKILL.md` shows `2.41.0`
- [ ] `grep "\[2.41.0\]" CHANGELOG.md` matches

## Functional Requirements

- FR-1: When `Loop auto-push: on` and `Auto-commit: on`, and new commits were made in the current iteration, run `git push origin <current-branch>` after all auto-commits complete
- FR-2: Before pushing, announce: `[hands-free] Auto-push: pushing N commit(s) to origin/<branch>`
- FR-3: After a successful push, announce: `[hands-free] Auto-push: pushed successfully`
- FR-4: If `git push` fails (rejected, no remote, auth failure, network error), fire a HARD STOP: `[hands-free] HARD STOP — Auto-push failed: <error>. Resolve the issue and run /hands-free resume to continue.`
- FR-5: Push is skipped (silently) when: iteration was skipped via loop-skip, iteration ended with HARD STOP from another cause, no new commits were made, or `Auto-commit: off`
- FR-6: `Auto-commit: off` silently disables auto-push regardless of the `Loop auto-push` setting

## Non-Goals

- Force-pushing or rebase-and-push (only plain `git push`)
- Per-file push scoping
- Push to non-origin remotes
- Configurable retry count on push failure (fail once, HARD STOP)

## Technical Considerations

- US-001 modifies the existing `### Loop Auto-Push` section in `## Ralph Loop Integration` (currently at ~line 4334 in SKILL.md); this is an in-place edit, not a new section
- US-002 modifies the existing `Loop auto-push: on/off` row in the Available Persistent Settings table (currently at ~line 4860 in SKILL.md)
- US-003 modifies the frontmatter `version:` field and prepends a new section to CHANGELOG.md
- No new code fences are added or removed; the code fence count parity must be preserved
- The `### What Hands-Free Does NOT Do in Loop Mode` section (line ~4498) references push behavior; verify no conflict with the updated HARD STOP semantics (it says "Does NOT auto-accept git push in full/partial/off modes" which is about the classification, not about auto-push, so no update needed)

## Success Metrics

- Push failure handling upgraded from silent-continue to HARD STOP
- Pre-push and post-push announcements both documented
- Available Persistent Settings table updated to reflect HARD STOP semantics
- Even code-fence count, version 2.41.0, CHANGELOG entry

## Open Questions

- None
[/PRD]
