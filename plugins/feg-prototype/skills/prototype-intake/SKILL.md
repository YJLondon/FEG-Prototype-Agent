---
name: prototype-intake
description: Assess Figma key screens, briefs or Jira tickets for a coded prototype, decide whether they are sufficient, propose the prototype user flow, and draft only the critical missing screens on a separate Figma working page. Use at the start of any FEG prototype, and again when building reveals a missing state.
---

# Prototype intake: assess, propose flow, fill critical gaps

Adapted from Part A of `${CLAUDE_PLUGIN_ROOT}/references/coded-prototype-workflow-v2.md`, without its sign-off gate.

## 1. Gather

Record in `prototypes/<slug>/PROTOTYPE.md`:

```
Name / slug:
Mode:              Design-led | Requirement-led | Hybrid
Core flow:         the one journey the prototype must prove
Figma key screens: links + node ids
Brief / ticket:    link or pasted text
Brand scope:       e.g. Fortuna (FTN) only; NDS brand-scoped assets
Component specs:   same file, or link to separate spec file
Target devices:    default iPhone Safari first, desktop browser for review
Design width:      read from the frames (often 375)
Level:             1
```

Ask only for what you cannot read yourself. If specs may live in a separate Figma file, ask for it rather than guessing a component.

## 2. Read the material

- Figma: `get_metadata` for structure, `get_screenshot` for each key screen, `get_design_context` (after loading `figma:figma-design-to-code`) for the frames you will build. If a flow diagram exists, read connectors by their endpoint node ids, not by where the arrow appears to point.
- Briefs and tickets: extract user goal, steps, rules, states (empty, loading, error, selected), and acceptance criteria.
- Several frames are often one screen in several states. Do not assume every frame is a page.

## 3. Inventory and verdict

One table, one row per screen or state the flow reaches, including states the material implies but does not draw (expanded, empty, selected, after a reorder, error).

| Step | Screen / state | Source (node id or brief section) | Status | Notes |
|---|---|---|---|---|

- **Exists**: a frame matches this screen and state.
- **Reference**: no exact frame, but another frame or component defines the pattern. Name it.
- **Missing**: nothing defines it.

An element present in one state but absent in its paired state counts as missing. Also list frames no flow step reaches and ask if they are out of scope.

Verdict, stated in one line:
- **Sufficient**: build.
- **Sufficient with assumptions**: list each assumption; ask once whether they are acceptable.
- **Blocked**: a critical screen is Missing and no reasonable assumption exists.

A gap is **critical** only if it sits on the core flow and the designer could not judge the prototype without seeing it designed. Everything else is built in code from existing patterns and listed as an assumption.

## 4. Propose the flow

Show the designer a compact numbered flow, for example:

```
1 Home > tap "A-Z"            > A-Z list (frame 12:345, Exists)
2 A-Z list > tap star         > item pinned to top (state of 12:345, Reference: 12:400)
3 A-Z list > tap Football     > Football page (ASSUMPTION: reuse 12:500 layout)
```

Add the assumptions and any Missing items, then ask one AskUserQuestion round. Recommended option first, and always include a way to proceed on your assumptions. When the designer agrees or says to proceed, continue. Write the agreed flow into `PROTOTYPE.md`.

## 5. Draft critical gaps in Figma (only if needed)

Load `figma:figma-use` first. Then:

1. Create or reuse one page named `Prototype Working / <slug>` in the source file. Never edit, move, rename or restyle original frames. Work with duplicates or instances on this page only.
2. Build the missing state from existing patterns and design system components scoped to the project brand. Colour from the brand-prefixed NDS styles or tokens, never raw hex. Whole pixel values. Keep any local components on this working page.
3. Tag each draft frame `DRAFT / derived from <node id>` or `DRAFT / needs design`. Never present a guess as a designed screen.
4. Draft one screen, show it, and only then repeat the pattern for others.
5. Screenshot the result for the designer and record the new node ids in `PROTOTYPE.md`.

If building later reveals another missing state, come back to this skill for that state only.
