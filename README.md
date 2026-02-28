# Claude Skills

This project is for **user level** Claude skills and subagents.

## Features

- **Skills:** Custom Claude Code skills for specific workflows
- **Self-Improvement:** Automatic learning system that captures patterns across sessions
- **Rules System:** Behavioral rule proposals with manual review workflow

## Installation

### Option 1: Clone and Symlink Individual Skills

```bash
# Where do you want to checkout this project?
cd /path/to/checkout/
git clone https://github.com/jarrettmeyer/skills.git

# Symlink a skill to Claude skills
mkdir -p ~/.claude/skills/
ln -s /path/to/checkout/skills/skills/use-podman/ ~/.claude/skills/
```

### Option 2: Full Installation with Self-Improvement System

```bash
# Clone the repository
cd ~/GitHub/jarrettmeyer/  # or your preferred location
git clone https://github.com/jarrettmeyer/skills.git

# Create symlinks for self-improve skill and rules system
ln -s ~/GitHub/jarrettmeyer/skills/skills/self-improve ~/.claude/skills/self-improve
ln -s ~/GitHub/jarrettmeyer/skills/skills/rules ~/.claude/rules
```

## Available Skills

### `self-improve`
Captures session learnings into memory and proposes behavioral rules for review.

**Usage:** Invoke `/self-improve` at the end of meaningful work sessions.

**What it does:**
- Updates `MEMORY.md` with project-specific patterns and learnings
- Proposes behavioral rules in `~/.claude/rules/proposed/` for manual review
- Keeps your Claude Code configuration lean and effective

**Example workflow:**
```bash
# After completing work session
/self-improve

# Review proposed rules
ls ~/.claude/rules/proposed/

# Accept a rule
mv ~/.claude/rules/proposed/2026-02-28-rule-name.md ~/.claude/rules/active/

# Or reject it
mv ~/.claude/rules/proposed/2026-02-28-rule-name.md ~/.claude/rules/rejected/
```

### Other Skills
- `use-podman`: Instructions for using Podman instead of Docker
- `uuid`: Generate UUIDs for various purposes