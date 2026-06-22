# Command-Line UX Design

> _Implementation-ready guidance for designing, reviewing, and testing how a terminal program talks to people and to scripts._

Part of **[Agent Kaizen](https://github.com/DirectorGunner/agent-kaizen)** — Agent Kaizen is designed to support reliable AI agent workflows, context engineering, and AI systems engineering in VS Code, Codex, and Claude Code. Build reusable agent skills, reduce unnecessary context loading, add validation loops, and apply Spec → Verifier → Environment scaffolding to new and existing projects.

This repository is the `cli-design` skill: a reusable, trigger-rich task handbook that an AI coding agent (OpenAI Codex, Claude Code) loads on demand when a task matches its triggers.

## What this skill covers

This skill covers the full surface of command-line user experience: command and subcommand naming, positional arguments, flags, options, and defaults; prompts, confirmations, and destructive-action safety; help and usage text, error messages, and exit codes; stdout vs stderr, piping, and structured/JSON output; color and styling (`NO_COLOR`, Windows VT), progress indicators and spinners, and quiet/verbose/debug/json modes. It also covers TTY detection, interactive vs non-interactive/CI behavior, config and environment precedence, shell-completion UX, accessibility, and i18n. It fires when you build or review an argument parser (argparse, click, typer, cobra, clap, commander, oclif) or a setup/install script's prompts — even when no one says "CLI design" by name. Guidance gives equal weight to Unix/POSIX and Windows/PowerShell conventions and flags where they diverge, with worked examples and a review checklist.

## What's inside

- `SKILL.md` — frontmatter (`name` + trigger-rich `description`) and a lean body.
- `references/` — right-sized topic files the agent loads only when relevant (plus `INDEX.md` and `topics.json`).
- `GOTCHA.md` — known pitfalls and edge cases.

## Use it

This skill is one git repo inside the Agent Kaizen **skills store**. The store nests two folders on purpose: the outer **`SKILLS\`** is a VS Code project for building and maintaining skills (its own workspace + tooling), and the inner lowercase **`skills\`** holds every skill as its own repo. That split lets a project pull skills two ways — the **whole `skills\` folder at once** (loads everything — **not recommended**) or **one skill at a time** (recommended: load only what a task needs and stay under Claude Code's skill-listing budget).

Paths below use **`%DEVROOT%`** — the `DEVROOT` environment variable pointing at the folder that contains `SKILLS\`. Set it once by running **`SetDevRoot.cmd`** in the SKILLS repo root (no admin); a new shell then resolves `%DEVROOT%` automatically.

Link **just this skill** (recommended) — a Windows directory junction, no admin:

```cmd
mklink /J .agents\skills\cli-design  "%DEVROOT%\SKILLS\skills\cli-design"
mklink /J .claude\skills\cli-design  "%DEVROOT%\SKILLS\skills\cli-design"
```

Or link the **whole store** at once (loads every skill — not recommended outside a skills-dev project):

```cmd
mklink /J .agents\skills  "%DEVROOT%\SKILLS\skills"
mklink /J .claude\skills  "%DEVROOT%\SKILLS\skills"
```

Remove a link (the store copy is untouched):

```cmd
rmdir .agents\skills\cli-design
rmdir .claude\skills\cli-design
```

The agent (OpenAI Codex, Claude Code) then auto-loads this skill whenever a task matches its triggers. Built and validated with **[skill-drafting](https://github.com/DirectorGunner/AI-skill-drafting)** to the Agent Kaizen gold standard.

## License

Licensed under **AGPL-3.0**, matching the [Agent Kaizen](https://github.com/DirectorGunner/agent-kaizen) project.
