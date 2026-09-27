# Technical details

## Why the portal image is soft

Flameshot 14's Wayland capture path receives a full-desktop image from the portal, then `ScreenGrabber::cropToMonitor()` extracts the chosen monitor. In a measured Sway layout, a 2880×1800 internal display at scale 2 and a 2560×1080 external display at scale 1 produced an 8000×2160 desktop image. The external output occupied 5120×2160 pixels there, although its physical mode was 2560×1080. Its fine detail was already softened in that full-desktop image. Replacing the final `Qt::SmoothTransformation` with `Qt::FastTransformation` did not recover the missing detail.

## Capture path

For `flameshot screen --number N --edit`, the patch resolves Qt screen N to its Wayland output name with `QScreen::name()`. On Qt Wayland in a Sway session, it runs `grim -o <name> -`, decodes the PNG, checks its physical dimensions, sets the Qt device pixel ratio, and sends that pixmap through the normal Flameshot editor. This avoids the full-desktop image for direct capture.

For `flameshot gui`, the existing portal image still supplies the monitor preview. Once a monitor is selected, the selector hides and Flameshot obtains a fresh native image of that output for editing. The editor, its tools, clipboard, save, and Pin image remain Flameshot's code. If the native path cannot run or returns an invalid image, Flameshot uses its original crop/portal path. X11 and other desktops keep their original behavior.

## Measured results

The following results came from the original two-display validation, not a universal performance claim. Each Flameshot capture was bracketed by two direct `grim` captures; comparisons used only pixels unchanged between those grim images. Edge contrast compares adjacent-pixel gradients above 30 on stable pixels. A ratio of 1.000 matches grim.

| Output | Capture | PNG dimensions | Exact stable pixels | Mean absolute error | Edge contrast / grim |
| --- | --- | ---: | ---: | ---: | ---: |
| External, scale 1 | Stock Flameshot | 2560×1080 | 91.682% | 0.972 | 0.682 |
| External, scale 1 | Patched Flameshot | 2560×1080 | 100.000% | 0.000 | 1.000 |
| Internal, scale 2 | Stock Flameshot | 2880×1800 | 100.000% | 0.000 | 1.000 |
| Internal, scale 2 | Patched Flameshot | 2880×1800 | 100.000% | 0.000 | 1.000 |

The patched monitor-picker path on the external output measured 99.923% exact stable pixels, 0.069 mean absolute error, and 0.999 edge contrast. The difference reflects the time between reference and selected capture. Pencil, text, pixelation, copy, save, and Pin image were also exercised in the working build.
