# skills

This repo contains Claude Code skills packaged as a plugin (`jarrettmeyer` namespace).

## Structure

```
├── .claude/
├── .vscode/                    # VSCode settings
├── plugins/
│   └── jarrettmeyer/
│       ├── .claude-plugin/
│       │   └── plugin.json     # Plugin metadata
│       └── skills/             # Claude skills
├── LICENSE.txt
└── README.md
```

## Versioning

The version is defined in two places — keep them in sync:

- `plugins/jarrettmeyer/.claude-plugin/plugin.json`
- `CHANGELOG.md`

## Workflow

All changes must follow this process:

1. **Plan mode first.** No edits until a plan is approved. The plan must include: branch name, worktree setup, skill-specific steps, CHANGELOG decision, and PR creation.
2. **Branch + worktree.** Create a feature branch and a project-local worktree at `.worktrees/<branch-name>/`. All edits happen inside the worktree.
   ```bash
   git worktree add .worktrees/<branch-name> -b <branch-name>
   ```
3. **Make changes.**
   - For a new skill: create `plugins/jarrettmeyer/skills/<skill-name>/SKILL.md` and add it to the `README.md` skill table with a brief description.
   - For an existing skill: edit `plugins/jarrettmeyer/skills/<skill-name>/SKILL.md` and verify the README description is still accurate.
4. **CHANGELOG.** For material skill changes (new skills, removals, behavior changes, bug fixes): add an entry to `CHANGELOG.md` and bump the version in `plugin.json`. Infrastructure, tooling, and documentation-only changes do not get entries.
5. **Pull request.** Push the branch and open a draft PR to `main`. Always PR — never commit directly to `main`.

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
claude --plugin-dir ./plugins/jarrettmeyer/
```

Then invoke with `/jarrettmeyer:<skill-name>`.

## References

- https://github.com/anthropics/claude-plugins-official
