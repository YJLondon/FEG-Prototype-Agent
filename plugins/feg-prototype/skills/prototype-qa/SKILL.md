---
name: prototype-qa
description: Browser-based functional and visual QA for an FEG coded prototype using the Playwright MCP tools, at the standard phone widths, compared against the Figma key screens. Use after building a prototype version and before presenting it to the designer.
---

# Prototype QA

Run before every review. Fix what you find, then re-run the affected checks. Report what passed, what failed and what was not checked.

Run checks from the selected prototype directory. Keep review artefacts there unless the designer requests another location. Deploying or publishing requires explicit approval.

## 0. Serve

- `npm run build` passes with no type errors.
- Start the dev server from the prototype directory with `npm run dev` and note the local URL. For an explicitly requested on-device review, use `npm run dev -- --host` on a trusted network and include the LAN URL.

## 1. Functional pass (Playwright MCP)

At 390 x 844 (`browser_resize`), walk every step of the agreed flow in `PROTOTYPE.md`:

1. `browser_navigate` to the start route, `browser_snapshot` to get element refs.
2. Perform each user action (`browser_click`, `browser_type`, `browser_press_key`) and confirm the expected state or route via `browser_snapshot`.
3. Check browser back returns to the previous step, deep links load directly, and a refresh keeps persisted state.
4. `browser_console_messages`: no errors or React warnings. `browser_network_requests`: no failed assets.

Record each step as pass or fail with the observed result.

## 2. Visual pass

1. For each key screen, `browser_take_screenshot` at 375 x 812, 390 x 844, 428 x 926 and 440 x 956. Wait for images and fonts to load first, or missing icons will look like a bug.
2. Compare the design-width screenshot side by side with the Figma `get_screenshot` of the same frame. Check spacing, type, colour, radii, icon size and alignment.
3. Where it matters, measure with `browser_evaluate` (`getBoundingClientRect`) against Figma geometry rather than judging by eye.
4. Check the wider widths for the responsive rules: rows fill the width, no stray right gap, pinned-end items stay last, nothing clips.
5. Check motion feels consistent: one timing for each interaction family.

Save screenshots in `prototypes/<slug>/qa/v<version>/`.

## 3. Fallback without Playwright MCP

If the `browser_*` tools are unavailable, write a short Playwright script in the session scratchpad (not in the prototype), run it with `npx playwright` using the cached Chromium, and capture the same screenshots and console output. Tell the designer the MCP was unavailable.

## 4. Limits to state honestly

Headless Chromium is not iOS Safari. Safe-area insets, bar tinting, rubber-band scrolling and touch feel need a real iPhone. List what remains for on-device review. If the designer requests it, enable LAN access as described above and share the LAN URL. If the phone cannot reach the LAN URL, suspect a VPN capturing the local subnet.

## 5. QA summary for the review message

```
Build: pass
Flow: 7/7 steps pass
Console: clean
Visual: 4 widths checked; 2 deviations (listed)
Not checked: on-device iOS Safari
```
