# CLI Design References — Index

Start here. Each component is a self-contained, original guide. Load only what the task
needs (progressive disclosure). Metadata: [topics.json](topics.json) (topic → file + keywords). Guidance gives
equal weight to Unix/POSIX and Windows/PowerShell and flags divergences.

| Component | Open when |
| --- | --- |
| [arguments-and-flags.md](arguments-and-flags.md) | Open when designing or reviewing CLI operands, flag names, defaults, required options, or safe secret input. |
| [option-parsing-and-platforms.md](option-parsing-and-platforms.md) | Open when implementing option parsing, optional values, repeated flags, globbing, or Unix and Windows/PowerShell parsing differences. |
| [subcommands-and-command-shape.md](subcommands-and-command-shape.md) | Open when organizing CLI subcommands, choosing command names or word order, or reviewing command consistency. |
| [prompts-and-confirmation.md](prompts-and-confirmation.md) | Open when adding interactive prompts, confirmation for destructive actions, or ways to run without prompting. |
| [help-and-usage.md](help-and-usage.md) | Open when writing CLI help, usage syntax, or examples, or deciding help output streams and exit status. |
| [discovery-completion-and-docs.md](discovery-completion-and-docs.md) | Open when designing command discovery, typo suggestions, shell completion, man pages, or links to CLI documentation. |
| [output-and-formatting.md](output-and-formatting.md) | Open when choosing CLI output formats, JSON or plain-text modes, tables, paging, verbosity, or success messages. |
| [color-and-styling.md](color-and-styling.md) | Open when adding CLI color or styling, honoring NO_COLOR and TTY detection, or supporting terminal themes and Windows VT. |
| [errors-and-exit-codes.md](errors-and-exit-codes.md) | Open when writing actionable CLI errors, assigning exit codes, or handling reserved and platform-specific exit statuses. |
| [streams-and-piping.md](streams-and-piping.md) | Open when separating stdout and stderr, accepting stdin, using '-' for streams, or handling pipelines and broken pipes. |
| [progress-and-feedback.md](progress-and-feedback.md) | Open when adding progress bars, spinners, status feedback, or failure logs for long-running CLI operations. |
| [interactivity-tty-and-ci.md](interactivity-tty-and-ci.md) | Open when adapting a CLI to terminals, redirected streams, unattended runs, or CI, including TTY detection, --no-input, and signals. |
| [config-env-and-precedence.md](config-env-and-precedence.md) | Open when choosing CLI configuration sources, resolving flag/environment/file precedence, locating config files, or protecting secrets. |
| [accessibility-and-i18n.md](accessibility-and-i18n.md) | Open when making CLI output accessible to screen readers, usable without color, or safe across locales and display widths. |
| [testing-cli-ux.md](testing-cli-ux.md) | Open when testing CLI exit codes, stdout, stderr, JSON output, or behavior in terminals, pipelines, and CI. |
| [cli-design-review.md](cli-design-review.md) | Open when reviewing an existing CLI against a design checklist and reporting findings by severity. |
| [languages.md](languages.md) | Open when you need a language version of this skill's references other than English. |

## Reading paths

- **New CLI from scratch:** subcommands-and-command-shape → arguments-and-flags → help-and-usage →
  output-and-formatting → errors-and-exit-codes; read option-parsing-and-platforms for parsing, and discovery-completion-and-docs for suggestions, completion, or documentation.
- **Reviewing an existing CLI:** start at cli-design-review.md and follow its links.
- **Output/automation focus:** streams-and-piping → output-and-formatting → color-and-styling →
  progress-and-feedback.
- **Safety/automation focus:** prompts-and-confirmation → interactivity-tty-and-ci →
  config-env-and-precedence.

## Source disagreements (reconciled in the components)

- **Command word order:** noun-verb/area-grouping recommended; verb-noun is a noted alternative.
- **Exit codes:** layered model — binary baseline, small documented set, reserved codes, optional
  richer codes (reconciles the minimal and granular approaches).
- **Success output:** context-dependent — confirm on a TTY, quiet when piped (reconciles the
  confirm-for-humans and silent-for-scripts approaches).
