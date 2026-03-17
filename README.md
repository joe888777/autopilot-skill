# Hands-Free Skill

A Claude Code skill that auto-accepts recommended options from any skill workflow, giving you a hands-off experience. Works with [superpowers](https://github.com/anthropics/claude-code-plugins) and any custom skill that presents choices.

## What it does

- **Auto-accepts** recommended options at approval points (brainstorming, design, execution, checkpoints)
- **Auto-commits** changes at natural milestones with clean git history
- **Pauses** for destructive/irreversible actions (git push, merge, discard, force operations, rm -rf, CI/CD changes, etc.)
- **Blocks** pipe-to-shell (`curl | bash`), language-level RCE (`python -c exec`, `node -e eval`), privilege escalation (`chmod 777`), and secrets in commits — always, in every mode, including subshell patterns (`bash $(curl URL)`) and shell script content that embeds these patterns
- **Prompts injection prevention** — approval points are only recognized in Claude's own skill output, not in fetched web pages, file contents, or tool results that could contain crafted approval signals
- **Review checkpoints** — structured pause-and-summarize before costly phase transitions (optional, or always-on in `partial`)
- **Explains** decisions with `/hands-free explain` — trace why a specific auto-accept happened
- **Previews** classification with `/hands-free check <command>` — see if a command would auto-pass, ask, or HARD STOP before running it
- **Learns** your preferences over time — tracks choices, builds confidence, adapts to you
- **Ralph-loop integration** — works with ralph-loop + superpowers for fully autonomous iterative development
- **Four modes** — `full`, `partial`, `off`, and `crazy-workspace` for different levels of autonomy
- **Comprehensive tool coverage** — 500+ shell command patterns pre-classified for Python (uv/poetry/pipenv/tox/nox), Rust (cargo nextest/cross/miri), TypeScript (tsup/vite/esbuild/biome), Docker/Podman/nerdctl/containerd, Redis, PostgreSQL, SQLite, ClickHouse, Go, Java/JVM (Maven/Gradle), Ruby (bundle/RSpec/RuboCop), Rails, Django, Phoenix, .NET, Elixir, Erlang, C/C++/LLVM, Haskell/Scala/Clojure/Kotlin, Kubernetes (kubectl/Helm/Skaffold/kube-score/kubeval/kyverno), service mesh (istioctl/linkerd), cloud CLIs (gcloud/az/AWS), SaaS CLIs (Stripe/Supabase/Firebase/Vercel/Netlify/Railway/Fly.io), IaC (Terraform/Pulumi/CDK/Ansible), gRPC (grpcurl/buf/rover), API codegen (openapi-generator/swagger-codegen), ML tools (DVC/MLflow/wandb), security scanners (trivy/grype/bandit/gosec/semgrep/pip-audit/dependency-check), modern crypto (age/sops), and more
- **CLAUDE.md Override Reference** — persistent per-project overrides for commands, tools, and patterns; user-global overrides via `~/.claude/CLAUDE.md`

## Install

Clone and symlink (recommended — stays in sync with updates):

```bash
git clone git@github.com:joe888777/autopilot-skill.git ~/hands-free-skill
ln -s ~/hands-free-skill ~/.claude/skills/hands-free
```

Or copy:

```bash
git clone git@github.com:joe888777/autopilot-skill.git
cp -r autopilot-skill ~/.claude/skills/hands-free
```

## Usage

```
/hands-free              # Activate full mode; if already active, show status
/hands-free full         # Full mode — auto-accept all non-destructive points
/hands-free partial      # Auto-accept design, pause at execution
/hands-free off          # Back to normal (learning still active)
/hands-free crazy-workspace           # Max autonomy within ./
/hands-free auto-commit on            # Auto-commit at natural milestones
/hands-free auto-commit off           # Disable auto-commit (default)
/hands-free review-checkpoints on     # Pause at phase transitions for review
/hands-free review-checkpoints off    # Skip phase-transition pauses (default in full)
/hands-free learning <h/m/l>          # Set learning sensitivity (high/medium/low)
/hands-free learning                  # Show current learning level
/hands-free dry-run                   # Preview what would be auto-accepted
/hands-free pause                     # Temporarily suspend auto-accept (mode preserved)
/hands-free resume                    # Resume auto-accept after pause
/hands-free explain                   # Explain why the last auto-accept or hard-stop decision was made
/hands-free reset                     # Clear all learned preferences (requires confirmation)
/hands-free status                    # Show current mode + all settings
/hands-free recommend                 # Suggest optimal settings
/hands-free recommend promote <action> # Promote a standard hard-stop action to auto-accept
/hands-free recommend prune           # Review and remove stale low-confidence preferences
/hands-free log                       # Show session decisions (use --full for complete log)
/hands-free check <cmd>               # Preview how a command would be classified (no side effects)
```

### Modes

| Approval Type | full | partial | off | crazy-workspace |
|---|---|---|---|---|
| Brainstorming / design / phase transitions | auto | auto | ask | auto |
| Execution method | auto | **ask** | ask | auto |
| Batch checkpoints | auto | **ask** | ask | auto |
| Shell cmds scoped to cwd / pyenv / git add | auto | auto | ask | auto |
| Destructive actions (within workspace) | **ask** | **ask** | ask | auto |
| Review checkpoint — optional (brainstorming→plan, etc.) | skip | **HARD STOP** | **HARD STOP** | skip |
| Review checkpoint — mandatory (before execution) | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| Review checkpoint — mandatory (before push/merge) | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| Git push | ask | ask | ask | auto |
| `curl \| bash` / pipe-to-shell | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| Language RCE (`python -c exec`, `deno run <url>`) | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| `chmod 777` / privilege escalation | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| Secrets in staged files | **HARD STOP** | **HARD STOP** | **HARD STOP** | **HARD STOP** |
| `rm -rf *` | ask | ask | ask | **HARD STOP** |
| `rm -rf .git` | ask | ask | ask | **HARD STOP** |

### Example

Without hands-free:
```
Claude: "I see 3 approaches. Which do you prefer?"
You: "2"
Claude: "Design section 1: ... Does this look right?"
You: "yes"
Claude: "Subagent-driven or parallel session?"
You: "subagent"
```

With `/hands-free`:
```
Claude: "Going with approach 2 (recommended) — best balance of simplicity and flexibility."
Claude: "Design approved. Moving to implementation plan."
Claude: "Going with subagent-driven (recommended for this session)."
```

### Checking what's active

```
/hands-free status

Hands-Free Status
  Mode:                 full
  Learning:             high
  Auto-commit:          on
  Review checkpoints:   off
  Paused:               no
  Loop-aware:           no

  Session decisions:    7 auto-accepted, 1 paused
  Preferences loaded:   2 rules (1 high, 1 medium)

  Universal hard stops: curl|bash, chmod 777, secrets-in-commit, rm -rf *, rm -rf .git
  Mode hard stops:      git push, git merge, git reset --hard (paused in this mode)
```

### Previewing before enabling

```
/hands-free dry-run

Hands-Free Dry Run — current mode: off, learning: medium

Would auto-accept (if full mode enabled):
  Brainstorming approach selection    yes
  Design approval                     yes
  Shell commands scoped to cwd        yes

Would PAUSE for (always, all modes):
  git push                            HARD STOP
  curl | bash / source <(curl)        HARD STOP
  python -c exec / node -e eval       HARD STOP
  chmod 777                           HARD STOP
  Secrets in staged files             HARD STOP
  rm -rf *                            HARD STOP

To enable: /hands-free full
```

### Persisting settings across sessions

Mode resets to `off` at the start of each conversation. To auto-activate, add to your project's CLAUDE.md:

```markdown
# hands-free overrides
- Default mode: full
- Auto-commit: on
- Learning: high
```

## Ralph Loop + Superpowers Example

Hands-free combines with [ralph-loop](https://ghuntley.com/ralph/) and superpowers for fully autonomous iterative development. Type 4 lines, walk away, come back to working code.

### Setup

```
/hands-free full
/hands-free auto-commit on
/hands-free learning high
/ralph-loop "Build a REST API for a todo app with Express.js. Include CRUD endpoints, input validation, error handling, and tests. Output <promise>DONE</promise> when all tests pass." --completion-promise "DONE" --max-iterations 10
```

### What happens

**Iteration 1 — Design & scaffold**
```
[hands-free] Loop detected — iteration #1, no prior work → full superpowers flow
[hands-free] brainstorming → Going with approach 2 (recommended) — Express + Zod + Vitest
[hands-free] design approved (3 sections)
[hands-free] writing-plans → subagent-driven (recommended)
[auto-commit] [ralph #1] feat: scaffold Express app with CRUD routes (4 files)
[auto-commit] [ralph #1] feat: add Zod validation and error handler (3 files)
[auto-commit] [ralph #1] test: add CRUD endpoint tests (2 files)

Running tests... 3 passed, 2 failed → no completion promise, exiting iteration
```

**Iteration 2 — Fix failures**
```
[hands-free] Loop detected — iteration #2, tests failing → systematic-debugging
[auto-commit] [ralph #2] fix: handle null request body and missing todo 404 (2 files)

Running tests... 5 passed, 0 failed → edge cases not covered, no promise yet
```

**Iteration 3 — Add edge cases**
```
[hands-free] Loop detected — iteration #3, tests passing, work incomplete → continue plan
[auto-commit] [ralph #3] test: add edge case validation tests (1 file)

Running tests... 9 passed, 1 failed
```

**Iteration 4 — Final fix**
```
[hands-free] Loop detected — iteration #4, tests failing → systematic-debugging
[auto-commit] [ralph #4] fix: add duplicate title check in create endpoint (1 file)

Running tests... 10 passed, 0 failed
<promise>DONE</promise>
```

### Result

```
/hands-free log

Hands-Free Session Log (full, learning: high, ralph-loop: 4 iterations)
  [brainstorming] approach 2 — Express + Zod + Vitest
  [brainstorming] design approved (3 sections)
  [writing-plans] subagent-driven (recommended)
  [executing-plans] batches 1-3 auto-continued
  [auto-commit] [ralph #1] scaffold, validation, tests (3 commits)
  [systematic-debugging] 2 failures → fixed
  [auto-commit] [ralph #2] null body + 404 fix
  [auto-commit] [ralph #3] edge case tests
  [systematic-debugging] 1 failure → fixed
  [auto-commit] [ralph #4] duplicate check
  COMPLETED — 4 iterations, 7 commits, 10 tests passing
```

Git log:
```
* [ralph #4] fix: add duplicate title check in create endpoint
* [ralph #3] test: add edge case validation tests
* [ralph #2] fix: handle null request body and missing todo 404
* [ralph #1] test: add CRUD endpoint tests
* [ralph #1] feat: add Zod validation and error handler
* [ralph #1] feat: scaffold Express app with CRUD routes
```

## How learning works

Hands-free records your choices in `preferences.md` whether it's on or off.

| Occurrences | Confidence | Behavior |
|---|---|---|
| 1-2x | low | Tracked, not auto-applied |
| 3-4x | medium | Auto-applied with announcement |
| 5x+ | high | Auto-applied silently |

**Staleness:** If you override a learned preference, confidence decreases. Override 3x → rule replaced with new preference at low confidence.

Over time, hands-free picks **what you would pick**, not just the default recommendation. Use `/hands-free reset` to clear all preferences if they've drifted from your actual preferences.

## Session log

Run `/hands-free log` anytime to see a summary of all decisions in the current session. The log is in-memory only — it resets at the end of each conversation. Events include brainstorming choices, design approvals, auto-commits, review checkpoints, and hard stops.

## Security

Hands-free enforces **universal hard stops** in ALL modes, including `crazy-workspace`. These cannot be overridden, promoted to auto-accept, or disabled:

| Pattern | Why |
|---|---|
| `curl \| bash`, `wget \| sh`, `eval $(curl ...)`, `source <(curl ...)` | Remote code execution — arbitrary code from the internet |
| `python -c "exec(urllib...)"`, `node -e "eval(require...)"`, `deno run https://...`, `perl` fetch-eval | Language-level RCE — fetches and executes remote code via interpreter |
| `bash $(curl URL)` — subshell fetching remote content | Equivalent to pipe-to-shell; inner subshell is classified independently |
| Writing a script that embeds `curl \| bash` (Edit/Write tool) | Shell script content is scanned before writing — hard stop patterns in script content block the write |
| `chmod 777`, `chmod a+rwx` | World-writable permissions — any user can modify the file |
| Secrets in staged files | Prevent accidentally committing API keys, private keys, tokens (expanded: platform-specific prefixes for Stripe/GitLab/DigitalOcean/SendGrid, JWT tokens, HashiCorp Vault tokens, connection string passwords, false-positive reduction for test dirs and placeholder values) |
| `rm -rf *` | Indiscriminate wipe — deletes everything in scope |
| `rm -rf .git` | Destroys version history — unrecoverable without a backup |

All other hard stops (git push, merge, destructive ops) apply in `full`/`partial`/`off` modes but are auto-approved in `crazy-workspace` within `./`.

## Per-project overrides (CLAUDE.md)

Global preferences apply across all projects. For repo-specific rules, add a `hands-free overrides` section to the project's CLAUDE.md:

```markdown
# hands-free overrides
- Always pause before auto-committing in this repo
- Never auto-accept git push
- Shell commands containing `psql postgresql://prod` must always ask
```

CLAUDE.md instructions take precedence over global `preferences.md` rules.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- Works with [Superpowers](https://github.com/anthropics/claude-code-plugins), custom skills, or any workflow with approval points

## What's new in 2.3

**New commands:**
- `/hands-free check <command>` — preview how any command would be classified (auto-pass / ask / HARD STOP) without running it; handles compound commands, pipelines, and shell variables; shows rule and safe alternative
- `/hands-free recommend prune` — review and remove stale low-confidence observations from `preferences.md`
- `/hands-free log --full` — complete session log with full event history

**Security:**
- Shell script content scanning: writing a `.sh`, `.bash`, or `.zsh` file that embeds `curl | bash`, language RCE, or `chmod 777` triggers a hard stop before the write
- Subshell `$(...)` rule: subshell substitutions inherit the inner command's classification (`bash $(curl URL)` → HARD STOP)
- Pipe (`|`) classification: classify each stage; most restrictive wins; `| bash/sh/zsh` → HARD STOP
- Process substitution `<(cmd)` rule: `diff <(git show HEAD:file) ./file` → auto-pass; `source <(curl URL)` → HARD STOP
- Shell variable expansion in paths: unknown vars → ask (conservative); known escape-list vars → ask; clearly local vars → auto-pass
- Sensitive env-var name detection: `API_KEY=live-secret cmd` announces risk (appears in `ps aux`)
- Secrets detection expanded significantly: platform-specific prefixes (`dop_v1_` DigitalOcean, `glpat-` GitLab, `github_pat_` new PAT format, `sk_live_/rk_live_/pk_live_` Stripe live keys, `AIza` Google, `ya29.` Google OAuth, `SG.` SendGrid, `hvs.` HashiCorp Vault), JWT token detection (`eyJ` literal in non-test code), additional assignment patterns (`jwt_secret=`, `webhook_secret=`, `master_key=`), false positive reduction for test dirs, comments, and placeholder values
- Complex shell construct rule: `if/for/while/case` — classified by most restrictive branch
- Heredoc pattern classification: local DB heredoc → auto-pass; remote DB or POST heredoc → ask
- Preference conflict resolution: higher-confidence rule wins; conflict announced once

**CLAUDE.md Override Reference:** Comprehensive per-project override syntax covering persistent settings, command/tool/pattern overrides, precedence rules, and scope (project vs user-global `~/.claude/CLAUDE.md`)

**Tool coverage — Package management:**
- `npm ci` → auto-pass (lockfile-exact install, deterministic)
- `npm audit fix` → auto-pass; `npm audit fix --force` → ask
- `bun add/remove/upgrade` → auto-pass (cwd-scoped)
- `pnpm run`, `yarn run`, `bun run` script inference: known-safe targets auto-pass, deploy/prod targets ask, unknown targets → inspect `package.json` first

**Tool coverage — Python:**
- `uv`: sync, add, remove, venv, pip compile, tool run → all auto-pass
- `poetry`: install, add, run → auto-pass; `pipenv`: install, run → auto-pass
- `black`, `isort`, `bandit`, `safety`, `coverage run/html`, `hatch build/run`, `python -m build` → auto-pass
- `conda list`, `conda env list`, `conda activate` → auto-pass; `conda create/install` → ask (writes outside cwd)
- `python -m json.tool`, `dis`, `timeit`, `pydoc`, `calendar`, `base64`, `ast`, `compileall` → auto-pass

**Tool coverage — Rust:**
- `cargo nextest`, `cargo expand`, `cargo fix`, `cargo clippy --fix`, `cross build`, `cargo miri test` → auto-pass
- `cargo watch`, `cargo outdated`, `cargo tree`, `cargo deny check`, `cargo machete` → auto-pass

**Tool coverage — TypeScript/Frontend:**
- `tsup`, `vite build/dev`, `esbuild`, `rollup`, `npx prettier --write/--check` → auto-pass
- `biome check/format`, `ts-node`, `tsx`, `vitest watch` → auto-pass

**Tool coverage — Go:**
- `go test`, `go build`, `go vet`, `go mod tidy`, `go mod download/verify/graph`, `go doc`, `go env`, `go list` → auto-pass
- `go generate ./...` → ask (runs arbitrary `//go:generate` directives); `go install` → ask (writes to `$GOPATH/bin`)

**Tool coverage — Ruby / Elixir / Java:**
- `bundle install/exec/update` → auto-pass; `gem install` → ask (system paths)
- `mix deps.get/compile/test/phx.server/ecto.migrate` → auto-pass; `mix ecto.rollback/hex.publish` → ask
- `mvn compile/test/verify`, `gradle build/test` → auto-pass; `mvn deploy`, `gradle publish` → ask

**Tool coverage — IaC and provisioning:**
- Pulumi: `preview/stack ls/stack output` → auto; `up/destroy/refresh/import` → ask
- AWS CDK: `ls/diff/synth` → auto; `deploy/destroy/bootstrap` → ask
- Ansible: `--check/--syntax-check` → auto; `ansible-playbook` → ask (remote SSH); `ansible-lint` → auto

**Tool coverage — Database migrations:**
- Flyway: `info` → auto; `migrate/baseline/repair/clean` → ask
- Liquibase: `status/history` → auto; `update/rollback/drop-all` → ask
- Knex: `migrate:status/list` → auto; `migrate:latest/rollback/seed:run` → ask
- Alembic: `current/history` → auto; `upgrade/downgrade` → ask

**Tool coverage — SaaS service CLIs:**
- Stripe: `listen/logs tail/fixtures` → auto; `trigger/events resend/login` → ask
- Supabase: `start/stop/status/migration new/gen types` → auto; `db push/reset/functions deploy/link` → ask
- Firebase: `emulators:start/functions:log` → auto; `deploy/use/login` → ask
- Vercel: `dev/build` → auto; `deploy/link/login/env pull` → ask
- Netlify: `dev/build/status/env:list` → auto; `deploy/link/login/env:set` → ask
- Railway: `status/list/logs` → auto; `up/run/link/login` → ask
- Fly.io: `status/logs/info/proxy` → auto; `deploy/launch/scale/ssh console/secrets set` → ask

**Tool coverage — SSH key management:**
- `ssh-keyscan <host>` (stdout) → auto; `>> ~/.ssh/known_hosts` → ask
- `ssh-add -l/-L` → auto; `ssh-add`/`ssh-copy-id` → ask
- `ssh-keygen -f ./key` (cwd output) → auto; default `~/.ssh/` path → ask

**Tool coverage — Container alternatives:**
- Podman and nerdctl follow the same rules as Docker equivalents

**Tool coverage — Debugging and analysis:**
- `gdb`/`lldb`/`valgrind`/`perf` on local binaries → auto-pass; on running process PIDs → ask
- Security testing tools (`nmap localhost`, `burpsuite` on local dev) → context-dependent

**Tool coverage — Infrastructure:**
- GitHub CLI (`gh`): 15+ read ops auto-pass; write ops ask; `gh pr checkout` auto in full, ask in partial
- GitLab CLI (`glab`): read ops auto-pass; write ops ask
- Playwright MCP: 8 read tools auto-pass; 14 write/interaction tools ask; `browser_evaluate` always ask
- kubectl: `get/describe/logs/port-forward` → auto-pass; `apply` (local file) auto in full; `delete/scale/rollout restart` → ask
- AWS CLI: `ec2 describe-*`, `iam list-*`, `lambda list-*`, `logs get-*` → auto-pass; `lambda invoke`, `iam create-*`, `ec2 start/stop`, `cloudformation deploy` → ask
- MCP tool naming heuristic: `get/fetch/list/search` → read (auto); `create/update/delete/send` → write (ask)

**Tool coverage — Build systems, services, Docker, Redis, SQL:**
- `cmake`, `ninja`, `meson` → auto-pass; `make clean/all/lint/fmt/check` → auto-pass; `make install/uninstall` → ask
- `uvicorn/gunicorn` localhost → auto-pass; `--host 0.0.0.0` → ask
- Docker: `cp`, `logs`, `inspect`, `pull`, `run --rm` (well-known), `buildx build` → auto-pass
- Redis: read ops localhost → auto-pass; write/destructive/remote → ask
- SQL DDL: `DROP TABLE/TRUNCATE` → ask; `SELECT/INSERT/CREATE TABLE` → auto-pass
- Network diagnostics: `ping`, `traceroute`, `dig`, `nslookup`, `curl --head` → auto-pass (read-only)

**Tool coverage (batches 51–60) — new in this version:**
- **Shell meta-rules:** --dry-run promotes ask→auto; --force demotes auto→ask; --insecure/--global/--system → ask; --version/--help → always auto
- **Security HARD STOPs added:** eval "$REMOTE_SCRIPT", LD_PRELOAD/DYLD_INSERT_LIBRARIES injection, socat EXEC:bash (reverse shell), SSH -R remote port forwarding, data exfiltration patterns (cat /etc/... | nc)
- **Low-level tools:** C/C++/LLVM (gcc/clang/clang-tidy/cppcheck/llvm-ar/llc/opt) → auto; Erlang/rebar3 (compile/test/dialyzer → auto; publish → ask); containerd/ctr/crictl (list → auto; pull/run → ask)
- **Framework CLIs:** Rails, Django extras, Phoenix extras, .NET CLI → all covered with full rule sets
- **Web server config:** nginx -t/caddy validate → auto; reload/restart → ask
- **Ruby testing:** rspec/minitest/cucumber → auto; rubocop -a autocorrect → auto
- **Python testing envs:** tox/nox (test → auto; publish → ask); pip-tools (pip-compile → auto; pip-sync → ask)
- **Security scanners:** bandit/safety/pip-audit/dependency-check/gosec/semgrep local → auto; semgrep remote rules → ask
- **Modern cryptography:** age (encrypt cwd → auto; decrypt → ask); sops (encrypt → auto; decrypt/edit → ask)
- **ML/Data science:** wandb (login/sync → ask; offline → auto); DVC (repro/params/metrics → auto; push → ask); MLflow (serve → auto; artifacts download → ask)
- **CI/CD:** Buildkite (start/upload → ask; meta-data get → auto); Jenkins CLI (localhost list → auto; build → ask)
- **K8s quality:** kube-score/kubeval/kubesec/pluto/conftest/kyverno → all auto (local validation only)
- **Service mesh:** istioctl analyze/validate → auto; install → ask; linkerd install → ask
- **gRPC/API:** grpcurl (localhost read → auto; remote → ask); rover (localhost introspect → auto; publish → ask); openapi-generator local → auto; remote URL → ask
- **Network:** tcpdump/tshark live capture → ask; reading pcap → auto; version managers rbenv/jenv/sdkman → auto
- **Code coverage:** lcov/nyc/c8/go cover → all auto; outdated dep checkers (npm/cargo/bundle/pip) → all auto
- **Observability:** vector validate/test → auto; otelcol validate → auto; both daemons → ask
- **Terminal/shell:** tmux/screen/zellij (list/attach → auto; kill → ask); just/task (list → auto; recipe → classify by name); shfmt → auto; envsubst cwd → auto; supervisord/supervisorctl (status → auto; start/stop → ask)
- **Database backup/restore:** pg_restore → ask (DB state); pg_restore -l → auto; mongorestore → ask; SQLite .dump → auto; Redis --rdb → auto
- **70 new example table entries** and **10 new troubleshooting FAQ entries**

**Behavior:**
- Parallel agents (dispatching-parallel-agents): agent count auto-accepted in full; task assignment review auto-approved in full
- Announcement throttling: precise rules for what counts as "identical" across iterations; every-10-iteration cadence summary
- Auto-commit new edge cases: GPG signing failure, post-commit hook failure (non-blocking), submodule pointer changes
- CLAUDE.md: user `/hands-free` commands win except project security rules; status shows active overrides
- Known Limitations section: 8 documented limitations (session scope, concurrent sessions, alias inspection, etc.)
- `/hands-free log --full`: complete event timeline with skill context, decision, and source

## What's new in 2.1

**Security:**
- Root shell escalation hard stop: `sudo -s`, `sudo su`, `sudo bash` now blocked in all modes
- Language RCE expanded: `deno run <url>`, `perl` fetch-then-eval patterns added to universal hard stops
- Global install detection: `npm install -g`, `cargo install`, `pip install` (no venv) → ask
- Docker volume mount rules: `-v ./:/app` auto-pass; `-v /:/host` ask
- Package publish/deploy hard stops: `cargo publish`, `npm publish`, `docker push`, `vercel deploy`, `docker compose push`, `terraform apply/destroy` → ask
- Secrets detection expanded: `.npmrc`, `*.cer/*.der/*.crt`, `database_url=`, `signing_key=`, hardcoded auth headers

**Commands:**
- `/hands-free` with no argument: activates full mode or shows status if already active
- `/hands-free learning` with no argument: shows current level and threshold summary
- `/hands-free recommend promote <action>`: promote a standard hard-stop to auto-accept (double confirmation required)
- `/hands-free explain` now covers hard stops, not just auto-accepts

**Shell auto-pass:**
- Git: `git checkout -b`, `git branch`, `git stash/pop`, `git log/status/diff`, `git tag`, `git commit` (non-amend), `git worktree add` added to always-auto-pass
- Version managers: `nvm`, `rustup` added alongside `pyenv`
- Package managers: `pnpm`, `yarn`, `bun` added alongside `npm`
- Databases: `sqlx migrate`, `alembic upgrade`, `npx prisma migrate`, `django manage.py migrate`
- More languages: Python type checking (mypy, ruff), Rust formatting (cargo fmt), Bun
- Docker Compose: `docker compose up/build/down/run` → auto-pass; `docker compose push` → ask
- Build tools: `make test/build` → auto-pass; `make install` → ask (may write to system)
- Infrastructure: `terraform plan` → auto-pass (read-only); `terraform apply/destroy` → ask
- Quality tools: `pre-commit run --all-files` → auto-pass

**Behavior:**
- Mode persistence: resets at session end; persist via CLAUDE.md `Default mode:` directive
- CLAUDE.md `Default mode/Auto-commit/Learning` directives activate at session start automatically
- Announcement format defined for all decision sources (silent, announce, hard stop)
- Custom skill implicit recommendation detection ("I recommend...", "I suggest...")
- Custom skill execution-type vs design-type classification for partial mode
- Loop stall prevention: 3 iterations with no new progress triggers stall warning

**Reliability:**
- Partial git add failure: announces which files failed before committing partial staging
- `preferences.md` corruption: continue with defaults, announce once
- `review-checkpoints off` ignored in partial mode (always on by design)
- Agent tool dispatch guidance: full mode auto-approves, partial mode depends on task type

## What's new in 2.0

**Security (all modes, no exceptions):**
- Universal hard stops for pipe-to-shell (`curl | bash`, `wget | sh`, `eval $(curl ...)`, `source <(curl ...)`)
- Universal hard stops for language-level RCE (`python -c "exec(urllib...)"`, `node -e "eval(require...)"`)
- Universal hard stops for privilege escalation (`chmod 777`, `sudo` to system paths)
- Pre-commit secrets detection (filename patterns + content signals) before every auto-commit
- Merge conflict detection blocks auto-commit when conflicts are unresolved

**New commands:**
- `/hands-free pause` / `/hands-free resume` — suspend without changing mode
- `/hands-free explain` — trace why the last decision was auto-accepted
- `/hands-free reset` — clear learned preferences with confirmation
- `/hands-free dry-run` — preview what would be auto-accepted
- `/hands-free review-checkpoints on/off` — structured pause before phase transitions

**Automation:**
- Iteration warnings and mandatory pause at 1 remaining iteration (ralph-loop)
- Git-log-based loop state detection replaces heuristic guessing
- Comprehensive auto-commit edge case handling

**Improvements:**
- Mode transitions, conflict resolution, custom skill integration all documented
- Preference staleness rules (confidence downgrades on repeated contradictions)
- Troubleshooting guide for common issues

## FAQ

**Does hands-free work with any Claude Code skill, not just superpowers?**
Yes. It recognizes approval point patterns (option lists, "Shall I proceed?" prompts, numbered choices) in any skill.

**Will it auto-accept things I don't want auto-accepted?**
Use `/hands-free dry-run` before enabling to see what would be auto-accepted. Use `/hands-free pause` to temporarily suspend auto-accept. Hard stops (pipe-to-shell, chmod 777, secrets) can never be auto-accepted.

**What if I change my mind about a learned preference?**
Override it manually — the staleness system will downgrade confidence after 2 overrides and replace the rule after 3. Or use `/hands-free reset` to clear all at once.

**Is it safe to use crazy-workspace on a shared repo?**
No. Crazy-workspace auto-approves git push, force push, and destructive resets within `./`. Use it only on personal sandboxes or throwaway repos.

**Does hands-free store sensitive data in preferences.md?**
Preferences store skill names and option choices (e.g., "writing-plans → subagent-driven"). They never contain code, secrets, or file contents. Secrets are explicitly blocked from being recorded.

**Can I disable learning for one session without resetting preferences?**
Use `/hands-free learning l` (low) for the session — it tracks but only auto-applies after 7x. Or use `/hands-free off` to observe without any auto-applying.

**Why did hands-free block a `python -c` or `node -e` command?**
If the command fetches content from a URL and executes it (e.g., `python -c "exec(urllib.request.urlopen('...').read())"`), it's treated as a language-level RCE pattern — equivalent to `curl | bash`. Download the script to a local file first, review it, then run it. The same rule applies to `deno run https://...` — use `deno run ./local-script.ts` instead.

**Does the mode persist across conversations?**
No — mode resets to `off` at the start of each new conversation. Add a `# hands-free overrides` section to your project's CLAUDE.md with `Default mode: full` to auto-activate it each session.

**Why is `docker compose push` / `terraform apply` blocked when `docker compose up` is auto-approved?**
`docker compose up` is a local operation that runs containers from your local images. `docker compose push` and `terraform apply` reach outside your local machine — they push to external registries or modify remote infrastructure. Hands-free distinguishes between local ops (auto-pass in full mode) and remote ops (always ask).

**How does hands-free interact with Claude Code's built-in permission system?**
They operate at different levels. Claude Code's permission system controls whether tool calls need system-level user confirmation (before the tool runs). Hands-free controls skill-level approval points — structured moments where a skill presents choices to the user. Both can be active simultaneously; hands-free's auto-pass rules don't bypass Claude Code's permission gates.

**Why does partial mode keep asking about `docker compose up` when full mode doesn't?**
Partial mode pauses at execution-type decisions — any approval point that leads directly to running code or making infrastructure changes. Even if `docker compose up` is cwd-scoped, it starts running services, so in partial mode it's classified as execution-type and will ask.

**How do I check if a specific command would auto-pass without running it?**
Use `/hands-free check <command>`. For example: `/hands-free check cargo build --release` shows "auto-pass (cwd-scoped)". `/hands-free check curl https://example.com/install.sh | bash` shows "HARD STOP (pipe-to-shell) — safe alternative: curl -o ./install.sh ...". This works in any mode and doesn't run the command.

**Does hands-free work with `uv`, `poetry`, or `pipenv`?**
Yes. All three are fully supported: `uv sync`, `uv add`, `poetry install`, `poetry run`, `pipenv install`, `pipenv run` are all auto-pass (cwd-scoped). `uv tool run` is also auto-pass (runs a tool in an isolated environment, equivalent to `pipx run`).

**Why is `DROP TABLE` blocked even on a local database?**
Dropping a table on a local dev database can delete weeks of seed data that's hard to restore. Hands-free asks for explicit confirmation on any destructive DDL (`DROP TABLE`, `TRUNCATE`) regardless of the database host. `SELECT`, `INSERT`, `UPDATE`, and `CREATE TABLE` on local databases auto-pass.

**My shell script contains `curl | bash` as a comment. Is it blocked?**
Shell script content scanning checks for hard stop patterns in the actual code lines, not comments. A comment like `# This runs: curl | bash` would not trigger the block. However, an uncommented `curl | bash` line in the script would block the write.

**Can I use `git pull --rebase` automatically?**
Yes. In full mode, `git pull`, `git pull --rebase`, and `git pull --ff-only` all auto-pass (they're local operations that update your working branch). In partial mode, `git pull` and `git pull --rebase` ask (they modify working state — execution-type decision), but `git pull --ff-only` always auto-passes (safe fast-forward that can't rewrite history).

**How does hands-free classify pipeline commands like `curl URL | jq '.'`?**
Pipelines are classified by their most restrictive stage. `curl URL | jq '.'` asks because `curl URL` escapes cwd (fetches remote). `cat ./data.json | jq '.'` auto-passes (both cwd-scoped). The exception: any pipeline ending in `| bash` or `| sh` is always a HARD STOP (pipe-to-shell). Use `/hands-free check curl URL | jq '.'` to see the breakdown.

**How do I work with monorepo tools like Turborepo or Nx?**
Common targets auto-pass: `turbo run build`, `turbo run test`, `turbo run lint`, `nx run app:build`, `nx run-many --target=build`, `lerna run test`. Deployment targets ask: `turbo run deploy`, `nx run app:deploy`, `lerna publish`. This matches the same known-safe/ask distinction as `npm run`.

**What happens when a shell variable in a command is blocked?**
Commands with `$VAR` in path arguments are classified conservatively when the variable's value is unknown (`rm -rf $BUILD_DIR` → ask). If you know the variable always resolves to a cwd path, restructure using an explicit relative path (`rm -rf ./build`), or add a CLAUDE.md override. Use `/hands-free check rm -rf $BUILD_DIR` to see why it's blocked.

**What if my preferences.md has conflicting rules?**
Higher-confidence rules win (5x high > 3x medium). If equal confidence, the more recent rule wins. A conflict is announced once per session: `[hands-free] Conflicting preferences for [decision] — using [winning-option]`. Run `/hands-free recommend prune` to clean up the conflict.

**How does hands-free know what mode to start in?**
`off` by default. If the project has a CLAUDE.md with `Default mode: full`, that mode activates at session start. Any `/hands-free <mode>` command during the session immediately overrides CLAUDE.md — the user's live command always wins. Project security rules in CLAUDE.md (custom HARD STOP overrides) cannot be overridden.

**Does hands-free support GitLab, Bitbucket, or Azure DevOps CLI tools?**
Yes for GitLab (`glab`): read operations (`glab mr list`, `glab issue view`, etc.) auto-pass; write operations (`glab mr create`, `glab pipeline run`, etc.) ask. For Bitbucket and Azure DevOps CLI tools, classification uses the MCP naming heuristic (verb-prefix) since they're less common. Add CLAUDE.md overrides for project-specific rules.

## Contributing

PRs welcome. If you find an approval point that hands-free doesn't handle well, open an issue.

## License

MIT
