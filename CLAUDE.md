# CLAUDE

This is a user-level `~/.claude/CLAUDE.md`. This applies to all projects. Keep this file concise — if it grows beyond 200 lines, notify the user.

## 1. Plan Mode

**Always begin non-trivial tasks in plan mode to understand the problem deeply.** Plan mode is about interrogating the problem before jumping to solutions. Both the user and Claude must understand:

- What problem are we actually solving?
- What are the requirements?
- What are the edge cases and boundary constraints?

### In Plan Mode, Always:

1. **Ask clarifying questions**: Do not assume you understand.
2. **Explore context**: What code/systems are involved?
3. **Identify constraints**: What can't we change?
4. **Define success criteria**: How do we verify it worked?
5. **Consider alternatives**: What other approaches exist?

### Red Flags (You Don't Understand the Problem):

- You can't explain WHY we're doing this
- Success criteria are vague ("make it better")
- You don't know what can/can't be changed
- Multiple interpretations of the request exist

**When in doubt, ask questions in plan mode before proposing solutions.**

## 2. Verification

**Define how to verify success before starting work.** Success criteria must be concrete and testable.

- What commands verify it works?
- What should the output be?
- What edge cases to test?
- How to confirm no regressions?

**Before claiming "done": run verification steps and show actual output.**

### UI Changes Require Playwright Testing

**CRITICAL: When making UI updates (Streamlit, web interfaces), you MUST test with Playwright, not just unit tests.**

- Unit tests (pytest) alone are NOT sufficient for UI changes
- Always verify the actual UI loads and renders correctly
- Check for red error text, broken imports, missing data, and console logs

## 3. Workflow

**Use task lists and feature branches for all work.**

- Create a task list for non-trivial work (use TaskCreate)
- Always work on a feature branch, never on primary branches (main, master, dev, qa, prod, etc.)
- Branch naming: descriptive of the problem (e.g., `fix-auth-bug`, `add-user-profile`)
- After making code changes, always run `/simplify`
- When work is complete: push the branch and create a draft PR to the base branch. **Do not ask.** PR is always the answer.

### Task Decomposition

**Before starting work, decompose the request into independent work streams.** A single user request often contains multiple concerns that should be separate branches, worktrees, and PRs.

Ask yourself: "If I made a PR for each part, would a reviewer need context from the other PRs to understand any one of them?" If no, they're independent — split them.

When the split is obvious, do it and inform the user. Only ask for confirmation when the boundary is genuinely ambiguous. If in doubt, split — merging two small PRs is easier than untangling one large one.

## 4. Best Practices

- **No unsolicited Markdown files**: Never create `.md` files unless the user explicitly requests them.
- **No magic strings or numbers**: Name constants to express intent. Prefer `user.has_role(ADMIN_GROUP_NAME)` over `user.has_role("my_group_name")`.
- **Guard clauses / early returns**: Validate inputs at the top and return early rather than nesting the happy path.
- **No dead code**: Delete unused code rather than commenting it out. Version control is the safety net.
- **Fail fast**: Validate at system boundaries (user input, external APIs) and surface errors immediately rather than letting bad state propagate.
- **Prefer pure functions**: Minimize side effects. When side effects are necessary, make them explicit.

## 5. Pushback

**Always challenge requests that don't add value — before executing them.**

Before starting any task, ask: does this create redundancy, maintenance burden, or complexity without a clear benefit? If so, say so directly and recommend the simpler path. Do not silently comply and fix it later.

Examples of things to push back on:

- Creating a file that duplicates content already maintained elsewhere
- Adding abstraction or generality that no current use case requires
- Writing documentation that restates what the code already says clearly
- Keeping a workaround alive when the underlying problem can just be fixed

**The standard:** if a thoughtful senior engineer would raise an eyebrow, raise it first.

## 6. Code Comments

Comments explain **why**, not **what**. The code itself shows what is happening. If the "what" isn't obvious, that's a code clarity problem — fix the code first.

**When to write a comment:**

- **Classes, functions, and constants**: Add docstrings to all public classes, functions, and constants.
- **Edge cases**: Explain non-obvious boundary conditions that the code must handle.
- **Environmental or systemic constraints**: Note external factors that force a particular implementation (infrastructure limits, platform quirks, performance constraints).
- **Library conflicts or surprises**: Call out unexpected library behavior, version incompatibilities, or workarounds.
- **Failed approaches**: Document what was tried and why it didn't work, so future developers don't repeat the same dead ends.

**When NOT to comment:**

- Do not restate what the code obviously does (`i++ // increment i`)
- Do not add boilerplate attribution or TODO comments without an owner and ticket
