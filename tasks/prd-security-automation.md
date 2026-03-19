[PRD]
# PRD: Security Automation Toolkit for Hands-Free Skill

## Overview

Extend the hands-free Claude Code skill with a comprehensive security automation toolkit. The toolkit provides automated vulnerability scanning, secrets detection, dependency auditing, and security posture reporting — all integrated into the hands-free workflow so security checks run automatically without user intervention.

The problem: developers often skip manual security checks under time pressure. By embedding security automation into the hands-free loop, every iteration of code generation is automatically validated against security standards before being committed.

## Goals

- Automatically run security scans (trivy, bandit, semgrep, pip-audit, cargo-audit) as part of the hands-free auto-commit workflow
- Generate a security posture report after each development session
- Block auto-commit when critical vulnerabilities are found (configurable thresholds)
- Integrate dependency vulnerability checks into the loop
- Provide a `/hands-free security` command to view current security posture
- Support CLAUDE.md overrides for security scan configurations per project

## Quality Gates

These commands must pass for every user story:
- Shell validation: `shellcheck` on any new shell scripts
- SKILL.md integrity: verify the SKILL.md file is valid markdown

## User Stories

### US-001: Automated Pre-Commit Security Scanning
**Description:** As a developer using hands-free auto-commit, I want security scans to run automatically before each commit so that vulnerabilities are caught before they enter version history.

**Acceptance Criteria:**
- [ ] Before auto-commit, run applicable security scanners based on project type detection
- [ ] If `Cargo.toml` present → run `cargo audit`
- [ ] If `*.py` files present → run `bandit -r ./src` (or detected source dir)
- [ ] If `package.json` present → run `npm audit`
- [ ] If `requirements.txt` / `pyproject.toml` present → run `pip-audit`
- [ ] If any source files present → run `semgrep --config ./rules/` (local rules only, no remote)
- [ ] Critical severity findings block the auto-commit and announce `[security] Auto-commit blocked — N critical vulnerabilities found`
- [ ] High severity findings warn but do not block (configurable via CLAUDE.md)
- [ ] Results are logged to `.claude/security-scan.log`

### US-002: Secrets Detection Enhancement
**Description:** As a developer, I want enhanced secrets detection that catches more patterns than the basic check so that no credentials are accidentally committed.

**Acceptance Criteria:**
- [ ] Add entropy-based detection for high-entropy strings (>4.5 bits/char, length >20)
- [ ] Add detection for base64-encoded credentials
- [ ] Add AWS credential file patterns (`.aws/credentials` in staged diff)
- [ ] Add detection for private key PEM blocks in any file extension (not just `.pem`)
- [ ] Add `GITHUB_TOKEN`, `GITLAB_TOKEN`, `BITBUCKET_APP_PASSWORD` patterns
- [ ] Add `DATABASE_URL` with embedded password detection
- [ ] False positive whitelist: allow test files, examples, fixtures (existing behavior preserved)
- [ ] New patterns documented in SKILL.md under "Secrets Detection" section

### US-003: Dependency Vulnerability Reporting
**Description:** As a developer, I want a summary of known vulnerabilities in project dependencies after each development session so I can prioritize security fixes.

**Acceptance Criteria:**
- [ ] `/hands-free security` command outputs current vulnerability summary
- [ ] Summary shows: critical count, high count, medium count, low count per scanner
- [ ] Summary includes: affected package name, CVE ID (if available), fixed version
- [ ] Summary is grouped by severity (Critical → High → Medium → Low)
- [ ] Report is also written to `.claude/security-report.md` for reference
- [ ] Command auto-detects which scanners are available (no error if scanner not installed)

### US-004: Security Posture Score
**Description:** As a developer, I want a simple security posture score displayed in the session status so I have immediate visibility into the project's security health.

**Acceptance Criteria:**
- [ ] `/hands-free status` includes a "Security" line: `Security: A (0 critical, 2 high, 5 medium)`
- [ ] Score grades: A (0 critical, <5 high), B (0 critical, 5-15 high), C (1-2 critical), D (3+ critical), F (scan failed/blocked)
- [ ] Score is computed from the last successful scan run
- [ ] If no scan has been run, shows `Security: unknown (run /hands-free security)`
- [ ] Score persists to `.claude/security-posture.json` between sessions

### US-005: CLAUDE.md Security Override Support
**Description:** As a project maintainer, I want to configure security scan thresholds per project via CLAUDE.md so that different projects can have appropriate security policies.

**Acceptance Criteria:**
- [ ] Support `# hands-free security` section in CLAUDE.md
- [ ] `block-on: critical` (default) — only block on critical findings
- [ ] `block-on: high` — block on high and critical findings
- [ ] `block-on: none` — warn only, never block
- [ ] `skip-scanners: cargo-audit, bandit` — disable specific scanners
- [ ] `allow-patterns: <pattern>` — whitelist specific false positive patterns
- [ ] CLAUDE.md security section documented in SKILL.md "CLAUDE.md Override Reference"

### US-006: Loop-Aware Security Integration
**Description:** As a developer running ralph-loop, I want security scans to be integrated into the loop health check so that failed scans are reported as blockers and routed to systematic-debugging.

**Acceptance Criteria:**
- [ ] Loop iteration health check includes security scan status
- [ ] If critical vulnerabilities exist from previous iteration → route to systematic-debugging
- [ ] Iteration start announcement includes security status: `[hands-free] Iteration 3/100 — security: A`
- [ ] Auto-commit in loop mode appends security grade: `[ralph #3] feat: add validation [security: A]`
- [ ] If scan cannot run (scanner not installed), skip gracefully with warning

### US-007: Security Scan Documentation
**Description:** As a user of the hands-free skill, I want comprehensive documentation of the security scanning behavior so I understand what is scanned, when, and how to configure it.

**Acceptance Criteria:**
- [ ] New "## Security Automation" section added to SKILL.md
- [ ] Documents all supported scanners and when they trigger
- [ ] Documents severity thresholds and blocking behavior
- [ ] Documents CLAUDE.md security override syntax with examples
- [ ] Documents `/hands-free security` command output format
- [ ] Troubleshooting entries for common security scan issues added
- [ ] README.md updated to mention security automation feature

## Functional Requirements

- FR-1: Security scans must run within 30 seconds for typical projects (skip slow scanners if budget exceeded)
- FR-2: Security scanning must not break existing auto-commit behavior — it is additive
- FR-3: Scanner output must be parsed into a normalized format (severity, package, description, fix)
- FR-4: All security scan results must be stored in `.claude/` directory (not committed to git)
- FR-5: `.claude/security-scan.log` and `.claude/security-report.md` must be added to `.gitignore` automatically
- FR-6: Security scanning must be skippable via `--no-security` flag equivalent in CLAUDE.md
- FR-7: The system must detect which scanners are installed and skip unavailable ones gracefully
- FR-8: Security scan results must survive across sessions (persisted to `.claude/`)

## Non-Goals

- Building custom vulnerability scanners (use existing tools: trivy, bandit, semgrep, cargo-audit, npm audit)
- Network-based scanning or DAST (dynamic application security testing)
- Container image scanning (trivy image) in the auto-commit flow
- SBOM (Software Bill of Materials) generation
- Compliance reporting (SOC2, PCI-DSS)
- Automatic remediation of vulnerabilities (report only, not fix)

## Technical Considerations

- All scanner invocations follow existing hands-free classification rules (trivy fs → auto-pass, semgrep --config auto → ask)
- Security scan output parsing should handle JSON output formats where available
- `.claude/security-posture.json` schema: `{score, grade, lastScan, findings: [{severity, scanner, package, cve, description, fixedIn}]}`
- Scanner detection: use `which <scanner>` or `command -v <scanner>` pattern
- The security posture feature extends existing `/hands-free status` output — no new command needed for status display

## Success Metrics

- Zero critical vulnerabilities committed through auto-commit in any project using hands-free
- Security scan completes within 30 seconds for 95% of typical project sizes
- False positive rate < 5% for secrets detection
- Security posture score visible in every `/hands-free status` call after first scan

## Open Questions

- Should security scans run on every auto-commit or only at phase transitions (end of batch)?
- Should the security posture score be shown in the ralph-loop status line even when score is unknown?
- What is the right default for `block-on` — should it default to `high` rather than `critical`?
[/PRD]
