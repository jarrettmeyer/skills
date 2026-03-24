---
description: "Use uv for Python project management, running scripts and tools, and managing Python versions"
allowed-tools: Bash
disable-model-invocation: false
---

uv is a fast Python package and project manager. It covers project management, one-off script execution, global CLI tools (like pipx), and Python version management (like pyenv).

## Self-Update

```bash
uv self update
```

## Project Management

```bash
uv init my-project          # create new project
uv init                     # init in current directory

uv add requests             # add dependency
uv add 'requests>=2.28'     # with version constraint
uv add --dev pytest         # add dev dependency
uv remove requests          # remove dependency

uv sync                     # install all deps from lockfile
uv lock                     # update lockfile without syncing

uv run python main.py       # run within project virtualenv
uv run pytest               # run command within project virtualenv
```

## Script Execution (No Project Required)

```bash
# Flag-specified deps (no pyproject.toml needed)
uv run --with requests script.py
uv run --with 'requests,beautifulsoup4' script.py

# PEP 723 inline metadata (deps declared inside the script)
# /// script
# dependencies = ["requests", "rich"]
# ///
uv run script.py
```

## Tools (Global CLI Tools)

Equivalent to pipx. Install once, available system-wide.

```bash
uv tool install ruff          # install a CLI tool globally
uv tool list                  # list installed tools
uv tool upgrade ruff          # upgrade a specific tool
uv tool upgrade --all         # upgrade all tools
uv tool uninstall ruff        # remove a tool

uvx ruff check .              # run tool without installing (ephemeral)
uvx --from httpie http GET https://example.com
```

## Python Version Management

```bash
uv python list                       # list available Python versions
uv python list --only-installed      # list only installed versions
uv python install 3.12               # install a Python version
uv python pin 3.12                   # pin version for current project (.python-version)
```

## Examples

| User says                              | Action                                                    |
| -------------------------------------- | --------------------------------------------------------- |
| "Update uv"                            | `uv self update`                                          |
| "Add requests to the project"          | `uv add requests`                                         |
| "Run this script with httpx available" | `uv run --with httpx script.py`                           |
| "Install ruff globally"                | `uv tool install ruff`                                    |
| "Update all my tools"                  | `uv tool upgrade --all`                                   |
| "What Python versions are installed?"  | `uv python list --only-installed`                         |
| "Switch project to Python 3.13"        | `uv python install 3.13`, `uv python pin 3.13`, `uv sync` |
