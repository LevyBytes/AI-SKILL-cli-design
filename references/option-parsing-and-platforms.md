# Option Parsing and Platforms

POSIX-style option parsing and Windows/PowerShell differences for argv-based CLIs.

**When this applies:** implementing option delimiters, optional values, repetition, negation, or file-pattern handling.

## POSIX-style option syntax (the Unix contract)

- Options are a `-` plus a single alphanumeric; `-W` is reserved for vendors.
- Options without arguments may be grouped: `-abc` == `-a -b -c`.
- `--` ends options; everything after is operands even if it starts with `-`.
- Options should precede operands; option order shouldn't matter unless documented mutually exclusive.
- **Optional option-arguments:** by convention, option-arguments should *not* be
 optional, yet real tools ship `--color[=when]`. If you must, attach the value (`--color=auto`) and
 document it; do not rely on a following token. For optional-value flags, accept an explicit word
 like `none` rather than a blank (`ssh -F none`).
- **Support `-`** to mean stdin/stdout in file operands — see
 [streams-and-piping.md](streams-and-piping.md).

## Windows / PowerShell callouts

- Parser behavior belongs to the command and library version, not the OS. Current System.CommandLine supports attached short-option values and explicit Boolean values. Multiple values in one occurrence depend on arity and `AllowMultipleArgumentsPerToken`; test the configuration instead of assuming every .NET command behaves alike.
- Native PowerShell cmdlets use `-Name value` (single dash, PascalCase). For a cross-platform argv CLI, use consistent `--long`/`-x` forms on both OSes.
- Windows tools historically used `/flag`; for portable modern CLIs prefer `--long` everywhere and,
 at most, also accept `/flag` on Windows.

## Edge cases

- A flag that can repeat (`-v -v -v` or `-vvv` for verbosity) — define whether repetition stacks.
- Negatable booleans: offer `--feature`/`--no-feature` for clarity.
- Globbing is expanded by the shell on Unix but **not** by `cmd.exe`/PowerShell the same way — if you
 accept patterns, document whether the program expands them.

## Example

```text
tool inspect --color=auto -- -input.txt
```

The optional value is attached; `--` makes `-input.txt` an operand.

## Do / Don't

- **Do** accept the documented separate argument for required values (`--output file`). For optional values, define an attached form (`--color=auto`) so the next operand stays unambiguous.

## Related

For operand design, defaults, standard option names, and secrets, read [Arguments and flags](arguments-and-flags.md).
For stream operands, read [Streams and piping](streams-and-piping.md).

Sources: [POSIX utility conventions](https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/V1_chap12.html) · [System.CommandLine syntax](https://learn.microsoft.com/en-us/dotnet/standard/commandline/syntax).
