# Testing CLI UX

How to verify that a CLI's user experience actually holds — using observable, binary checks and real
command output as ground truth rather than opinion. Test the contract (exit codes, stdout/stderr,
TTY vs non-TTY, prompts, help) the same way you'd test any interface.

**When this applies:** writing tests for a CLI's behavior, setting up CI checks, or verifying a UX
change before shipping.

## Principle

- Prefer ground truth over self-assessment: assert against real exit
 codes and captured stdout/stderr, not "looks right." Define the checks before building, and report
 passed, failed, not applicable and not run results honestly.
- Because non-zero exit codes and the stdout/stderr split are the machine contract, they are exactly what tests should pin down. See [errors-and-exit-codes.md](errors-and-exit-codes.md),
 [streams-and-piping.md](streams-and-piping.md).

## What to test (observable, binary)

### The machine contract
- Exit code is `0` on success and the documented non-zero on each failure class; `--help`/`--version`
 exit `0` (see [errors-and-exit-codes.md](errors-and-exit-codes.md), [help-and-usage.md](help-and-usage.md)).
- Data goes to stdout, messages to stderr — assert each stream separately.
```sh
# Run in an isolated test directory; capture both streams from the same invocation.
if tool export >stdout.txt 2>stderr.txt; then rc=0; else rc=$?; fi
test "$rc" -eq 0 || exit 1
head -n 1 stdout.txt | grep -q '^id,' || exit 1
grep -q '^Exporting' stderr.txt || exit 1
```
These assertions assume this fixture promises a CSV header and an Exporting diagnostic. A different command needs its own contract.

### TTY vs non-TTY behavior
- Test stdin, stdout and stderr redirection separately. Redirected destinations receive plain output; non-interactive mode disables prompts and animations even with a pseudo-terminal. Missing required inputs fail fast.
- Drive non-interactive paths in tests by piping/`--no-input`; never let a test hang on a prompt.
```sh
# Run under a harness timeout; --yes supplies only documented confirmations.
if tool deploy --no-input --yes </dev/null; then rc=0; else rc=$?; fi
test "$rc" -eq 0 || exit 1
if NO_COLOR=1 tool status >status.txt; then rc=0; else rc=$?; fi
test "$rc" -eq 0 || exit 1
grep -q '^OK' status.txt || exit 1
```
Also run the color case under a pseudo-terminal and check for color escape sequences; piped output alone cannot prove NO_COLOR is honored.

### Help, errors, suggestions
- `--help` prints to stdout/exit 0; usage error prints to stderr/exit non-zero; unknown command emits
 a "did you mean" suggestion (see [help-and-usage.md](help-and-usage.md) and [discovery-completion-and-docs.md](discovery-completion-and-docs.md)).

### Stability and idempotence
- Released stdout/`--json` honors its compatibility contract; test strict consumers before adding fields and version incompatible changes. Snapshot-test `--json`
 (see [output-and-formatting.md](output-and-formatting.md)). Re-running a command is safe/idempotent where promised.

## Decision rules

- Test the **non-interactive** surface in CI (it's deterministic); reserve manual/recorded checks for
 interactive prompts and animations.
- Snapshot machine output (`--json`, `--plain`); assert on *fields*, not exact human-formatted lines
 (which are allowed to change — see [output-and-formatting.md](output-and-formatting.md)).
- Include an accessibility pass: `NO_COLOR` output still conveys state; a screen-reader/colorblind
 spot check for human output. See [accessibility-and-i18n.md](accessibility-and-i18n.md).

## Windows / PowerShell callouts

- Assert `$LASTEXITCODE` (the native exit code), not `$?`, in PowerShell tests; on
 PowerShell 5.1 stderr output can flip `$?` to `$false` even on exit 0, so keep the exit code
 authoritative (see [errors-and-exit-codes.md](errors-and-exit-codes.md)). Capture native stdout/stderr with care (5.1 wraps
 native stderr as error records). Run the same behavioral suite on both OSes.

## Edge cases / anti-patterns

- **Do** create pseudo-terminal fixtures for terminal behavior; cover all three redirected streams separately and impose timeouts so prompt tests cannot hang.
- **Don't** make semantic checks depend on decoration: use plain mode or strip ANSI. Test color/styling controls separately; `NO_COLOR` need not remove bold or other non-color styling.
- **Don't** let a missing-input case prompt in CI — it will hang.

## Do / Don't

- **Do** assert exit code + stdout + stderr separately, against documented behavior.
- **Do** snapshot `--json` and test `NO_COLOR`/non-TTY paths.
- **Don't** test interactive prompts in a way that can block.
- **Don't** pin human-formatted lines that are allowed to evolve.

## Related

[errors-and-exit-codes.md](errors-and-exit-codes.md) · [streams-and-piping.md](streams-and-piping.md) · [output-and-formatting.md](output-and-formatting.md) ·
[interactivity-tty-and-ci.md](interactivity-tty-and-ci.md) · [accessibility-and-i18n.md](accessibility-and-i18n.md) · [cli-design-review.md](cli-design-review.md)
