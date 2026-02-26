# Autopilot Skill

A Claude Code skill that auto-accepts recommended options from [superpowers](https://github.com/anthropics/claude-code-plugins) skills, giving you a hands-off workflow.

## What it does

- **Auto-accepts** all recommended options at approval points (brainstorming approaches, design sections, execution methods, batch checkpoints)
- **Pauses** for destructive/irreversible actions (git push, merge, discard, force operations)
- **Learns** your preferences over time — tracks your choices and adapts to pick what you would pick

## Install

Copy the skill to your Claude Code skills directory:

```bash
# Clone the repo
git clone git@github.com:joe888777/autopilot-skill.git

# Copy to Claude Code skills
cp -r autopilot-skill ~/.claude/skills/autopilot
```

Or symlink it:

```bash
git clone git@github.com:joe888777/autopilot-skill.git ~/autopilot-skill
ln -s ~/autopilot-skill ~/.claude/skills/autopilot
```

## Usage

Invoke at the start of a session:

```
/autopilot
```

Turn off (reverts to normal approval behavior):

```
/autopilot off
```

## How learning works

Autopilot records your choices in `preferences.md` (whether autopilot is on or off). After seeing the same pattern 3+ times, it consolidates into a learned rule. Next time autopilot is on, it uses your preference instead of the default recommendation.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Superpowers plugin](https://github.com/anthropics/claude-code-plugins) (or any skill-based workflow with approval points)

## License

MIT
