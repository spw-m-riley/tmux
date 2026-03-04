# Copilot instructions for this repository

## Build, test, and lint commands

- There is no build system, lint configuration, or automated test suite in this repository.
- Config validation smoke check:
  - `tmux -L copilot-check -f "$PWD/.tmux.conf" new-session -d -s copilot-check && tmux -L copilot-check kill-server`
- Single-test command: not applicable (no test framework is configured).

## High-level architecture

- This repository is a tmux dotfile project; `.tmux.conf` is the behavior source of truth and `README.md` is the user-facing contract.
- `.tmux.conf` is organized as layered configuration:
  1. Environment/bootstrap (`TMUX_PLUGIN_MANAGER_PATH`, terminal defaults, `update-environment`).
  2. Interaction model (prefix remap, pane/window/session keybindings, copy-mode behavior).
  3. Plugin inventory (`set -g @plugin ...` lines for TPM-managed plugins).
  4. Plugin-specific wiring (Catppuccin status modules, `TMUX_FZF_*`, extrakto settings).
  5. Compatibility/runtime toggles (truecolor/undercurl overrides, tmux version gate for clipboard).
  6. Plugin manager entrypoint (`run '~/.tmux/plugins/tpm-redux/tpm'`).

## Key conventions in this codebase

- Keep `~/.tmux/plugins/tpm-redux/tpm` as the plugin manager path and runtime entrypoint; README install instructions assume this exact location.
- Keep pane/window creation commands path-aware by using `-c "#{pane_current_path}"`.
- Preserve dual-prefix behavior (`C-a` primary, `C-b` secondary) and both send-prefix bindings.
- For session switching keys (`s`/`S`), keep the `tmux-fzf` script existence check with `choose-tree -s` fallback.
- Keep Catppuccin status composition pattern (`set -gF status-right` followed by `set -agF status-right`) so plugin modules append correctly.
- When keybindings, plugin list, or install path changes in `.tmux.conf`, update `README.md` in the same change.
- **TMUX_FZF_OPTIONS whitespace**: Never use plain ASCII spaces as visual padding inside `TMUX_FZF_OPTIONS` string values (e.g., in `--prompt` or `--pointer`); they split fzf argument parsing. Use Unicode thin-space (U+2009) for visual padding in prompt/pointer strings.
- **Version comparisons**: Use tmux built-in format arithmetic (`#{>=:#{version},X.Y}`) for version gates. Never introduce a `bc` shell dependency.
- **fzf popup geometry**: All fzf-based popups (tmux-fzf via `TMUX_FZF_OPTIONS`, extrakto via `@extrakto_popup_size`) must use matching geometry (75%×90%) for visual consistency.
- **No redundant session pickers**: Do not suggest or add session-specific fuzzy pickers (e.g., tmux-sessionx). `tmux-fzf` is the designated fuzzy launcher for all panes/windows/sessions/commands; verify any new plugin doesn't duplicate its function.

## MCP server guidance

- Configure a GitHub MCP server for repository workflows (PRs, issues, commits, branches) when sessions need remote repo context.
- Configure GitHub Actions MCP access when troubleshooting CI failures on doc/config changes.
