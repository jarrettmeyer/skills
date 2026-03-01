---
name: uv
description: Use uv for Python project management, running scripts and tools, managing Python versions, and keeping uv itself up to date. Trigger when users mention uv, Python dependencies, virtual environments, running Python scripts, or managing Python tool installations.
---

# SKILL: uv

uv is a fast Python package and project manager. It covers project management, one-off script execution, global CLI tools (like pipx), and Python version management (like pyenv).

## Verify uv

```bash
which uv         # => /opt/homebrew/bin/uv (or ~/.local/bin/uv)
uv --version     # => uv 0.x.x
```

## Self-update

```bash
uv self update
```

## Project Management

Core project workflow: init, add/remove deps, sync env, lock.

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

One-off scripts with inline or flag-specified dependencies.

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
uv tool install httpie

uvx ruff check .              # run tool without installing (ephemeral)
uvx --from httpie http GET https://example.com

uv tool list                  # list installed tools
uv tool upgrade ruff          # upgrade a specific tool
uv tool upgrade --all         # upgrade all tools
uv tool uninstall ruff        # remove a tool
```

## Python Version Management

Replaces pyenv.

```bash
uv python list                       # list available Python versions
uv python list --only-installed      # list only installed versions

uv python install 3.12               # install a Python version
uv python install 3.11 3.12          # install multiple versions

uv python pin 3.12                   # pin version for current project (.python-version)

# To upgrade a patch version, just install the newer one:
# Installing 3.14 (or 3.14.2) replaces the existing 3.14.x installation.
uv python install 3.14               # installs latest patch of 3.14
uv python install 3.14.2             # installs a specific patch version

# To migrate a project to a new Python version (e.g. 3.13 → 3.14):
# Work on a feature branch — library compatibility failures are common during
# Python version upgrades. A branch lets you test before merging.
git checkout -b upgrade-python-3.14
uv python install 3.14        # 1. ensure the version is available
uv python pin 3.14            # 2. update .python-version
# Also update requires-python in pyproject.toml if needed
uv lock                       # 3. regenerate uv.lock for new Python
uv sync                       # 4. rebuild .venv with new version + locked deps
# Run your test suite to catch any incompatible packages before merging.
```

## Pip-Compatible Interface (Legacy / Escape Hatch)

For projects not yet using uv natively.

```bash
uv venv                       # create a virtual environment
uv pip install requests       # install into active venv
uv pip compile requirements.in -o requirements.txt
uv pip sync requirements.txt  # install exact pinned deps
```
