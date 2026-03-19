# Claude Skills

This project is for **user level** Claude Code instructions, skills, and subagents, packaged as a Claude Code plugin.

## Installation

```bash
# Install from GitHub
claude plugin install jarrettmeyer/skills

# Or for local development/testing
claude --plugin-dir /path/to/skills
```

Once installed, skills are available with the `jarrettmeyer:` namespace prefix:

- `/jarrettmeyer:caffeinate`
- `/jarrettmeyer:podman`
- `/jarrettmeyer:scrapling`
- `/jarrettmeyer:uuid`
- `/jarrettmeyer:uv`

## Available Skills

- `caffeinate`: Keep the computer from going to sleep
- `podman`: Instructions for using [Podman](https://podman.io/docs) instead of Docker
- `scrapling`: Scrape websites using the [scrapling](https://github.com/D4Vinci/Scrapling) library, with intelligent fetcher selection for static, JS-rendered, and anti-bot protected sites
- `uuid`: Generate UUIDs
- `uv`: Python project management, script execution, global tool management, and Python version management using [uv](https://docs.astral.sh/uv/)
