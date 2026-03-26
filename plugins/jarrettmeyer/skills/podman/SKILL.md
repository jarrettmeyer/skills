---
name: podman
description: "Use Podman to interact with local containers"
allowed-tools: Bash
disable-model-invocation: false
---

Use [Podman](https://podman.io) for local container operations: building images, running containers, compose, logs, volumes, and networking.

## Verify Installation

Always confirm podman is available before running commands. If not found, tell the user and stop.

```bash
podman --version
```

## Build

Build an image from a Containerfile or Dockerfile.

```bash
podman build --tag <image>:<tag> <context-path>

# Examples
podman build --tag myapp:latest .
podman build --tag myapp:v1.0 --file Containerfile.dev .
podman build --no-cache --tag myapp:latest .
```

## Run

Run a container from an image.

```bash
podman run --interactive --tty ubuntu:latest /bin/bash
podman run --detach --publish 8080:80 --name webserver nginx:latest
podman run --rm --volume $(pwd):/app myapp:latest
podman run --env DATABASE_URL=postgres://... myapp:latest
```

## Logs

When the user asks about container logs, spawn a subagent to analyze them. This keeps the main context clean. Pass the user's original question to the subagent. Give the subagent these instructions:

1. Fetch logs using `podman logs --tail 200 <container>` (or `podman compose logs <service>`). Increase `--tail` if the user asks for more history.
2. Scan for: errors, warnings, stack traces, repeated patterns, and timing anomalies.
3. Summarize findings: error/warning counts, notable patterns, most recent errors with timestamps.
4. Answer this specific question: [USER'S ORIGINAL QUESTION]

## Compose

Run multi-container applications.

```bash
podman compose up --detach
podman compose down
podman compose logs --follow
podman compose ps
```

## Container Management

```bash
podman ps                  # list running containers
podman ps --all            # list all containers
podman stop <container>    # stop a container
podman rm <container>      # remove a container
podman images              # list images
podman rmi <image>         # remove an image
```

## Networking

```bash
podman network ls
podman network create <name>
podman network inspect <name>
podman network rm <name>
```

## Volumes

```bash
podman volume ls
podman volume create <name>
podman volume inspect <name>
podman volume rm <name>
```

## Docker Compatibility

- **Rootless by default**: runs containers without root privileges
- **Daemonless**: no background daemon; each command is a direct process
- **CLI-compatible**: most `docker` commands work with `podman`
- You can alias `docker` to `podman` for drop-in compatibility

## Examples

| User says                                   | Action                                            |
| ------------------------------------------- | ------------------------------------------------- |
| "What version of podman is installed?"      | Run `podman --version`                            |
| "Build this container"                      | Find Containerfile/Dockerfile, run `podman build` |
| "Build and run this container"              | Build then run with sensible defaults             |
| "Show me the logs from the nginx container" | Spawn log-analysis subagent                       |
| "Stop all running containers"               | `podman stop` each, or `podman compose down`      |
| "Clean up old images"                       | `podman rmi` unused images                        |
