# Changelog

## [1.0.7] - 2026-04-16

### Added

- `pdf` — read, extract text from, and convert PDF files

### Changed

- `system-update` — update key brew packages list

## [1.0.6] - 2026-03-31

### Added

- `system-update` — detect and update system tools (Homebrew, bun, uv)

## [1.0.5] - 2026-03-30

### Changed

- `gan` — document intentional signal asymmetry between Creation and Execution phase critics (closes #12)

## [1.0.4] - 2026-03-29

### Fixed

- `scrapling` — `StealthyFetcher` and `DynamicFetcher` examples corrected from `.get()` to `.fetch()` (closes #6)
- `scrapling` — run command updated from `--with scrapling` to `--with "scrapling[all]"` to include browser/HTTP extras (closes #7)

## [1.0.3] - 2026-03-29

### Changed

- `scrapling` — output directory changed from `.scrapling/` to `.scratch/jarrettmeyer/scrapling/` to match the `gan` skill convention

## [1.0.2] - 2026-03-29

### Added

- `gan` — adversarial multi-agent review loop (Creator, Critic, Executor)

## [1.0.1] - 2026-03-26

### Fixed

- Added missing `name` field to frontmatter of all 4 skills

## [1.0.0] - 2026-03-24

Initial release.

### Added

- `caffeinate` — prevent your Mac from sleeping
- `podman` — build, run, and manage containers with Podman
- `scrapling` — scrape websites (static, JS-rendered, and anti-bot protected)
- `uv` — Python project management, tools, and versions
