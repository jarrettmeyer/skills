# Claude Skills

This project is for **user level** Claude Code instructions, skills, and subagents, packaged as a [Code plugin](https://claude.com/plugins).

## Installation

```bash
# Install from GitHub
claude plugin install jarrettmeyer/skills

# Or for local development/testing
claude --plugin-dir ./plugins/jarrettmeyer/
```

Once installed, skills are available with the `jarrettmeyer:` namespace prefix:

| Command                       | Description                                            |
| ----------------------------- | ------------------------------------------------------ |
| `/jarrettmeyer:caffeinate`    | Prevent your Mac from sleeping                         |
| `/jarrettmeyer:gan`           | Adversarial multi-agent review loop                    |
| `/jarrettmeyer:pdf`           | Read and process PDF files                             |
| `/jarrettmeyer:podman`        | Build, run, and manage containers with Podman          |
| `/jarrettmeyer:scrapling`     | Scrape websites                                        |
| `/jarrettmeyer:system-update` | Detect and update system tools (Homebrew, bun, uv)     |
| `/jarrettmeyer:uv`            | Python project management, tools, and versions with uv |
