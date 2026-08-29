# Project agent workflow

This repository uses a three-role development workflow.

## Roles

- `architect`: read-only. Understands the request and codebase, identifies affected areas, risks, interfaces, and proposes an implementation plan. Does not edit code.
- `developer`: implementation owner. Makes code changes, builds, and runs relevant tests. Does not approve its own work.
- `reviewer`: independent read-only reviewer. Reviews the resulting diff for correctness, regressions, design issues, concurrency/lifetime problems, error handling, and missing tests. Does not edit code.

## Default workflow

For feature work, bug fixes, refactors, or non-trivial code changes:

1. Delegate analysis to `architect` first.
2. Wait for the architect's implementation plan.
3. Delegate implementation to `developer`, passing the architect's plan and the original user request.
4. Wait for the developer to finish implementation and verification.
5. Delegate the final diff to a fresh `reviewer` agent. The reviewer must be independent from the developer.
6. If the reviewer reports any `Critical` or `Major` findings, send those findings back to `developer` for correction.
7. Re-run `reviewer` after the correction. Repeat at most two correction cycles unless the user explicitly asks for more.
8. The main agent gives the final report. Do not call the task complete while unresolved `Critical` or `Major` findings remain.

For tiny edits, the user may explicitly ask to skip the architect, but keep Developer and Reviewer separate.

## Shared engineering rules

- Inspect repository documentation and build files before choosing build/test commands.
- Never invent a build or test command when the repository already defines one.
- Prefer the smallest change that fully solves the request.
- Do not modify unrelated files.
- Do not make destructive Git operations or rewrite history unless the user explicitly asks.
- Do not commit, push, merge, or open a PR unless the user explicitly asks.
- Preserve existing APIs and behavior unless the requested change requires otherwise.
- When C/C++ is involved, pay special attention to ownership, lifetime, RAII, undefined behavior, thread safety, races, integer conversions, error handling, ABI/API compatibility, and test coverage.
- Every implementation report must include the exact verification commands that were run and their results.

## Skills

Use the `team-flow` skill for implementation requests that should go through Architect -> Developer -> Reviewer.
