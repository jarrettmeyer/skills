---
name: gan
description: "Adversarial multi-agent loop — Creator produces an artifact, Critic stress-tests it"
allowed-tools: Agent, Bash, Read, Write, Edit
disable-model-invocation: false
---

Run two adversarial feedback loops on any artifact. The Creator produces; the Critic reviews without
seeing the Creator's reasoning. Iteration stops when the Critic finds no critical or major issues,
or when max rounds is reached.

The top-level skill uses Bash for `git rev-parse`, `mkdir -p`, and `date`. Subagents declare their
own tools independently.

## Invocation

```
/jarrettmeyer:gan <request>
/jarrettmeyer:gan --plan-only <request>
/jarrettmeyer:gan --max-rounds N <request>
/jarrettmeyer:gan --creator-role "role" --critic-role "role" <request>
```

- Default: run both the Creation Phase and the Execution Phase.
- `--plan-only`: run only the Creation Phase; stop before execution.
- `--max-rounds N`: override the default of 3 rounds per phase. N must be a positive integer ≥ 1;
  invalid values fall back to 3.
- `--creator-role "..."`: explicitly set the Creator agent's role description.
- `--critic-role "..."`: explicitly set the Critic agent's role description.
- Flags must appear before the request text. A flag appearing elsewhere is treated as part of
  the request.
- Rounds can also be overridden in prose: "maximum of 2 rounds" or "max 2 rounds".

## File Conventions

Session artifacts are written to:

```
.scratch/jarrettmeyer/gan/gan-session-<timestamp>.md
```

Ensure `.scratch/` is listed in `.gitignore` to prevent session files from appearing in `git status`.

## Step 1: Initialize

**Prerequisites:** Use the Bash tool to run:

```bash
PROJECT_ROOT=$(git rev-parse --show-toplevel 2>/dev/null || pwd)
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
mkdir -p "$PROJECT_ROOT/.scratch/jarrettmeyer/gan"
```

All paths in this skill are relative to `$PROJECT_ROOT`. Do not re-evaluate `$TIMESTAMP`.

1. Parse flags and request from the invocation (see Invocation section). If the request string is
   empty after flag parsing, stop immediately and tell the user: "No request provided. Usage:
   /jarrettmeyer:gan [flags] <request>".
2. **Clarify goal and audience before proceeding.** Before inferring roles or writing any session
   file, identify two things from the request:
   - **Goal:** what outcome is the user trying to achieve? (e.g., "justify the 'build' choice to
     senior leadership", "find architecture risks in this plan", "produce a migration strategy")
   - **Audience:** who will consume the final output? (e.g., "senior leadership", "the engineering
     team", "my direct manager")

   If either is ambiguous or absent, **stop and ask the user** — do not infer or invent them.
   Present what you understood and what is unclear, e.g.:

   > I want to make sure I have the right framing before starting.
   > - **Goal:** [what I think you want — or "unclear"]
   > - **Audience:** [who I think the output is for — or "unclear"]
   >
   > Can you confirm or correct this?

   Only proceed once goal and audience are confirmed. Record both in the session file.
3. Determine role assignments using the confirmed goal, audience, and domain (e.g., a coding
   request → Creator: "software engineer", Critic: "senior code reviewer"; a build-vs-buy
   request for senior leadership → Creator: "business analyst", Critic: "CFO"). Roles can also
   be set explicitly via `--creator-role "..."` and `--critic-role "..."` flags. The Executor
   role defaults to the same as Creator.
3. Create `$PROJECT_ROOT/.scratch/jarrettmeyer/gan/gan-session-$TIMESTAMP.md`:

```markdown
# GAN Session

## Request

[verbatim request]

## Goal

[confirmed goal]

## Audience

[confirmed audience]

## Mode

FULL | PLAN_ONLY

## Max Rounds

[N]

## Roles

- **Creator:** [role]
- **Critic:** [role]
- **Executor:** [role]
```

## Step 2: Creation Phase Loop

Repeat up to max rounds.

### Creator agent

Spawn a general-purpose subagent:

1. You are a [CREATOR ROLE]. Produce an artifact for the following request: [REQUEST]
2. Prior Critic issues (if any): [ISSUES FROM PREVIOUS ROUND, or "none"]
3. Output ONLY the artifact. No preamble, no reasoning.

Append to session file. Under `### Round N`, write `**Artifact:**` as a bold inline label on its
own line, followed by the artifact text. Do not use `####` sub-headings.

```markdown
## Creation Phase

### Round N

**Artifact:**
[artifact text]
```

### Critic agent

> **Context isolation:** pass ONLY the artifact text — never the session file or prior reasoning.

Spawn a general-purpose subagent:

1. You are a [CRITIC ROLE] performing an adversarial review. You have no knowledge of how this
   artifact was produced.
2. Artifact: [ARTIFACT TEXT ONLY]
3. Identify issues. Classify each as `critical`, `major`, or `minor`.
4. Your response MUST begin with one of these exact signals on its own line:
   - `APPROVED`
   - `CHANGES_REQUESTED`
5. If `CHANGES_REQUESTED`: follow with a rank-ordered issue list using this format:

   ```
   [critical] Issue description
   [major]    Issue description
   [minor]    Issue description
   ```

6. If `APPROVED`: follow with a brief rationale.

**Signal parsing:** Trim whitespace and normalise to uppercase. If the first non-blank line does
not exactly match `APPROVED` or `CHANGES_REQUESTED`, treat it as `CHANGES_REQUESTED` and log:
`**Warning:** Unrecognised signal "[raw value]" — defaulting to CHANGES_REQUESTED`.

Append to session file:

```markdown
**Review:**
[review text]

**Status:** APPROVED | CHANGES_REQUESTED
**Issues:**

- [rank-ordered list if CHANGES_REQUESTED]
```

### Loop logic

- `CHANGES_REQUESTED` with critical or major issues and round < max rounds → iterate.
- `APPROVED`, only minor issues, or round = max rounds → record final status and proceed.

## Step 3: Execution Phase Loop

Skip entirely if mode is `PLAN_ONLY`.

Before spawning any agent, derive a one-sentence acceptance criterion from the original request.
Record it in the session file as `**Acceptance Criterion:** [criterion]`. Use this string verbatim
in every Critic prompt for this phase.

Repeat up to max rounds.

### Executor agent

Spawn a general-purpose subagent:

1. You are a [EXECUTOR ROLE]. Execute the following approved artifact exactly: [FINAL APPROVED ARTIFACT]
   For non-code artifacts, execution means producing or delivering the artifact as a final polished
   output (e.g., writing the memo, populating the spreadsheet, or generating the report).
   On every round, execute against this same approved artifact — do not revise the artifact itself.
2. Prior Critic issues (if any): [ISSUES FROM PREVIOUS ROUND, or "none"]
3. When done, output ONLY: (a) a summary of what was produced or changed, (b) any intentional
   deviations and why.

Append to session file. Under `### Round N`, write `**Output:**` as a bold inline label on its
own line, followed by the executor summary. Do not use `####` sub-headings.

```markdown
## Execution Phase

### Round N

**Output:**
[executor summary]
```

### Critic agent

> **Context isolation:** pass ONLY the executor summary and a one-sentence acceptance criterion.

Spawn a general-purpose subagent:

1. You are a [CRITIC ROLE] reviewing a completed execution. You have no knowledge of how it was done.
2. Acceptance criterion: [ONE-SENTENCE SUMMARY OF REQUEST]
3. Output summary: [EXECUTOR SUMMARY ONLY]
4. Classify issues as `critical`, `major`, or `minor`.
5. Your response MUST begin with:
   - `ACCEPTED`
   - `REJECTED`
6. If `REJECTED`: rank-ordered issue list. If `ACCEPTED`: brief summary.

**Signal parsing:** Trim whitespace and normalise to uppercase. If the first non-blank line does
not exactly match `ACCEPTED` or `REJECTED`, treat it as `REJECTED` and log:
`**Warning:** Unrecognised signal "[raw value]" — defaulting to REJECTED`.

Append to session file:

```markdown
**Review:**
[review text]

**Status:** ACCEPTED | REJECTED
**Issues:**

- [rank-ordered list if REJECTED]
```

### Loop logic

- `REJECTED` with critical or major issues and round < max rounds → iterate.
- `ACCEPTED`, only minor issues, or round = max rounds → record final status and stop.

## Step 4: Final Report

Tell the user:

- Creation phase: N rounds, final status (`APPROVED` or `MAX_ROUNDS_REACHED`)
- Execution phase: N rounds, final status (`ACCEPTED` or `MAX_ROUNDS_REACHED`), or `SKIPPED`
- Session file location

## Examples

| Invocation                                                            | Behavior                           |
| --------------------------------------------------------------------- | ---------------------------------- |
| `/jarrettmeyer:gan refactor the auth module`                          | Creation + execution, max 3 rounds |
| `/jarrettmeyer:gan --plan-only draft a migration strategy`            | Creation only, max 3 rounds        |
| `/jarrettmeyer:gan --max-rounds 2 write a blog post outline`          | Creation + execution, max 2 rounds |
| `/jarrettmeyer:gan --plan-only --max-rounds 1 review this API design` | Creation only, 1 round             |
| `/jarrettmeyer:gan --creator-role "novelist" --critic-role "literary editor" write a short story` | Custom roles, creation + execution |
