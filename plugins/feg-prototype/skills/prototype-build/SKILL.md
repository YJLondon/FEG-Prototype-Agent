---
name: prototype-build
description: Build and revise an FEG coded prototype in React, TypeScript and Vite from the agreed flow, with the layout shell, responsive, iOS Safari, motion and visual fidelity rules learned on earlier FEG prototypes. Use when scaffolding a prototype, building a screen, or applying designer feedback.
---

# Prototype build

Rules below come from `${CLAUDE_PLUGIN_ROOT}/references/coded-prototype-workflow-v2.md` (sections 3 to 10). Read it for the reasoning or for cases not covered here.

## Stack (default)

Vite + React + TypeScript + CSS Modules + React Router + Motion (`motion` package). Use a different stack only if the project or designer requires it. Scaffold in `prototypes/<slug>/` under the project from which Claude Code was launched with `npm create vite@latest . -- --template react-ts`.

- Design tokens as CSS custom properties in `src/styles/tokens.css`, named after the Figma variables or styles they come from.
- Real routes and browser history so back gestures work.
- Persist state a real product would keep (pins, favourites, selections) in `localStorage` under a `<slug>:` key.
- Test and screenshot tooling stays out of the prototype's dependencies.

## Before code: a short plan, not a gate

In `PROTOTYPE.md`, write in a few lines: the state model (collapse several frames into one screen plus a few state variables, and derive orderings instead of storing them), the route table, and the assets to export. Then build. Only stop to ask if a choice changes what the designer will see.

## Layout shell (iOS Safari lessons)

1. Let the document scroll. Header and tab bar use `position: sticky`, not `fixed`, and no inner `overflow: auto` container. iOS Safari tints its bars from the top and bottom of the scrolling document.
2. Colour the top edge like the header and the bottom edge like the tab bar. Never paint `html` with the header colour.
3. Use `100dvh` and `env(safe-area-inset-bottom)` padding on the tab bar. `viewport-fit=cover` in the viewport meta.
4. No fake status bar. No device frame chosen by screen size; full screen everywhere unless a frame is explicitly requested, then only behind a query parameter.
5. Scroll to top on every route change.

## Responsive

Designs are drawn at one width; phones run 375 to 440.

- Width-spanning rows use fractional sizing that equals the design value at the design width.
- Fixed-height blocks show more items when wider, never fewer rows. Pinned-end items (Add, Edit) stay last.
- Absolutely positioned badges anchor to the edge they hug in the design.
- Distribute space with flex or grid first. No negative gaps.

## Motion

- One transition spec per interaction family, defined once as tokens (`--motion-base`, `--easing-standard`) and reused. Mixed timings read as a bug.
- Reordering uses layout animation. Colour changes tween one layer.
- Avoid exit lag: keep elements mounted and collapse size and opacity together.
- Respect `prefers-reduced-motion`.

## Visual fidelity

1. Use original Figma assets. Export SVGs with `exportAsync` (`SVG_STRING`) or the Figma MCP asset download. Placeholders only where the designer agreed.
2. Measure, do not eyeball: sizes, padding, gaps, radii and text styles come from node geometry or `get_design_context`.
3. Trust geometry over written specs (for example gradient direction from `gradientTransform`). Flag the disagreement.
4. FEG brand scope: use only assets and styles for the project brand (for example FTN for Fortuna). Do not mix brands.
5. Check vertical alignment across rows (a header control lines up with the same control in each row).
6. Every deliberate deviation from the design is listed in the review message.

## Prototype shortcuts (allowed at Level 1)

- Non-core items may route to one representative page.
- Secondary variants may reuse a page's structure with a shallower flow.
- Invented copy, icons or items are fine when the designer gave latitude; list them.

## Revisions

- Make the smallest change that answers the feedback. Keep one motion spec and one token set.
- `npm run build` must pass after every change.
- Bump the version and log the change in `PROTOTYPE.md`.
- Before changing code for "it looks the wrong size on my phone", rule out Safari page zoom and stale cache. For a device-only problem, after one failed fix, build a small diagnostic page (safe-area values, innerHeight, screen size) and ask for the readout.

## Never without explicit approval

Creating a GitHub repo, pushing, deploying to Vercel or anywhere else, or touching a production repository. If publishing is approved later: private repo by default, `vercel.json` SPA rewrite, repo-local commit identity of the Vercel project owner, and confirm the live deploy contains the change before reporting it as published.
