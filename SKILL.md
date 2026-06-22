---
name: cli-design
description: >-
  Use when designing, building, reviewing, testing, or improving how a terminal program interacts
  with people - command and subcommand naming, arguments, flags, defaults, prompts and confirmations,
  help and usage text, error messages and exit codes, stdout vs stderr, piping and structured/JSON
  output, color and styling, progress indicators and spinners, quiet/verbose/debug/json modes, TTY
  detection, interactive vs non-interactive/CI behavior, config and environment precedence,
  shell-completion UX, accessibility, and i18n. Also fires when building or reviewing an argument
  parser (argparse, click, typer, cobra, clap, commander, oclif) or a setup/install script's prompts,
  even when the user never says "CLI design" by name. Gives Unix- and Windows/PowerShell-aware
  guidance with worked examples and a review checklist.
covers:
  - stdout
  - stderr
  - secrets
  - --no-input
  - no-color
  - tty
  - arguments
  - flags
  - options
  - positional
  - getopt
  - posix
---

# Command-Line UX Design

Implementation-ready guidance for designing and reviewing terminal user
experiences in scripts and CLIs. Where established practices disagree, the disagreement is
reconciled, not averaged away. Guidance gives **equal weight to Unix/POSIX and
Windows/PowerShell** conventions and flags where they diverge.

## When to use this skill

Reach for this skill whenever a task touches how a command-line program talks to a human or a
script: argument/flag/subcommand design, help and usage text, error messages and exit codes,
stdout/stderr and piping, color, prompts and confirmations, progress, verbosity/quiet/JSON modes,
TTY and CI behavior, configuration precedence, accessibility, or general command-line ergonomics —
including when you are wiring up a parser (argparse/click/typer/cobra/clap/commander/oclif) or a
setup script's prompts.

## How to use it (runtime workflow)

1. Read this file, then open `references/INDEX.md`.
2. Use the **task router** below (or `references/topics.json`) to load only the component(s) relevant
   to the concern at hand — do not read everything.
3. Apply the component's principles, decision rules, and do/don't guidance.
4. When established practices conflict, each component states the reconciliation inline; follow it.
5. For a critique or audit of an existing CLI, run `references/cli-design-review.md` as a checklist
   and report findings with severity.
6. If a needed point is not covered by the digested sources, say so and mark it `TODO` — never invent
   a convention.

## Task router

| If the task is about… | Read |
| --- | --- |
| positional args, flags, options, defaults, secrets on the command line | `references/arguments-and-flags.md` |
| command/subcommand structure, naming, verb-noun vs noun-verb | `references/subcommands-and-command-shape.md` |
| prompting, confirmations, destructive-action safety | `references/prompts-and-confirmation.md` |
| `-h`/`--help`, usage synopsis, man pages, "did you mean" | `references/help-and-usage.md` |
| what/where to print, success output, tables, `--json`, paging | `references/output-and-formatting.md` |
| ANSI color, `NO_COLOR`, themes, Windows VT | `references/color-and-styling.md` |
| error messages, exit codes, `sysexits.h` | `references/errors-and-exit-codes.md` |
| stdout vs stderr, pipes, `-` for stdin/stdout | `references/streams-and-piping.md` |
| spinners, progress bars, responsiveness | `references/progress-and-feedback.md` |
| TTY detection, `--no-input`, CI/non-interactive behavior, signals | `references/interactivity-tty-and-ci.md` |
| flags vs env vars vs config files, precedence, XDG | `references/config-env-and-precedence.md` |
| screen readers, colorblindness, locale, plain output | `references/accessibility-and-i18n.md` |
| testing CLI behavior and UX | `references/testing-cli-ux.md` |
| authoring/discovery of this skill for Codex & Claude | `references/skill-format-and-discovery.md` |
| auditing an existing CLI | `references/cli-design-review.md` |

## Gotchas

Recurring failure modes and what to do instead live in the sibling [GOTCHA.md](GOTCHA.md).

## Verification notes

Validate the package structure (the validator ships with the skill-drafting skill; paths use the `DEVROOT` env var — adjust to your layout):

```powershell
python "$env:DEVROOT\SKILLS\skills\skill-drafting\scripts\validate_skill_package.py" "$env:DEVROOT\SKILLS\skills\cli-design"
```

This package is built and checked with the three-layer method (Spec -> Verifier -> Environment;
see `references/cli-design-review.md`). Ground-truth checks live in `AI/work/verify_cli_design.py`:
every component is listed in `references/INDEX.md`, has exactly one `topics.json` entry, resolves
its cross-links, and contains a summary, a
"When this applies" line, at least one example, and a do/don't or anti-patterns section. Keep those
invariants when editing.

## Reference map

Start at `references/INDEX.md`. Metadata: `references/topics.json` (topic -> file + keywords).

## Inspired by

Includes original content written for this skill, informed by: GNU Coding Standards, POSIX utility conventions, Microsoft .NET command-line guidance, and the Agent Skills (Claude/Codex) skill format.
