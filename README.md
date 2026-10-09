# Speedrun

A collection of AI coding agent skills for Claude Code, Codex, and other coding agents.

Skills package practical workflows and instructions that help an agent handle common development tasks consistently. This repository is intended to make useful skills easy to discover, reuse, and adapt across agents.

See [Project structure](PROJECT_STRUCTURE.md) for the proposed categories based on the AI native software development lifecycle and the conventions for organizing skills.

## Using a skill

Available skills:

| Skill | Category | Purpose |
| --- | --- | --- |
| [create-changelog](skills/07-release/create-changelog/SKILL.md) | Release | Consolidate changes across repositories into a public Markdown changelog draft with a separate private evidence record. |

1. Browse the skill directories and choose a skill that fits your task.
2. Read its `SKILL.md` and follow its instructions.
3. Install or link it using the conventions of your coding agent.

Some agents use different skill discovery paths or metadata. Check each skill's instructions and your agent's documentation before installing it. Where possible, keep the core instructions portable and put agent-specific setup in the relevant location.

## Adding a skill

- Give the skill a clear, descriptive name.
- Include a `SKILL.md` with the task it supports, when to use it, and the workflow to follow.
- Keep instructions focused and reusable across projects.
- Document any required tools, files, or agent-specific setup.

## Supported agents

- Claude Code
- Codex
- Other coding agents that support reusable skills or instruction files

## License

See the license included with each skill or the repository license, if present.
