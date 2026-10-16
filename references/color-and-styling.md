# Color and Styling

Using ANSI color and text styling to aid scanning without breaking machines or excluding users.
Color must be an enhancement, never the only signal: disable automatic color off-TTY and on nonempty `NO_COLOR`, keep
palettes small and theme-safe, and handle Windows virtual-terminal mode.

**When this applies:** adding color/bold to output, choosing a palette, or deciding when to turn
color off.

## Principles

- **Color is a redundant enhancement.** Overusing it means nothing stands out; meaning
 must survive with color stripped. See [accessibility-and-i18n.md](accessibility-and-i18n.md).
- **Reserve red/yellow for errors/warnings**; use one or two accent colors plus dim/bold
 for everything else.

## Decision rules

### Automatic color: evaluate each destination separately
- Disable automatic color on a redirected destination. Piping stdout does not imply stderr is redirected. Escape codes can break anchored matches such as `grep '^hi$'`; a plain `grep hi` still matches the text between them.
- Disable automatic color when `NO_COLOR` is set and non-empty.
- Disable automatic color for `TERM=dumb`.
- Honor `--no-color` and any app-specific color settings you document; names such as `COLOR=false` are not a universal convention.
- Document overrides: explicit color settings may override the `NO_COLOR` default. If you support `FORCE_COLOR`, state its priority. `NO_COLOR` concerns color, not all styling; offer a plain mode that suppresses decorations too.

```python
def automatic_color(is_tty, env, no_color=False):
    return (is_tty and not env.get("NO_COLOR")
            and env.get("TERM") != "dumb" and not no_color)
```
This is the automatic-mode predicate for one destination; apply documented explicit overrides separately.

### How to emit it
- ANSI sequence = ESC `[` + code + `m`: `\e[31m` red, `\e[1m` bold, `\e[0m` reset; combine bold+red
 for errors.
- Prefer the basic 16 or the fixed 8-bit palette (`\e[38;5;<0-255>m`) for consistent rendering across
 terminals; user themes make exact colors unpredictable, so keep styling simple.
- When you set a background, also set the foreground (light/dark theme safety).

### Don't convey meaning by color alone
- Pair color with a text label or symbol so colorblind users and screen readers get the signal: `OK build passed` / `ERROR build failed`, not green/red dots only.

## Windows / PowerShell callouts

- Modern Windows Terminal and PowerShell render ANSI, but legacy `conhost` needs
 virtual-terminal processing enabled (`ENABLE_VIRTUAL_TERMINAL_PROCESSING`) or ANSI shows as raw
 escapes. Detect capability and fall back to no-color rather than printing garbage.
- PowerShell 7.2+ has `$PSStyle`; older PowerShell has `Write-Host -ForegroundColor`. For a cross-platform
 argv CLI, emit ANSI and gate it on capability detection so the same binary behaves on both OSes.
- `NO_COLOR` is honored cross-platform — respect it on Windows too.

## Edge cases / anti-patterns

- **Don't** use backspace/overstrike "bold" (garbles in editors as `bboolldd`).
- **Do** default CI logs to plain output unless explicitly configured otherwise. Some CI jobs allocate pseudo-terminals; TTY detection alone does not establish an interactive user.
- Hyperlinks (OSC 8) and 24-bit color aren't universal — feature-detect, degrade gracefully.

## Do / Don't

- **Do** make output meaningful in monochrome; add color on top.
- **Do** honor `NO_COLOR`, `--no-color`, and TTY detection consistently across stdout/stderr.
- **Don't** hardcode a background without a foreground.
- **Don't** rely on color as the sole status indicator.

## Related

[output-and-formatting.md](output-and-formatting.md) · [accessibility-and-i18n.md](accessibility-and-i18n.md) · [progress-and-feedback.md](progress-and-feedback.md) ·
[interactivity-tty-and-ci.md](interactivity-tty-and-ci.md)

Sources: [NO_COLOR convention](https://no-color.org/) · [Console stream redirection](https://learn.microsoft.com/en-us/dotnet/api/system.console.iserrorredirected) · [PowerShell terminal styling](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_ansi_terminals?view=powershell-7.5).
