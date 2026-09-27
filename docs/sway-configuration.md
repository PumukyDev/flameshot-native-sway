# Sway configuration

Install the repository's `flameshot-active-screen` as `~/.local/bin/flameshot-active-screen` and make it executable. It needs `python-pyqt6`, `swaymsg`, and the patched Flameshot package.

The helper reads `swaymsg -t get_outputs`, keeps active outputs, and matches their names against Qt's `QGuiApplication.screens()`. It passes the resulting Qt screen number to `flameshot screen --number N --edit`. Without an argument it chooses an external output, falling back to an internal one. `--laptop` selects an internal output even while an external display is active. Internal connector names are recognized by the common `eDP-`, `LVDS-`, and `DSI-` prefixes; edit this list if your hardware uses another name. If no requested output can be resolved, the helper exits with an error rather than capturing an arbitrary screen.

Example bindings with `$mod` set to `Mod4`:

```sway
bindsym --no-repeat $mod+Shift+a exec flameshot gui
bindsym --no-repeat $mod+Shift+s exec ~/.local/bin/flameshot-active-screen
bindsym --no-repeat $mod+Shift+d exec ~/.local/bin/flameshot-active-screen --laptop
```

`flameshot gui` opens the normal monitor selector. The other bindings open the editor directly on the chosen output. Check for conflicting shortcuts in your own config.

These window rules float the monitor selector and make pinned images floating and borderless:

```sway
for_window [app_id="^flameshot$" title="^flameshot$"] floating enable
for_window [app_id="^flameshot$" title="^flameshot-pin$"] floating enable, border none
```

The rules do not make pinned images sticky across workspaces. Validate with `sway -C -c ~/.config/sway/config`, then reload with `swaymsg reload`.
