# Flameshot native Sway capture

A small Arch Linux patch and package recipe for sharp Flameshot screenshots on mixed-scale Sway desktops. Flameshot remains the screenshot editor, including annotations, text, pixelation, copy, save, and pinned images.

Flameshot 14 normally obtains one full-desktop image through the Wayland portal. On a layout with different output scales, that image can already have lost detail on a scale-1 display before Flameshot crops it. Changing the final crop's interpolation cannot restore those pixels.

This patch captures a selected Sway output at its physical resolution with `grim -o <output> -` and passes the PNG to Flameshot's existing editor. The normal `flameshot gui` monitor picker stays available; after selection, Flameshot captures that output afresh. If native capture is unavailable, the original capture path remains the fallback.

## Requirements

Arch Linux, Sway/Wayland, `grim`, and Flameshot's build dependencies. The PKGBUILD installs `grim` as a runtime dependency and is based on Arch's Flameshot 14.0.0-1 package recipe. For building, install `base-devel`, `git`, `cmake`, and `qt6-tools`; `makepkg -s` resolves package dependencies.

## Install

Update Arch normally, then build and install the package as a regular user:

```sh
sudo pacman -Syu --needed base-devel git cmake qt6-tools
git clone https://github.com/PumukyDev/flameshot-native-sway.git
cd flameshot-native-sway
makepkg -si
```

Restart any running Flameshot daemon after installation, or log out and back in. Check the installed package with `pacman -Q flameshot`.

To use the optional display-selecting helper, install it separately:

```sh
sudo pacman -S --needed python-pyqt6
install -Dm755 flameshot-active-screen "$HOME/.local/bin/flameshot-active-screen"
```

The helper requires `python-pyqt6` and uses `swaymsg` to inspect active outputs. It chooses an external output by default and falls back to the internal display; `--laptop` chooses the internal display directly. It maps output names to Qt screen numbers dynamically.

## Sway bindings

With `$mod` set to `Mod4`, these examples give Win+Shift+A for the monitor picker, Win+Shift+S for an external output with an internal fallback, and Win+Shift+D for the internal display:

```sway
bindsym --no-repeat $mod+Shift+a exec flameshot gui
bindsym --no-repeat $mod+Shift+s exec ~/.local/bin/flameshot-active-screen
bindsym --no-repeat $mod+Shift+d exec ~/.local/bin/flameshot-active-screen --laptop
```

The selector and pinned images can be floated with the rules in [Sway configuration](docs/sway-configuration.md). Adapt bindings to avoid conflicts in your own config.

This is a locally patched `flameshot` package. See [maintenance](docs/maintenance.md) before updating it or using `IgnorePkg`; installation does not edit `/etc/pacman.conf`. The [technical notes](docs/technical-details.md) describe the capture path and measured results. Upstream Flameshot is licensed under GPL-3.0-or-later; this repository uses the same [license](LICENSE).
