# CLI Design Review Checklist

A binary, severity-labeled checklist for auditing an existing CLI against this skill's guidance. Run
it top to bottom, mark each item pass/fail/n-a/not-run with evidence (real command output), and report
findings by severity. Define the applicable contract first and distinguish observed failures from unexecuted checks.

**When this applies:** reviewing or critiquing an existing CLI, gating a release, or self-auditing a
CLI you just built.

## How to run it

1. For each item, run the command and capture exit code + stdout + stderr (ground truth, not opinion).
2. Mark **pass / fail / n-a / not run** and attach evidence or the actual reason a check was not run.
3. Report findings grouped by severity. Suggested severities:
 - **blocker** — breaks scripting/automation or is unsafe (e.g., prompt hangs CI, exit 0 on failure,
 secret on the command line, destructive action with no guard).
 - **major** — real usability/consistency problem (inconsistent flags, color-only meaning, no
 `--json`, unhelpful errors).
 - **minor** — polish (wording, ordering, density).

## Checklist

### Arguments & flags — see [arguments-and-flags.md](arguments-and-flags.md)
- [ ] Every flag has a long form; common ones have stable short forms; `-h` is help-only. (major)
- [ ] No secrets accepted via flags or env vars. (blocker)
- [ ] Sensible defaults for the majority; booleans need no value. (major)
- [ ] POSIX baseline honored where applicable (`--` ends options; `-` = stdin/stdout); see [option-parsing-and-platforms.md](option-parsing-and-platforms.md). (major)

### Command shape — see [subcommands-and-command-shape.md](subcommands-and-command-shape.md)
- [ ] One consistent word order (noun-verb or verb-noun), never mixed. (major)
- [ ] Flag names/output consistent across subcommands. (major)
- [ ] No catch-all subcommand; no arbitrary prefix abbreviation. (major)

### Help & usage — see [help-and-usage.md](help-and-usage.md)
- [ ] `--help`/`-h` and (for multi-command tools) `tool help <cmd>` work; help → stdout, exit 0. (major)
- [ ] Concise help on missing args; full help leads with examples. (major)
- [ ] Unknown command/typo yields a "did you mean" suggestion; see [discovery-completion-and-docs.md](discovery-completion-and-docs.md). (minor)

### Output — see [output-and-formatting.md](output-and-formatting.md) / [streams-and-piping.md](streams-and-piping.md)
- [ ] Data → stdout, messages → stderr. (blocker)
- [ ] Success output is context-dependent (confirm on TTY, quiet/data when piped). (major)
- [ ] `--json` (or `--plain`) available; human tables are grep-parseable. (major)
- [ ] Released output honors its documented compatibility contract; additional fields are checked against existing consumers. (major)
- [ ] Pager/animation only on a TTY. (minor)

### Color — see [color-and-styling.md](color-and-styling.md)
- [ ] Automatic color respects each destination, nonempty `NO_COLOR`, `TERM=dumb` and documented explicit overrides; plain mode disables styling. (major)
- [ ] Meaning never conveyed by color alone. (major)
- [ ] Windows VT handled or color disabled gracefully on legacy consoles. (minor)

### Errors & exit codes — see [errors-and-exit-codes.md](errors-and-exit-codes.md)
- [ ] Exit `0` only on success; documented non-zero on failure; reserved 126/127/128+N respected. (blocker)
- [ ] Error messages are specific and actionable; no default stack traces. (major)
- [ ] Exit-code table documented. (minor)

### Prompts & interactivity — see [prompts-and-confirmation.md](prompts-and-confirmation.md) / [interactivity-tty-and-ci.md](interactivity-tty-and-ci.md)
- [ ] Inputs and confirmations have documented non-interactive forms; `--no-input` does not grant consent and `--yes` does not supply missing values. (blocker)
- [ ] Prompts only when stdin is a TTY; non-TTY fails fast naming the flag. (blocker)
- [ ] Destructive actions guarded; severity scales with blast radius; secrets not echoed. (blocker)
- [ ] Ctrl-C works mid-network; cleanup is bounded. (major)

### Config — see [config-env-and-precedence.md](config-env-and-precedence.md)
- [ ] Documented precedence (flags > env > project > user > system). (major)
- [ ] Standard locations (XDG / `%APPDATA%`); config validated as untrusted input. (minor)
- [ ] No consent-free edits to files the tool doesn't own. (major)

### Progress — see [progress-and-feedback.md](progress-and-feedback.md)
- [ ] Prompt feedback before slow work; progress gated on stderr capability and explicit plain/non-interactive policy. (major)
- [ ] Failure logs surfaced on error. (major)

### Accessibility & i18n — see [accessibility-and-i18n.md](accessibility-and-i18n.md)
- [ ] Output meaningful with `NO_COLOR`; plain/`--json` mode exists. (major)
- [ ] Descriptive text (no "it failed"); not dependent on visual scanning. (major)
- [ ] No assumption of 80 columns; emoji/CJK width handled in tables. (minor)

### Testing — see [testing-cli-ux.md](testing-cli-ux.md)
- [ ] Non-interactive paths covered in CI; exit/stdout/stderr asserted separately. (major)
- [ ] `--json` snapshotted; `NO_COLOR`/non-TTY tested; PowerShell uses `$LASTEXITCODE`. (minor)

## Do / Don't (for the reviewer)

- **Do** capture real exit code + stdout + stderr as evidence for each item; opinion is not a finding.
- **Do** sort findings by severity (blocker → major → minor) and lead with blockers.
- **Do** test the non-interactive path (pipe / `--no-input`) so the review itself can't hang.
- **Don't** mark an item pass without running it; **don't** file style nits as blockers.
- **Don't** penalize human-output wording changes that are explicitly allowed to evolve (see
 [output-and-formatting.md](output-and-formatting.md)).

## Related

[../SKILL.md](../SKILL.md) · [INDEX.md](INDEX.md) · every component file referenced above.
