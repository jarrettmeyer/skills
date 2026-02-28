---
name: caffeinate
description: Keep the computer awake. Use when user mentions sleep, caffeinate, stay awake, keepalive, or specifies a wake duration/time.
---

# Caffeinate

Prevent the system from sleeping using macOS `caffeinate`.

## Always Kill Existing Processes First

Before starting caffeinate, always kill any existing process:

```bash
killall caffeinate 2>/dev/null || true
```

## Start Caffeinate

**Indefinitely:**
```bash
caffeinate -dimsu &
```

**Until a specific time:**

Calculate seconds from now until target time, then run:
```bash
caffeinate -dimsu -t <seconds> &
```

Example: If it's 1:38 pm and user says "until 4:30 pm":
```bash
caffeinate -dimsu -t $(( ((16*60 + 30) - (13*60 + 38)) * 60 )) &
```

**Always run in background with `&`.**

## Stop Caffeinate

```bash
killall caffeinate
```

## Examples

| User says | Action |
|-----------|--------|
| "Stay awake until 6 pm" | Calculate seconds, run with `-t` |
| "/caffeinate 4:30 pm" | Calculate seconds, run with `-t` |
| "Keep my computer awake" | Run indefinitely |
| "Stop caffeinate" | Kill the process |
