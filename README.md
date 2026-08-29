# Codex 3-Agent Team Starter

A ready-to-copy Codex setup that separates software work into three dedicated custom agents:

- **Architect** — read-only planning and architecture analysis
- **Developer** — implementation, build, and tests
- **Reviewer** — independent read-only review

## Folder structure

```text
AGENTS.md
.codex/
  config.toml
  agents/
    architect.toml
    developer.toml
    reviewer.toml
.agents/
  skills/
    team-flow/
      SKILL.md
```

## Install into a repository

Copy the contents of this folder into the root of your Git repository.

If the repository already has an `AGENTS.md` or `.codex/config.toml`, merge the relevant sections instead of overwriting your existing rules/settings.

Restart/reopen Codex after adding custom agents/skills so the local configuration is reloaded.

## How to use it

From the repository root in Codex, ask for example:

> Use the team-flow workflow. Add retry handling to the connection manager. Have Architect plan it, Developer implement and test it, then a separate Reviewer review the diff. Fix Major findings and re-review before you finish.

Or shorter:

> Use Architect -> Developer -> Reviewer for this change: <your request>

The repository `AGENTS.md` also tells Codex to use this flow by default for non-trivial code changes.

## Why both custom agents and a skill?

The `.codex/agents/*.toml` files are the **actual dedicated agent definitions**. The `team-flow` Skill is the reusable **orchestration playbook** that tells the main Codex agent how to sequence them.

## Model selection

This starter intentionally does **not** pin models, so it is portable across plan/model availability and each subagent can inherit the parent model.

If your plan supports the models and you want an optimized setup, add these lines to the corresponding TOML files:

### Architect — deeper reasoning

```toml
model = "gpt-5.6"
model_reasoning_effort = "high"
```

### Developer — balanced speed/cost

```toml
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
```

### Reviewer — stronger independent review

```toml
model = "gpt-5.6"
model_reasoning_effort = "high"
```

## Important behavior

- Architect and Reviewer are `read-only`.
- Developer is `workspace-write`.
- Reviewer is explicitly forbidden from fixing its own findings; findings go back to Developer.
- The workflow allows up to two automatic fix/re-review cycles by default.
- Build/test commands must be discovered from the repository rather than guessed.
- Nothing commits, pushes, merges, or rewrites Git history unless you explicitly ask.

## First customization to make

Once this works in your real repository, update `AGENTS.md` with repository-specific rules such as:

- build command
- unit/integration test command
- formatter/linter
- C++ standard
- forbidden dependencies/APIs
- module ownership boundaries
- architecture rules

That makes the three agents much more reliable because every role shares the same project-specific ground rules.
