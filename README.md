# Claude Skills

This project is for **user level** Claude Code instructions, skills, and subagents, packaged as a [Code plugin](https://claude.com/plugins).

## Installation

```bash
# Install from GitHub
claude plugin install jarrettmeyer/skills

# Or for local development/testing
claude --plugin-dir /path/to/skills
```

Once installed, skills are available with the `jarrettmeyer:` namespace prefix:

| Command                    | Description                                            |
| -------------------------- | ------------------------------------------------------ |
| `/jarrettmeyer:caffeinate` | Prevent your Mac from sleeping                         |
| `/jarrettmeyer:podman`     | Build, run, and manage containers with Podman          |
| `/jarrettmeyer:scrapling`  | Scrape websites — static, JS-rendered, and anti-bot protected |
| `/jarrettmeyer:uv`         | Python project management, tools, and versions with uv |
