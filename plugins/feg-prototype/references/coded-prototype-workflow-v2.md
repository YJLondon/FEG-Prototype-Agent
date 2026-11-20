# High-Fidelity Coded Prototype from Figma: Master Prompt v2

Distilled from the A-Z Sports prototype (Sept to Oct 2026). Fill in the PROJECT INPUTS block, then hand
the whole file to a fresh agent. Every rule below exists because something went wrong without it.

The work has two parts, and Part B never starts before Part A is signed off:

- **Part A: verify the material.** In Figma, complete the flow, confirm every screen and element exists
  or has a reference, and close every gap with the designer.
- **Part B: build the prototype.** Code it from the signed-off flow.

Changes from v1: added Part A. Part B Phase 0 now starts from the verified flow instead of re-deriving it.

---

## PROJECT INPUTS (fill in before use)

```
Project name:            <e.g. A-Z Sports Navigation>
Core feature:            <the one flow the prototype is built around>
Flow reference:          <Figma link or image of the intended flow diagram>
Design source file:      <Figma link to the screens and assets>
Component spec file:     <Figma link, if specs live in a separate file, else "same file">
Design system:           <name and brand scope, e.g. FEG NDS, Fortuna only, or "none">
Screens in scope:        <list>
Designer / reviewer:     <who signs off Part A>
Flow working page:       <Figma file and page where the verified flow is assembled, e.g. "Prototype Flow">
Target devices:          <e.g. iPhone Safari first, desktop browser for review>
Design width:            <e.g. 375>
Project folder:          <e.g. ~/Desktop/<Name>_Prototype>
GitHub account:          <account that owns the Vercel project>
Publish:                 <Vercel / none for now>
```

---

## 1. Role and goal

You are building a high-fidelity, clickable coded prototype (React + TypeScript + HTML/CSS) that will be
opened on a real phone and shown to stakeholders. It must behave like the real product for the core
feature and look like the Figma design. It is not production code, but it must be clean, fast and free
of dead code.

**Source of truth:**
- The **flow reference defines intended behaviour.**
- The **Figma file defines the available assets and visual spec.**
- Do not let the current Figma structure override the intended flow just because it is easier to build.

---

# PART A: VERIFY THE MATERIAL IN FIGMA

Goal: before any code exists, every screen, state and element the prototype needs is present in Figma,
assembled in flow order, and confirmed by the designer. A missing screen found in Part B costs a rebuild;
found in Part A it costs one question.

## A1. Map the flow

Read the flow reference as a flow diagram, not a set of screenshots. For every arrow, write:
user action, UI response, state change, destination, next available action.
Separate: navigation to a new page, state change on the same page, accordion expand/collapse,
list reordering, active/inactive component states, shortcuts, back navigation.
Decode connectors from their endpoint node IDs, not from where the arrow appears to point.
Do not assume every screenshot is a separate page.

## A2. Inventory screens, states and elements

Build one table. One row per screen or state the flow reaches, including states the reference implies
but does not draw (expanded, collapsed, pinned, empty, loading, selected, the screen after a reorder).

| Flow step | Screen / state | Figma frame (node id) | Status | Elements needed | Elements missing |
|---|---|---|---|---|---|

Status is one of:
- **Exists**: a designed frame matches this screen and state.
- **Reference**: no frame for this exact state, but another frame or component defines its pattern
  (name that reference node). Say exactly what is derived from it.
- **Missing**: nothing in the file defines it.

For each screen, check the elements the flow depends on: every tap target, both states of every toggle,
icons, counts, badges, header and tab bar variants, real copy. An element present in one state but absent
in its paired state counts as missing.

Also check the reverse direction: frames in the file that no flow step reaches. Ask whether they are
out of scope, an alternative, or a step the reference forgot.

## A3. Assemble and complete the flow in Figma

On the flow working page (never on the designer's signed-off frames):

1. Place copies or instances of the existing frames in flow order, left to right per flow, with numbered
   step labels and arrows annotated with the user action.
2. For **Reference** screens, build the state from the referenced pattern using design system components
   scoped to the project brand, and tag the frame `DRAFT / derived from <node>`.
3. For **Missing** screens, either build a best-guess draft from existing patterns tagged
   `DRAFT / needs design`, or an empty placeholder frame tagged `TO BE DESIGNED` with a note of what it must
   contain. Never present a guess as a designed screen.
4. Keep any local components you create on this working page. Use whole pixel values and the design system
   tokens; no hard-coded hex.
5. Settle one flow fully first and get it reviewed before completing the rest the same way.

## A4. Designer review loop

1. Send the designer the inventory table, a screenshot of the assembled flow page and a numbered gap list.
   For every gap, ask one concrete question with AskUserQuestion, recommended answer first
   (for example: "Screen 07 after unpin: does the item return to its alphabetical slot? Yes / No, it stays
   below the pinned group / Other").
2. The designer may fix gaps by hand in Figma. When they say they are done, re-read the file and update
   the table from what is actually there, not from what was planned.
3. Repeat until every row is **Exists**, or **Reference / Draft** explicitly accepted by the designer.

## A5. Gate: material sign-off

Part A is done only when:
- every flow step has a confirmed screen or an accepted reference,
- no element in the table is still missing,
- every content error (typo, wrong icon, duplicate row) has a decision: fix or keep,
- the designer has said the flow page is approved.

Write the final table and decisions into `FLOW.md` in the project folder and link the Figma flow page.
Do not start Part B without this sign-off.

---

# PART B: BUILD THE PROTOTYPE

## 2. Phase 0: plan the build before any code (mandatory)

Do not scaffold, install or write components until the user approves the plan.
Start from `FLOW.md` and the approved flow page. Do not re-derive the flow; if building reveals a gap that
Part A missed, stop and go back to A4 for that screen.

1. **Collapse frames into state.** Several Figma frames are often one screen under a few state variables
   (A-Z had 5 frames that were 1 screen x 2 booleans). Write the state model as TypeScript interfaces
   and derive orderings (for example pinned items first, then the rest alphabetically) instead of storing them.
2. **Route table.** Real URLs and real browser history so the back gesture works without wiring.
3. **Asset inventory.** List every icon, image, logo and text to export, using the content decisions
   already made in Part A.
4. **Ask the open build questions in one round** with AskUserQuestion, recommended option first. Typical
   ones: styling approach, animation library, which screens first.
5. Write the result to `SPEC.md` in the project folder (state model, routes, layout shell, tokens,
   motion table, content table, open questions). Wait for approval.

---

## 3. Default stack

Vite + React + TypeScript + CSS Modules + Motion (framer-motion successor) + React Router.
Design tokens as CSS custom properties in `src/styles/tokens.css`, mapped 1:1 to the Figma variable names.
Persist user-facing state that a real product would keep (pins, favourites) in `localStorage` under a
project-prefixed key. Keep test tooling (Puppeteer and similar) in a scratch folder, not in the project.

---

## 4. Layout shell rules (learned the hard way)

1. **Let the document scroll.** Header and tab bar use `position: sticky`, not `fixed`, and not an inner
   `overflow: auto` container. iOS Safari tints its status bar and bottom toolbar from the top and bottom
   of the scrolling document. With an inner scroll container the bars stay the system colour no matter
   what. `theme-color` is ignored on recent iOS, and `safe-area-inset-*` reads 0 in Safari even with
   `viewport-fit=cover`.
2. **Colour each edge separately.** The top edge should match the header, the bottom edge the tab bar.
   Never paint `html` with the header colour or it shows below the tab bar on overscroll.
3. Use `100dvh`, and `env(safe-area-inset-bottom)` padding on the tab bar.
4. **No fake status bar** (time, signal) in the prototype. The phone already has one.
5. **No device frame chosen by screen size.** A rule like "width >= 420 and height >= 850 means desktop"
   caught a 428 x 926 iPhone and framed it. Full screen everywhere unless the user asks for a desktop frame,
   and then only behind an explicit query parameter.
6. **Scroll to top on every route change.**
7. **Responsive from the start.** Designs are drawn at a fixed width, but phones vary (375 to 440).
   - Width-spanning rows use fractional sizing that computes to the design value at the design width,
     so tiles scale to fit instead of leaving empty space on the right.
   - Fixed-height blocks (for example 3 rows of chips) show more items when wider, never fewer rows.
   - Pinned-end items (an Add/Edit tile) stay last however many items fit.
   - Absolutely positioned decorations (badges, counters) anchor to the edge they hug in the design,
     not a fixed left offset.
   - Distribute space with flex/grid first, proportional sizing second. No negative gaps to force maths.

---

## 5. Motion rules

1. **One transition spec per interaction family**, set once as tokens (`--motion-base`, `--easing-standard`)
   and reused for every pin, reorder, mode switch and accordion. Mixed timings read as a bug.
2. List reordering uses layout animation. Colour changes tween one layer instead of cross-fading variants.
3. To avoid exit lag, keep elements mounted and collapse width and opacity together, rather than letting
   an exit animation hold the space open.
4. Respect `prefers-reduced-motion`.

---

## 6. Fidelity rules

1. **Use original Figma assets wherever possible.** Placeholders are fine only where the user has said so.
   Asset pipeline: `exportAsync` SVG_STRING for named control, `download_assets` for single logos,
   headless Chrome plus `sips` to rasterise oversized assets.
2. **Measure, do not eyeball.** Read sizes, padding, gaps, radii and text styles from node geometry.
   After building, compare with screenshots side by side and check alignment numerically
   (for example stepper dot centres equal list row centres).
3. **Trust geometry over spec text.** Gradient direction comes from `gradientTransform`, not a written
   description. Flag the doc as wrong when they disagree.
4. Component specs may live in a separate Figma file. Ask for it rather than guessing a component.
5. Vertical alignment across rows matters (a header control must line up with the same control in
   every row below). Check it explicitly.
6. When you knowingly deviate from the design, say so and say why.

---

## 7. Prototype shortcuts

- Non-core items may all route to one representative page (in A-Z every sport opened Football).
- Secondary variants of a page reuse its structure with a shallower flow when the content is thin
  (American Football skipped the region level).
- When the user gives creative freedom on copy, icons or extra items, use it and list what you invented.

---

## 8. Verification

1. `npm run build` must pass after every change.
2. Headless screenshots at 375 x 812, 390 x 844, 428 x 926 and 440 x 956. Wait for images to load
   before capturing, or missing icons will look like a bug.
3. **On-device testing.** Serve on the LAN and give the phone URL. If the phone cannot reach it,
   check for a VPN capturing the local subnet.
4. **For device-specific problems, stop guessing after one failed attempt.** Build a small diagnostic
   page (safe-area values, innerHeight, screen size, standalone mode, colour test strips) and ask the user
   for the readout. The iOS bar colour issue took seven guesses and was solved in one step by measurement.
5. Before changing code for "the size looks wrong on my phone", rule out Safari page zoom
   (per-site zoom once made everything look 85% scale) and stale cache.

---

## 9. Audit before publishing

Remove dead code, unused assets and leftover diagnostics. Check bundle size and that images are
appropriately sized. Report what was removed.

---

## 10. Publish (GitHub + Vercel)

1. Ask before creating the repo, before every push, and about visibility. Default to **private**
   because the prototype contains client brand assets. Vercel Hobby deploys private repos for free.
2. Add `vercel.json` with an SPA rewrite so deep links and refreshes do not 404.
3. **Commit identity must belong to the Vercel project owner**, or Vercel silently blocks the deploy.
   Set a repo-local `user.email` (the owner account's noreply address) and do not change global config.
4. After pushing, confirm the deployment status and check the live site actually contains the change.
   Never report "published" on the push alone.
5. Workflow per change: edit locally, show the user, push on their go-ahead.
6. Record the project folder, repo, live URL and session ID in memory so the project can be found later.

---

## 11. Sync back to Figma (when asked)

Changes made in the prototype (type sizes, spacing, ordering of controls, new controls) may need to go
back to the design file. Before writing to Figma, list every difference found, including ones the user
did not mention, and ask which to sync. Where the prototype and the design system disagree, ask which
is right instead of adding local overrides silently.

---

## 12. Communication

- Reply in the user's language. Be concrete: what changed, which files, what was verified, what was not.
- Ask with AskUserQuestion when a decision is genuinely theirs, recommended option first.
- If the user pushes back with evidence ("our product does this"), re-examine your assumption rather
  than repeating the explanation.
- Delivered text, UI copy and layer names avoid em dashes, en dashes, middots, bullets, the ellipsis
  character and curly quotes.

---

## 13. Close-out

At the end of the project, write a short retrospective: the corrections the user made, rules here that
turned out wrong, gaps that Part A failed to catch, and anything missing. Propose the next version of this
prompt with those changes.
