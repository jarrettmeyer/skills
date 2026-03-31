---
name: system-update
description: "Detect and update system packages, tools, and runtimes (Homebrew, bun, uv)"
allowed-tools: Bash
disable-model-invocation: false
---

Run system-wide updates for detected package managers and tool runtimes. Skips anything not installed.

## Step 1: Detect Available Tools

Run a single detection block:

```bash
echo "=== Tool Detection ==="
if command -v brew >/dev/null 2>&1; then echo "brew: $(brew --version | head -1)"; else echo "brew: not found"; fi
if command -v bun >/dev/null 2>&1; then echo "bun: $(bun --version)"; else echo "bun: not found"; fi
if command -v uv >/dev/null 2>&1; then echo "uv: $(uv --version)"; else echo "uv: not found"; fi
```

Print a summary of what was found and what will be skipped. If nothing is found, tell the user and stop.

## Step 2: Homebrew (if installed)

Skip this section entirely if brew was not detected.

### 2a: Update formula index

```bash
brew update
```

### 2b: List outdated packages

```bash
brew outdated
```

Capture this output. Partition the results into two groups:

- **Key packages** — packages from the list in step 2c that appear in the outdated output
- **Other packages** — everything else in the outdated output

### 2c: Upgrade key packages

These packages should be kept current. **Only upgrade a package if it appears in the `brew outdated` output from step 2b AND is already installed.** Do not run `brew install` for packages that are not present.

Key packages:

- `awscli`
- `bash`
- `cfn-lint`
- `coreutils`
- `curl`
- `gh`
- `git`
- `jq`
- `node`
- `podman`
- `podman-compose`
- `podman-desktop`
- `python@3.14`
- `tree`
- `wget`

Upgrade all outdated key packages in a single command:

```bash
brew upgrade <pkg1> <pkg2> ...
```

If no key packages are outdated, skip this step.

### 2d: Show remaining outdated packages

Show the "Other packages" group from step 2b to the user. Only upgrade them if the user confirms or originally said "update everything."

### 2e: Cleanup

```bash
brew cleanup
```

### 2f: Casks (opt-in only)

Cask upgrades (GUI applications) are **opt-in only**. Only run these if the user explicitly asks (e.g., "update everything including apps"):

```bash
brew upgrade --cask
```

## Steps 3 & 4: bun and uv (in parallel)

After brew completes, run bun and uv updates concurrently — they are independent.

### bun (if installed)

Skip if bun was not detected.

```bash
bun upgrade
```

Report the version change. If the upgrade fails with a permissions error, suggest `sudo bun upgrade`.

### uv (if installed)

Skip if uv was not detected.

```bash
uv self update
uv tool upgrade --all
```

## Step 5: Summary

Print a final summary listing:

- Tools that were updated (with version changes where available)
- Tools that were skipped (not installed)
- Any failures encountered

## Examples

| User says                                | Action                                                |
| ---------------------------------------- | ----------------------------------------------------- |
| "Update my system"                       | Detect tools, update all with safe defaults           |
| "Update everything including brew casks" | Include `brew upgrade --cask`                         |
| "Just update bun and uv"                 | Skip brew even if installed                           |
| "Update all outdated brew packages"      | Upgrade everything from `brew outdated`, not just key |
| "Skip homebrew"                          | Run bun and uv only                                   |
