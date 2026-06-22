# CLI Design — Gotchas

Recurring failure modes when relying on the CLI Design reference, and what to do instead. Read alongside `SKILL.md`.

- This skill is design GUIDANCE, not a spec to copy verbatim; adapt each recommendation to the target platform (Unix vs Windows/PowerShell) and the program's existing conventions.
- It is not an argument-parser API reference; verify framework-specific behavior (argparse, click, typer, cobra, clap, commander, oclif) against that framework's own docs.
- When a recommendation conflicts with an established project or platform convention, surface the tradeoff rather than silently overriding it.
- Distinguish interactive/TTY behavior from non-interactive/CI behavior; a UX choice that helps at a terminal can break pipes and scripts.