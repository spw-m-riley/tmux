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

Configured through TPM-compatible plugin declarations:

- `catppuccin/tmux`
- `christoomey/vim-tmux-navigator`
- `tmux-plugins/tmux-prefix-highlight`
- `sainnhe/tmux-fzf`
- `tmux-plugins/tmux-copycat`
- `laktak/extrakto`
- `tmux-plugins/tmux-open`
- `tmux-plugins/tmux-yank`
- `tmux-plugins/tmux-battery`
- `tmux-plugins/tmux-cpu`

## Prerequisites

- tmux (3.2+ recommended)
- `git`
- `bc` (used by the version check for clipboard behavior)
- `fzf` (recommended for best `tmux-fzf` experience)

## Install and use

1. Copy this config:

   ```bash
   cp .tmux.conf ~/.tmux.conf
   ```

2. Install TPM in the path expected by this config (`~/.tmux/plugins/tpm-redux/tpm`):

   ```bash
   git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm-redux
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
- `prefix + P`: paste buffer

### Copy-mode (vi)

- `v`: begin selection
- `Ctrl-v`: toggle rectangular selection
- `y`: copy selection and exit copy-mode
- `Esc` or `Ctrl-s`: cancel copy-mode
- `Ctrl-h/j/k/l`: move to neighboring panes while in copy-mode

## Daily workflow quick start

```bash
tmux new -s work
tmux attach -t work
```

Use `prefix + f` to launch `tmux-fzf` (configured by `TMUX_FZF_LAUNCH_KEY="f"`).
