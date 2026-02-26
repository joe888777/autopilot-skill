# Autopilot Skill

A Claude Code skill that auto-accepts recommended options from any skill workflow, giving you a hands-off experience. Works with [superpowers](https://github.com/anthropics/claude-code-plugins) and any custom skill that presents choices.

## What it does

- **Auto-accepts** recommended options at approval points (brainstorming, design, execution, checkpoints)
- **Pauses** for destructive/irreversible actions (git push, merge, discard, force operations)
- **Learns** your preferences over time — tracks choices, builds confidence, adapts to you
- **Three modes** — `full`, `partial`, and `off` for different levels of autonomy

## Install

Clone and symlink (recommended — stays in sync with updates):

```bash
git clone git@github.com:joe888777/autopilot-skill.git ~/autopilot-skill
ln -s ~/autopilot-skill ~/.claude/skills/autopilot
```

Or copy:

```bash
git clone git@github.com:joe888777/autopilot-skill.git
cp -r autopilot-skill ~/.claude/skills/autopilot
```

## Usage

```
/autopilot          # Full auto-accept (default)
/autopilot full     # Same as above
/autopilot partial  # Auto-accept design, pause at execution
/autopilot off      # Back to normal
```

### Modes

| Approval Type | full | partial | off |
|---|---|---|---|
| Brainstorming approaches | auto | auto | ask |
| Design approval | auto | auto | ask |
| Execution method | auto | ask | ask |
| Batch checkpoints | auto | ask | ask |
| Destructive actions | ask | ask | ask |

### Example

Without autopilot:
```
Claude: "I see 3 approaches. Which do you prefer?"
You: "2"
Claude: "Design section 1: ... Does this look right?"
You: "yes"
Claude: "Subagent-driven or parallel session?"
You: "subagent"
```

With `/autopilot`:
```
Claude: "Going with approach 2 (recommended) — best balance of simplicity and flexibility."
Claude: "Design approved. Moving to implementation plan."
Claude: "Going with subagent-driven (recommended for this session)."
```

## How learning works

Autopilot records your choices in `preferences.md` whether it's on or off.

| Occurrences | Confidence | Behavior |
|---|---|---|
| 1-2x | low | Tracked, not auto-applied |
| 3-4x | medium | Auto-applied with announcement |
| 5x+ | high | Auto-applied silently |

Over time, autopilot picks **what you would pick**, not just the default recommendation.

## Session log

Ask "what did autopilot decide?" anytime to see a summary of all auto-accepted decisions in the current session.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- Works with [Superpowers](https://github.com/anthropics/claude-code-plugins), custom skills, or any workflow with approval points

## Contributing

PRs welcome. If you find an approval point that autopilot doesn't handle well, open an issue.

## License

MIT
