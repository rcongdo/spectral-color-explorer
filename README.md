# Spectral Color Explorer

An interactive, single-file educational web app for exploring how spectral reflectance curves produce perceived color.

**[Live Demo →](https://rcongdo.github.io/spectral-color-explorer/spectral-reflectance.html)**

![Spectral Color Explorer](https://rcongdo.github.io/spectral-color-explorer/spectral-reflectance.html)

## What it does

Drag control points on a 380–780 nm reflectance curve and instantly see the resulting color swatch, CIE XYZ, L\*a\*b\*, and hex/RGB values — updated live. Overlay up to three illuminants simultaneously to compare how the same surface appears under different lighting.

## Features

- **Catmull-Rom spline editor** — 11 control points at 40 nm spacing, vertical drag only, 1% snap, arrow-key nudging (±1%, Shift ±5%)
- **6 illuminants** — D65, D50, A, F2, F7, F11; up to 3 active at once, oldest auto-evicted when a 4th is selected
- **Illuminant SPD overlays** — spectral power distributions drawn as muted filled areas behind the reflectance curve
- **Live color output** — XYZ tristimulus, CIE L\*a\*b\*, sRGB hex, large color swatch per illuminant
- **Gamut warning** — badge on swatch when the true color exceeds sRGB gamut
- **Metamerism callout** — highlights when two illuminants produce a perceptual color shift (CIEDE2000 ΔE > 2.0)
- **9 presets** — Flat White, Neutral Gray, Process Cyan/Magenta/Yellow, Black, Red, Green, Blue

## Color math

All computation is pure JavaScript, no libraries:

1. Catmull-Rom spline → 81 reflectance samples at 5 nm intervals
2. XYZ tristimulus integration against CIE 1931 2° CMFs and illuminant SPD
3. Bradford chromatic adaptation to D65
4. XYZ → CIE L\*a\*b\* (CIE 1976)
5. XYZ → linear sRGB (3×3 matrix) → gamma encoding (IEC 61966-2-1)
6. CIEDE2000 for metamerism detection

## Usage

Open `spectral-reflectance.html` in any modern browser. No build step, no server, no dependencies — fully offline-capable.

Minimum viewport width: 900px (curve dragging is impractical on small touch screens).

## File structure

```
spectral-reflectance.html   — complete app (single file)
test.html                   — browser-based test runner (24 assertions)
```
