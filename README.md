# Claude Skills

This project is for **user level** Claude Code instructions, skills, and subagents.

## Features

- **`CLAUDE.md`**: User-level Claude Code instructions, symlinked to `~/.claude/CLAUDE.md`.
- **Skills**: Custom Claude Code skills for specific workflows

## Installation

```bash
# Where do you want to checkout this project?
cd /path/to/checkout/
git clone https://github.com/jarrettmeyer/skills.git

# Symlink CLAUDE.md
ln -s /path/to/checkout/skills/CLAUDE.md ~/.claude/CLAUDE.md

# Symlink a skill to Claude skills
mkdir -p ~/.claude/skills/
ln -s /path/to/checkout/skills/skills/use-podman/ ~/.claude/skills/
```

### Available Skills
- `caffeinate`: Keep the computer from going to sleep
- `use-podman`: Instructions for using Podman instead of Docker
- `uuid`: Generate UUIDs