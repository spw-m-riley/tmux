# tmux configuration

This repository contains a single `.tmux.conf` focused on fast pane/session navigation, Vim-style behavior, and a themed status line with plugin-powered extras.

## What this config does

- Changes the primary prefix from `Ctrl-b` to `Ctrl-a` (while keeping `Ctrl-b` as a secondary prefix).
- Adds pane/window/session shortcuts that open in the current pane path.
- Provides a session picker on `prefix + s`/`prefix + S` using `tmux-fzf` when available, with `choose-tree` fallback.
- Enables vi-style copy mode, mouse support, activity monitoring, window indexing from `1`, and automatic window renumbering.
- Sets terminal/truecolor defaults and undercurl support.
- Configures clipboard passthrough and enables `set-clipboard` on tmux `>= 3.2`.
- Loads plugins for theme, navigation, fuzzy finding, searching, clipboard, open/yank helpers, and status metrics.
- Uses Catppuccin (mocha) status styling and places the status line at the top.

## Included plugins

Configured through `tpack`'s TPM-compatible plugin declarations:

- `catppuccin/tmux`: theme and status modules
- `christoomey/vim-tmux-navigator`: pane navigation between tmux and Vim
- `tmux-plugins/tmux-prefix-highlight`: active prefix indicator
- `sainnhe/tmux-fzf`: fuzzy launcher/switcher workflows
- `tmux-plugins/tmux-copycat`: pane text search helpers
- `laktak/extrakto`: extract/select text from pane output
- `tmux-plugins/tmux-open`: open file paths and URLs
- `tmux-plugins/tmux-yank`: improved copy-to-system-clipboard behavior
- `tmux-plugins/tmux-battery`: battery status module
- `tmux-plugins/tmux-cpu`: CPU/memory status module

## Prerequisites

- tmux (3.2+ recommended)
- `git`
- `fzf` (recommended for best `tmux-fzf` experience)

## Install and use

1. Copy this config:

   ```bash
   cp .tmux.conf ~/.tmux.conf
   ```

2. Install `tpack` in the path expected by this config (`~/.tmux/plugins/tpm/tpm`):

   ```bash
   git clone https://github.com/tmuxpack/tpack ~/.tmux/plugins/tpm
   ```

3. Start tmux:

   ```bash
   tmux
   ```

4. Install plugins inside tmux with:

   - `prefix + I` (capital i)

5. Reload the config any time with:

   - `prefix + r`

## Keybindings

`prefix` means `Ctrl-a` (primary) unless noted.

### Prefix and sending prefix

- `Ctrl-a`: primary prefix
- `Ctrl-b`: secondary prefix
- `prefix + Ctrl-a`: send literal `Ctrl-a` to the application
- `prefix + Ctrl-b`: send literal `Ctrl-b` to the application

### Pane management

- `prefix + \`: split left/right in current path
- `prefix + -`: split top/bottom in current path
- `prefix + b`: break current pane into its own window (detached)
- `prefix + t`: create a 20% side pane in current path
- `prefix + H/J/K/L`: resize pane left/down/up/right by 5 cells

### Window/session management

- `prefix + c`: new window in current path
- `prefix + s`: session switcher (`tmux-fzf` script if available, else `choose-tree -s`)
- `prefix + S`: session chooser/launcher (`tmux-fzf` script if available, else `choose-tree -s`)
- `prefix + f`: launch `tmux-fzf` (via `TMUX_FZF_LAUNCH_KEY`)
- `prefix + P`: paste buffer

### Copy-mode (vi)

- `v`: begin selection
- `Ctrl-v`: toggle rectangular selection
- `y`: copy selection and exit copy-mode
- `Esc` or `Ctrl-s`: cancel copy-mode
- `Ctrl-h/j/k/l`: move to neighboring panes while in copy-mode

## Configuration reference

This section maps directly to `.tmux.conf` so edits are safer and easier to review.

### Bootstrap and environment

- `TMUX_PLUGIN_MANAGER_PATH` is set to `~/.tmux/plugins`.
- Plugin manager entrypoint is fixed at `~/.tmux/plugins/tpm/tpm`.
- `update-environment` whitelists `DISPLAY`, SSH agent variables, `XAUTHORITY`, and `PATH` so attached clients inherit needed env.

### Interaction model

- Dual-prefix behavior:
  - primary prefix: `Ctrl-a`
  - secondary prefix: `Ctrl-b`
  - both have explicit `send-prefix` bindings.
- Path-aware commands preserve cwd context:
  - splits (`prefix + \`, `prefix + -`, `prefix + t`)
  - new windows (`prefix + c`)
- Session switching keeps a resilient fallback:
  - use `tmux-fzf` session script when present
  - fallback to built-in `choose-tree -s` when absent.
- Copy mode is vi-based (`mode-keys vi`) with custom selection (`v`, `Ctrl-v`, `y`) and pane-nav bindings in copy-mode.

### Plugins and plugin-specific wiring

- Plugins are declared with `set -g @plugin ...` for TPM management.
- Catppuccin status uses:
  - `set -gF status-right` for base modules
  - `set -agF status-right` for appended modules (`cpu`, `battery`)
  - `status-position top` and rounded window style options.
- `tmux-fzf` behavior is controlled through:
  - `TMUX_FZF_LAUNCH_KEY="f"`
  - `TMUX_FZF_OPTIONS` popup sizing and prompt/pointer styling.
- Extrakto popup behavior is set with:
  - `@extrakto_split_direction "p"`
  - `@extrakto_popup_size "75%,90%"`.

### Compatibility and runtime toggles

- Terminal defaults choose `tmux-256color` when available, else `xterm-256color`.
- Truecolor and undercurl/underline color escape sequences are enabled via `terminal-overrides`.
- `set-clipboard on` is enabled only on tmux `>= 3.2` (version-gated with tmux's built-in format comparison).

### Validate config after edits

```bash
tmux -L copilot-check -f "$PWD/.tmux.conf" new-session -d -s copilot-check && tmux -L copilot-check kill-server
```

## Daily workflow quick start

```bash
tmux new -s work
tmux attach -t work
```

Use `prefix + f` to launch `tmux-fzf` (configured by `TMUX_FZF_LAUNCH_KEY="f"`).
