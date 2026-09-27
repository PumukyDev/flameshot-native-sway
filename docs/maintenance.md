# Maintenance

This repository builds a patched package named `flameshot`, based on Arch's Flameshot 14.0.0-1 PKGBUILD. The version `14.0.0.native_sway1-1` identifies the local patch. The upstream source archive is downloaded and checksum-verified by `makepkg`; it is not stored here. The recipe pins commit `da6121bdd6fe58060fd7c05fb8503520a150b715`: its contents match the source used for the tested Arch build, while the `v14.0.0` tag now points to a later commit. `grim` is a runtime dependency because it supplies native output pixels on Sway.

## Rebuild this version

On an up-to-date Arch system, run `makepkg --cleanbuild -si` from the repository directory. Restart any running Flameshot daemon after a package change. Keep Arch updated normally; do not hold back unrelated packages to preserve an old binary.

## New Flameshot versions

First test whether the official new package already fixes both direct capture and the monitor-picker route. In a mixed-scale Sway session, compare its raw PNG dimensions and stable pixels against `grim -o <output>` on every output. Check edge contrast and exercise editor actions, including Pin image. Test an unpacked candidate in an isolated D-Bus session so the installed Flameshot daemon cannot serve the candidate's requests. Do not install an untested candidate over a working build.

If the issue remains, start from the matching official Arch PKGBUILD. Update the upstream version, source checksum, and local package version here; rebase `native-sway-capture.patch` against the new `ScreenGrabber` code. Review each conflict and build the package with `makepkg --cleanbuild`. Test the built binary and selector on each output before installing the new package with `makepkg -i` or `pacman -U`.

If upstream fixes both routes, remove any `flameshot` entry from `IgnorePkg` while preserving other entries, then install the official package during a full `sudo pacman -Syu flameshot`. Restart the daemon and verify the captures. The Sway helper and bindings can continue to work with the official package, though native quality must be confirmed independently.

## Optional update hold

An optional `IgnorePkg = flameshot` entry under `[options]` in `/etc/pacman.conf` prevents a routine `pacman -Syu` from replacing this build. Add it manually only after checking existing `IgnorePkg` entries and backing up the file. This repository and `makepkg -si` do not edit pacman.conf. Review upstream releases and rebuild the patched package when needed, especially if a system library changes; an update hold is not a substitute for maintenance.
