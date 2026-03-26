# skills

This repo contains Claude Code skills packaged as a plugin (`jarrettmeyer` namespace).

## Structure

```
├── plugins/
│   └── jarrettmeyer/
│       ├── .claude-plugin/
│       │   └── plugin.json     # Plugin metadata
│       └── skills/             # Claude skills
│           ├── caffeinate/
│           │   └── SKILL.md
│           ├── podman/
│           │   └── SKILL.md
│           ├── scrapling/
│           │   └── SKILL.md
│           └── uv/
│               └── SKILL.md
├── LICENSE.txt
└── README.md
```

## Versioning

The version is defined in two places — keep them in sync:

- `plugins/jarrettmeyer/.claude-plugin/plugin.json`
- `CHANGELOG.md`

## Adding a Skill

1. Create `plugins/jarrettmeyer/skills/<skill-name>/SKILL.md`
2. Add frontmatter: `description`, `allowed-tools`, `disable-model-invocation`
3. Write the skill content (instructions for Claude)
4. Update `README.md` skill table
5. Add entry to `CHANGELOG.md` and bump version in `plugin.json`

## Updating a Skill

1. Edit `plugins/jarrettmeyer/skills/<skill-name>/SKILL.md`
2. Add entry to `CHANGELOG.md` and bump version in `plugin.json`

## Skill Frontmatter

```yaml
---
name: skill-name
description: "Short description shown in skill picker"
allowed-tools: Bash # comma-separated: Bash, Read, Write, Edit, etc.
disable-model-invocation: false
---
```

## Local Testing

```bash
claude --plugin-dir /path/to/skills
```

Then invoke with `/jarrettmeyer:<skill-name>`.

## References

- https://github.com/anthropics/claude-plugins-official
