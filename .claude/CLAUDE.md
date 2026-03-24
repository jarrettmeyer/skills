# skills

This repo contains Claude Code skills packaged as a plugin (`jarrettmeyer` namespace).

## Structure

```
plugins/jarrettmeyer/
  .claude-plugin/plugin.json  # Plugin metadata
  commands/                   # Skill files (one .md per skill)
```

## Adding a Skill

1. Create `plugins/jarrettmeyer/commands/<skill-name>.md`
2. Add frontmatter: `description`, `allowed-tools`, `disable-model-invocation`
3. Write the skill content (instructions for Claude)
4. Update `README.md` skill table

## Skill Frontmatter

```yaml
---
description: "Short description shown in skill picker"
allowed-tools: Bash  # comma-separated: Bash, Read, Write, Edit, etc.
disable-model-invocation: false
---
```

## Local Testing

```bash
claude --plugin-dir /Users/V6X9878/GitHub/jarrettmeyer/skills
```

Then invoke with `/jarrettmeyer:<skill-name>`.

## References

- https://github.com/anthropics/claude-plugins-official