# Discovery, Completion, and CLI Documentation

Help users discover commands through suggestions, completion, and linked documentation.

**When this applies:** designing typo suggestions, shell-completion UX, man pages, or reference-documentation routes.

### Discovery aids
- **Suggest corrections** on typos/unknown commands ("Did you mean…"), and point to the
 command list: but suggest, don't auto-run, especially for state-changing actions:
```
$ tool buld
Error: unknown command "buld".
Did you mean "build"?  Run `tool help` to list commands.
```
- Provide a support/website link in top-level help; link to web docs from subcommand help.

### Shell completion (UX)
Tab-completion is a discovery aid; design for it even though the generator internals are out of scope.
- Prefer flags over bare positionals partly because they enable better autocomplete —
 e.g. `tool --app <tab><tab>` can complete app names because the parser knows the next value's type. See [arguments-and-flags.md](arguments-and-flags.md).
- Defining the explicit grouping/area command (not just an implicit
 area) gives the parser a place to expose group help and completion; configure and test both. See
 [subcommands-and-command-shape.md](subcommands-and-command-shape.md).
- Ship a `completion` command that generates the shell completion
 script when the parser supports it, so users can opt in
 per shell.

### Documentation beyond `--help`
- Offer both auto-generated **reference** (generated from the parser and checked against the released version) and human
 **documentation**; web docs (searchable/linkable) plus terminal docs/man pages (offline, version-
 matched). Make man pages reachable from the tool (`tool help <cmd>` ≈
 `man tool-cmd`).

## Do / Don't

- **Don't** auto-execute a guessed command.

## Related

For help rendering, synopsis, streams, and exit behavior, read [Help and usage](help-and-usage.md).
For command grouping, read [Subcommands and command shape](subcommands-and-command-shape.md).
