# Lab Chart — Design Spec
**Date:** 2026-05-26
**Status:** Approved

---

## Overview

Add a CIE L\*a\*b\* visualization panel to the right of the spectral graph. It shows two sub-charts that update whenever the color output cards update (`updateFull()`): an a\*b\* scatter plot (chromaticity plane) and an L\* bar (lightness indicator). Each active illuminant gets a colored marker in both sub-charts, using the same chip color already defined in `ILLUMINANT_CONFIG`.

---

## Layout Change

`#graph-wrap` changes from a block container to a CSS flex row with a small gap (`1rem`). Children:

| Element | Sizing |
|---------|--------|
| `#spectral-graph` (existing SVG) | `flex: 1` — shrinks to fill available space |
| `#lab-chart` (new SVG) | fixed `220px` width |

The existing `#graph-legend` stays below the spectral graph only, unchanged.

Minimum viewport (900px) is unaffected — the spectral graph already scales with its container, and 220px is comfortably within the remaining ~680px.

---

## Lab Chart SVG

**Element:** `<svg id="lab-chart">`, `viewBox="0 0 220 300"`, same dark background `#0a1628` as the spectral graph.

### a\*b\* Scatter (top sub-area)

| Property | Value |
|----------|-------|
| Plot area | 160 × 160 px, top-left at (30, 20) — 30px left margin for b\* label, 20px top margin |
| a\* axis | x — range −80 to +80, left to right |
| b\* axis | y — range −80 to +80, **inverted**: b\*=+80 at top, b\*=−80 at bottom (standard color science orientation — yellow up, blue down) |
| Grid lines | Muted lines at a\*=±40 and b\*=±40 |
| Origin crosshair | Slightly brighter lines at a\*=0 and b\*=0 |
| Axis labels | "a\*" centered below x-axis; "b\*" rotated −90° alongside y-axis, both in `#475569` small font |
| Achromatic reference | Single gray dot (filled `#334155`, stroke `#475569`) at (0, 0) |
| Illuminant dots | One `<circle r="5">` per active illuminant, `fill` = `ILLUMINANT_CONFIG[key].chipColor`, positioned at the illuminant's computed (a\*, b\*) |

### L\* Bar (right of scatter)

| Property | Value |
|----------|-------|
| Position | x = 200, y = 20 (top-right of the plot area) |
| Size | 16 px wide × 160 px tall |
| Fill | SVG `linearGradient`, vertical: black (`#000`) at bottom (L=100% along gradient = L\*=0) → white (`#fff`) at top (L\*=100) |
| Border | 1px stroke `#1e293b` |
| Illuminant ticks | `<line>` 14px wide, `stroke` = `ILLUMINANT_CONFIG[key].chipColor`, `stroke-width="2"`, centered on the bar at the L\* position |
| Label | "L\*" in `#475569` small font, centered above the bar |

### Coordinate mapping helpers (pure functions, no side effects)

```
abToSvg(a, b) → { x, y }   // maps (a*, b*) → SVG pixel coords within plot area
lToSvgY(L)   → y            // maps L* 0–100 → SVG y within the L* bar
```

---

## Rendering Function

```
function renderLabChart(results)
```

- Called from `updateFull()` after `renderColorCards()`.
- Clears and redraws only the dynamic elements (dots and ticks), not the static scaffold (axes, grid, gradient).
- The static scaffold is drawn once in `initLabChart()`, called from the app init sequence alongside `initGraph()`.

---

## Update Timing

| Event | Updates |
|-------|---------|
| Mouseup after drag | `updateFull()` → `renderLabChart()` ✓ |
| Preset load | `updateFull()` → `renderLabChart()` ✓ |
| Reset button | `updateFull()` → `renderLabChart()` ✓ |
| Live drag | `updateSwatchesLive()` only — **no** Lab chart update (Lab not computed during drag) |
| Illuminant toggle | `updateFull()` → `renderLabChart()` ✓ |

---

## File Changes

Single file: `spectral-reflectance.html`

- **CSS:** Add flex layout to `#graph-wrap`; add `#lab-chart` sizing rule.
- **HTML:** Add `<svg id="lab-chart">` inside `#graph-wrap`, after the spectral graph SVG.
- **JS:** Add `initLabChart()`, `renderLabChart(results)`, `abToSvg()`, `lToSvgY()`. Hook into existing `init()` and `updateFull()`.
