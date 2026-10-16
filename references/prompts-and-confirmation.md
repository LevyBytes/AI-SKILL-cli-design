# Prompts and Confirmation

Asking the user for input or approval without breaking automation. Prompts are a convenience layered
on top of flags — never the only way in — and the strength of a confirmation should scale with how
destructive the action is.

**When this applies:** adding interactive prompts, confirmations before destructive actions, password
entry, or making a command scriptable.

## Principles

- **Never *require* a prompt.** Every prompt must have a non-interactive path via a flag
 or argument, or scripts can't use the command.
- **Only prompt when stdin is a TTY.** If stdin isn't interactive, skip the prompt and
 either use the supplied flag or fail with a clear message naming the flag. See
 [interactivity-tty-and-ci.md](interactivity-tty-and-ci.md).

## Decision rules

### Prompt, but provide an escape hatch
```
$ tool keys add
? Which SSH key? (use arrows)  ~/.ssh/id_ed25519.pub
# Scriptable equivalent — no prompt:
$ tool keys add ~/.ssh/id_ed25519.pub
$ tool keys add --key ~/.ssh/id_ed25519.pub --no-input
```
- Prefer prompting for *missing* input over erroring immediately, when interactive.
- `--no-input` disables prompts and fails if required input or confirmation is missing. `--yes`/`-y` supplies only the confirmations it explicitly documents; it cannot invent missing values. Define how both options interact.

### Confirmation strength scales with danger
- **Mild** (one local file, reversible): often no prompt if the command name is explicit (`delete`);
 optional `--force` to skip any prompt.
- **Moderate** (recursive/remote/bulk change): prompt by default; offer `--dry-run` to preview and a
 flag to skip; require non-TTY callers to pass the skip flag.
- **Severe** (delete an app/server, irreversible remote): make confirmation *hard* — require typing
 the resource name, or `--confirm="name"` so it's still scriptable.
```
$ tool app destroy myapp
This permanently deletes "myapp". Type the app name to confirm: myapp
# Scriptable:
$ tool app destroy myapp --confirm myapp
```
- Watch for **non-obvious** destruction (e.g., lowering a replica count from 10 to 1 deletes 9) —
 treat by real impact, not by how the command reads.

### Passwords / secrets
- Don't echo secret input; turn off terminal echo while typing.
- Don't accept secrets via flags or env vars (they leak); read from a file, a pipe, or a prompt. See [arguments-and-flags.md](arguments-and-flags.md), [config-env-and-precedence.md](config-env-and-precedence.md).

## Windows / PowerShell callouts

- Signal prompting with an `-i`/`--interactive` flag; be cautious
 prompting users who didn't ask for it (scripts). If a quiet/`--verbosity Quiet` mode coexists with
 interactive, still show prompts under `--interactive` or forbid the combination, or the app appears
 frozen waiting for input.
- PowerShell advanced functions opt into `SupportsShouldProcess` and call `$PSCmdlet.ShouldProcess()` for `-WhatIf`/`-Confirm`. `ShouldContinue()` is a separate confirmation; `-Confirm:$false` does not automatically bypass it. For argv CLIs, explicitly implement and document dry-run and confirmation behavior rather than assuming these switches are interchangeable.

## Edge cases / anti-patterns

- **Don't** let a prompt block a pipeline (it will hang in CI) — gate on TTY and offer `--no-input`.
- **Don't** make a severe action a single `y/N` — too easy to fat-finger; require the name.
- Ensure the user can abort: Ctrl-C must work even mid-prompt or mid-network (see
 [interactivity-tty-and-ci.md](interactivity-tty-and-ci.md)).

## Do / Don't

- **Do** provide inputs and documented confirmations through flags or files; keep `--no-input` separate from consent.
- **Do** scale confirmation difficulty to real blast radius.
- **Don't** echo secrets or read them from flags/env.
- **Don't** prompt when stdin isn't a TTY.

## Related

[interactivity-tty-and-ci.md](interactivity-tty-and-ci.md) · [arguments-and-flags.md](arguments-and-flags.md) · [config-env-and-precedence.md](config-env-and-precedence.md) ·
[accessibility-and-i18n.md](accessibility-and-i18n.md)

Sources: [PowerShell confirmation](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-shouldprocess).
