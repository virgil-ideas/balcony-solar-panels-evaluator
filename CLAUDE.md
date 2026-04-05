# CLAUDE.md

## Project Overview

Balcony Solar Panel Evaluator — a single-file interactive simulator that helps users compare different solar panel mounting strategies on a balcony railing. Built as a standalone HTML file with no build tools, no frameworks, no external dependencies (except a Google Fonts import).

**Live site:** https://virgil-ideas.github.io/balcony-solar-panels-evaluator/
**License:** Apache-2.0

## Architecture

This is a single `index.html` file. Everything — CSS, HTML, JavaScript — lives in one file. Keep it that way. The whole point is zero-build, zero-dependency simplicity that can be hosted on GitHub Pages with no configuration.

### Key Components

**Canvas visualization (side view):** A 2D cross-section showing the balcony railing, tilted solar panel rows, sun position, shadow zones, sun rays, and two ghost comparison panels. Drawn every frame via `requestAnimationFrame`.

**Shading model:** Each row is only shaded by the row directly above it (venetian blind model — no cascade). The formula accounts for a tilted panel face, not a flat vertical wall:

```
shadeFraction = (effectiveOverhang × tan(sunElevation)) / (rowHeight + baseProtrusion × tan(sunElevation))
```

Where `effectiveOverhang = lip of row above - horizontal offset of this row`. High summer sun = more shading (shadow drops DOWN a vertical surface). Low winter sun = less shading (rays sneak under the lip).

**Stepped pyramid:** The slider controls the maximum protrusion (bottom row). Offsets are linearly interpolated from row 1 (flush with railing) to row 5 (max protrusion). Each row's total lip = its extra offset + the base panel protrusion from tilt.

**Ghost panels:** Two dashed comparison overlays drawn behind the main panels:
- Yellow dashed: single large panel at 15° tilt (~26cm protrusion)
- Green dashed: vertical panel flush against railing (0cm protrusion)

Both use tilt correction factors relative to the small panels' 30° tilt: `cos(θ_ghost) / cos(θ_small)` per month.

**Orientation:** An 8-direction compass selector applies a multiplier to all yearly kWh calculations. Factors: S=100%, SE/SW=87%, E/W=65%, NE/NW=45%, N=35%.

### Key Constants

- `PANEL_HEIGHT_CM = 20` → at 30° tilt gives exactly 10cm base protrusion
- `PANEL_TILT_DEG = 30` → tilt from vertical
- `NUM_ROWS = 5`
- `NAMEPLATE_PER_ROW = 375` (15 panels × 25W)
- `PEAK_SUN_HOURS` — monthly average for south-facing ~30° tilt at 45°N (Romania)
- `SUN_ELEVATIONS` — solar noon elevation per month at ~45°N

### Animation System

All state changes are interpolated smoothly via `lerp()` in the `tick()` loop at ~0.08 speed factor. Two state objects:
- `anim` — current interpolated values (what gets drawn)
- `target` — destination values (set by controls)

### Theming

Light/dark mode via CSS variables on `:root` and `body.light`. Canvas colors are handled separately through `isLight` checks in the `draw()` function. Accent colors use the `acc(name)` helper which returns theme-appropriate hex values.

## Working on This Project

### Common modifications

**Adding a new control:** Add HTML in the controls area, CSS for styling, a JS variable + handler function, call `recalc()` and `updateYearly()` from the handler, and if it affects the canvas, the `draw()` function picks it up automatically on the next frame.

**Changing the shading model:** Edit `calcShading()`. The yearly calc in `calcYearlyKwh()` calls it 12 times (once per month), so any changes propagate to the yearly table automatically.

**Adding a new ghost panel:** Add constants (tilt, protrusion, tilt correction array), draw it in the ghost section of `draw()`, add a row to `updateYearly()`, and add a corresponding yearly calc function.

### Things to be careful about

- The `acc()` function must have hardcoded hex strings, not calls to itself. A find-and-replace on hex colors can accidentally break it.
- Canvas drawing order matters — ghosts are drawn before small panels so panels appear on top.
- `railTop`, `railBot`, and `sc` (scale) must be defined before anything that uses pixel positions. The sun position depends on these.
- All animations flow through `target` → `anim` via `tick()`. Never set `anim` directly from controls; always set `target` and let interpolation handle it.

### Testing changes

Open `index.html` in a browser. No build step. Check:
1. All 12 months respond to clicks
2. Play button cycles through months
3. Slider animates the pyramid stepping out
4. Light/dark mode toggles all colors including canvas
5. Orientation buttons update the yearly table
6. Ghost panels are visible and correctly positioned
7. Row shading percentages are uniform (rows 2-5 should be equal when slider is at 10cm)

## Deployment

Push to `main` branch. GitHub Pages deploys automatically from root. The file must be named `index.html`.
