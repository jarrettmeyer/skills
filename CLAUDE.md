# CLAUDE

This is a user-level `~/.claude/CLAUDE.md`. This applies to all projects. Keep this file concise — if it grows beyond 200 lines, notify the user.

## 1. Plan Mode

**Always begin non-trivial tasks in plan mode to understand the problem deeply.** Plan mode is about interrogating the problem before jumping to solutions. Both the user and Claude must understand:
- What problem are we actually solving?
- What are the requirements?
- What are the edge cases and boundary constraints?

### In Plan Mode, Always:
1. **Ask clarifying questions** - don't assume you understand
2. **Explore context** - what code/systems are involved?
3. **Identify constraints** - what can't we change?
4. **Define success criteria** - how do we verify it worked?
5. **Consider alternatives** - what other approaches exist?

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

## 3. Workflow

**Use task lists and feature branches for all work.**

- Create a task list for non-trivial work (use TaskCreate)
- Always work on a feature branch, never on primary branches (main, master, dev, qa, prod, release, etc.)
- Branch naming: descriptive of the problem (e.g., `fix-auth-bug`, `add-user-profile`)
- After making code changes, always run `/simplify`
- Let the user decide: merge locally or create PR

**Don't assume the merge strategy.** Ask if unclear.

## 4. Best Practices

- **No magic strings or numbers.** Name constants to express intent. Prefer `expire_in(SESSION_EXPIRATION_SECONDS)` over `expire_in(86400)`.
- **Guard clauses / early returns.** Validate inputs at the top and return early rather than nesting the happy path.
- **No dead code.** Delete unused code rather than commenting it out. Version control is the safety net.
- **Fail fast.** Validate at system boundaries (user input, external APIs) and surface errors immediately rather than letting bad state propagate.
- **Prefer pure functions.** Minimize side effects. When side effects are necessary, make them explicit.
