# Arguments and Flags

Designing the inputs a command accepts: positional **arguments** (order-sensitive) and named
**flags/options** (order-independent, optionally valued). Prefer flags for clarity, follow the
POSIX-style syntax baseline, reserve short flags for common uses, and never accept secrets on the
command line. Equal-weight notes cover where Windows/.NET diverges from POSIX.

**When this applies:** designing or reviewing how a command takes input — positional args vs flags,
short vs long options, defaults, required vs optional, parsing libraries, or passing secrets.

## Principles

- **Use an argument-parsing library** rather than hand-rolling. Configure and test its help, validation, errors, and optional suggestions; these capabilities depend on the library.
- **Prefer flags to positional arguments.** Flags are explicit, self-documenting, order-
 independent, and easier to evolve without breaking scripts.
 Positional args are fine for one obvious operand or simple multi-file actions.
- **Provide a long form for every flag**, and a short form only for the common ones. Long forms make scripts readable; short forms speed interactive use.

## Decision rules

### Argument vs flag
- One obvious operand, or a list of like things → positional is fine: `rm a.txt b.txt`, `rm *.txt`.
- Two operands with a fixed, well-known order → positional acceptable: `cp <source> <dest>`.
- Anything else (two+ unlike values, optional inputs, future growth) → **use flags**.

```
# Unclear which app is source vs target:
$ tool fork destapp -a sourceapp
# Clear and order-independent:
$ tool fork --from sourceapp --to destapp
```

### Short-flag namespace
- Reserve single letters for frequent options; keep a stable short↔long mapping. Standard names:
 `-a/--all`, `-d/--debug`, `-f/--force`, `-h/--help` (help only — never overload it),
 `-o/--output`, `-q/--quiet`, `-u/--user`, `-v` (pick verbose **or** version), `-n/--dry-run`,
 `--json`, `--no-input`, `--version`.
- In .NET CLI, reserve `-i`=`--interactive`, `-o`=`--output`,
 `-v`=`--verbosity`; minimize short aliases overall.

### Defaults
- **Make the default right for the majority.** Most users never discover flags, so a bad
 default harms most people. Booleans get sensible defaults and need no explicit value
 (`--dry-run`, `--force`).

### Naming the flags
- Lowercase, kebab-case for multi-word (`--additional-probing-path`); be consistent about
 pluralization for multi-valued options; nouns for options, verbs for action commands.

### Reuse standard long-option names
- When an option matches a conventional meaning, use the standard long name
 so user expectations transfer across tools. Common
 ones to reuse rather than reinvent:

 | Option | Conventional meaning |
 | --- | --- |
 | `--help` / `--version` | usage info / version number |
 | `--verbose` / `--quiet` (`--silent`) | more progress output / suppress usual output |
 | `--output` | output file name |
 | `--force` | override/force the operation |
 | `--interactive` | prompt before acting |
 | `--recursive` | operate on directories recursively |
 | `--all` | include all items |
 | `--dry-run` | describe changes without doing them |
 | `--no-color` / honor `NO_COLOR` | disable color (see [color-and-styling.md](color-and-styling.md)) |

 Pick the standard name when the meaning fits; only coin a new name for genuinely new behavior.

## Do / Don't

- **Do** accept options in the documented positions; keep operand order and the subcommand hierarchy meaningful. Test global options before and after subcommands if both forms are supported.
- **Do** validate input early and fail with a clear message (see [errors-and-exit-codes.md](errors-and-exit-codes.md)).
- **Don't** read secrets from flags — they leak into `ps` output and shell history. Use a
 `--password-file`, stdin, or a secret store. See [config-env-and-precedence.md](config-env-and-precedence.md).
- **Don't** overload `-h`; when help is requested, show help and ignore other flags.

## Related

[subcommands-and-command-shape.md](subcommands-and-command-shape.md) · [help-and-usage.md](help-and-usage.md) · [config-env-and-precedence.md](config-env-and-precedence.md) ·
[streams-and-piping.md](streams-and-piping.md) · [errors-and-exit-codes.md](errors-and-exit-codes.md)

For option delimiters, optional values, repeated flags, globbing, and platform parsing differences, read [Option parsing and platforms](option-parsing-and-platforms.md).
