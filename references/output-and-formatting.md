# Output and Formatting

What to print, where, and in what shape. Human-readable output is primary; offer machine-readable
output (`--json`, grep-parseable tables) where it doesn't hurt usability. The reconciled stance on
success output is **context-dependent**: a brief confirmation on a TTY, quiet/data-only when piped.

**When this applies:** deciding what a command prints on success, table/columnar layout, `--json`,
`--plain`, paging, verbosity, or keeping output script-stable.

## Principles

- **Human-readable first; detect each output stream's TTY status** as a formatting hint, not proof a human is present. Assume any program's output may become another program's input.
- **Primary data → stdout; messaging → stderr**. See
 [streams-and-piping.md](streams-and-piping.md).

## Decision rules

### Success output — recommended: context-dependent
- **On a TTY:** print a brief confirmation that says what changed, especially when the effect isn't
 obvious: `Enabling maintenance mode for myapp... done`.
- **When piped / not a TTY:** stay quiet or emit only the data; follow the Unix rule of silence so
 output composes cleanly.
- This reconciles the "confirm for humans" and "stay silent for scripts" approaches.
```
$ tool deploy myapp            # TTY: human confirmation
Deployed myapp (v42). Run `tool status myapp` to check health.

$ tool deploy myapp | cat      # piped: data/quiet only
v42
```

### Verbosity ladder
- Provide `-q`/`--quiet` (suppress non-essential) and a verbose/debug level; hide developer detail
 (stack traces) unless `--verbose`/`--debug`.
- For `dotnet build`, `-v`/`--verbosity` takes a level: `quiet`, `minimal`, `normal`, `detailed`, or `diagnostic` (for example, `-v diagnostic`). A library sample may configure bare `-v` differently; do not treat that as every .NET tool's contract. Define only the levels your own tool supports.
 Caveat: if `Quiet` + interactive can collide, still show prompts or forbid the combination, or the
 app looks frozen.

### Machine-readable output
- **Offer `--json`** for structured data and scripting; `--plain`/`--terse` for tabular
 text when rich formatting would break one-record-per-line parsing.
- **Human tables should be grep-parseable** (aligned columns, one record per line) even before
 `--json` exists:
```
$ tool regions
ID         LOCATION                 RUNTIME
eu         Europe                   Common
us         United States            Common
$ tool regions | grep Common      # works because layout is line-oriented
```
- **Preserve the promised machine-output contract.** Adding fields or columns can break positional parsers, strict schemas and exact snapshots. Document whether unknown fields are allowed; version incompatible changes. Route scripts to stable `--json`/`--plain` formats.

### Density and paging
- Increase scannable density (e.g., `ls -l`-style columns) so users pattern-match.
- A pager shows long output one screen at a time (`less`, `more`); users can also pipe to one
 themselves (`tool log | less`). Use a pager automatically only when output is long
 **and** stdout is a TTY; never page by default in scripts. Honor the `PAGER` env var and
 prefer flags like `less -FIRX` (don't page if it fits one screen, keep color, leave output on exit).
- Tell the user when state changed and how to view current state (`tool status`).

## Windows / PowerShell callouts

- PowerShell consumers can parse JSON with `ConvertFrom-Json`. For text output under Windows PowerShell 5.1, `tool --json | Out-File -LiteralPath out.json -Encoding utf8` chooses UTF-8 with a BOM instead of the default UTF-16LE. Configure native text decoding correctly first. `>` has no `-Encoding` option; PowerShell 7.4+ direct native-stdout redirection preserves bytes.
- Column widths: don't assume 80; read `COLUMNS`/console width and degrade gracefully.

## Edge cases / anti-patterns

- Section-header layouts (blank lines, `===` banners) break `grep` — avoid for data output.
- Don't label stderr lines like a log file (`ERR`/`WARN`) by default; reserve for verbose.
- Emoji/symbols: use sparingly to clarify, never as the only signal (see [accessibility-and-i18n.md](accessibility-and-i18n.md)).

## Do / Don't

- **Do** send data to stdout, status to stderr, and confirm briefly on a TTY.
- **Do** provide `--json` and keep released output stable.
- **Don't** print debug/stack traces by default.
- **Don't** page when stdout isn't a TTY; gate animations on the stream they use (see [progress-and-feedback.md](progress-and-feedback.md)).

## Related

[streams-and-piping.md](streams-and-piping.md) · [color-and-styling.md](color-and-styling.md) · [progress-and-feedback.md](progress-and-feedback.md) ·
[errors-and-exit-codes.md](errors-and-exit-codes.md) · [accessibility-and-i18n.md](accessibility-and-i18n.md)

Sources: [dotnet build options](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) · [PowerShell encoding](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_character_encoding?view=powershell-5.1).
