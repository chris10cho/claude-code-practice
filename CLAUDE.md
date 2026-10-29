# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository layout

Two independent single-file web apps. Each is one HTML file with inline CSS and JS, no libraries, no build step, no tests, no linter.

- `index.html` + `README.md` (repo root): unit converter (torque, speed, rotational speed, force, pressure).
- `robot-arm/index.html` + `robot-arm/README.md`: 2-link robot arm simulator.

The two apps share nothing. A change to one never needs a change in the other. Keep each app's README in step with its behavior; the READMEs document the math and the user-facing features.

## Running

Open the HTML file directly in a browser. To check behavior headlessly, drive it with Playwright (preinstalled in cloud sessions; `require('playwright')`), dispatching `input` events on the sliders/inputs and reading the DOM text.

## Unit converter (`index.html`)

Data-driven: the `CATEGORIES` object maps each category to units, and each unit is a single factor to the category's base unit (N·m, m/s, rad/s, N, Pa). Conversion is `value × from_factor ÷ to_factor`. Adding a unit means adding one line to `CATEGORIES`; no other code changes.

## Robot arm (`robot-arm/index.html`)

State lives in the DOM inputs (`#t1`, `#t2` sliders, `#l1`, `#l2` lengths). `draw()` re-reads them via `read()` on every call, so anything that changes the pose just sets the slider values and calls `draw()`.

- Conventions: world coordinates are in link-length units, base at (0, 0), Y up. Canvas Y points down, so `draw()` flips it. The drawing scale (`scale`) shrinks for long arms; the click handler uses it to convert mouse position back to world units. Joint 2 is relative to link 1 (link 2's absolute angle is θ1 + θ2).
- `solveIK()` returns both elbow solutions within the ±150° joint 2 limit, or `[]` when the target is outside the reachable ring (`rMinOf()` to L1 + L2). Click-to-move, Play, and the out-of-reach message all depend on it.
- Animation goes through `tween()` (eased 0..1, `requestAnimationFrame`, one shared `anim` handle). `glide()` is a joint-space move; Play's straight-line legs call `poseFor()` (IK with a fixed elbow sign) every frame. Anything that should interrupt motion (slider input, canvas click) calls `stop()`.
- Play travels A ↔ B in a straight line only if `minDist()` of that segment clears the inner hole at `rMinOf()`; otherwise it falls back to joint-space interpolation.
- The trail stores world-space end points, capped at 3000. The "fully stretched" warning triggers at |θ2| < 0.5°.
- Colors come from CSS variables on `:root` with a dark-mode override; the canvas reads them with `css()` on each draw, so new drawing code should use `css("--name")` rather than hardcoded colors.
