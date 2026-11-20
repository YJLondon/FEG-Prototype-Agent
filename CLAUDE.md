# FEG Prototype Agent

This project hosts the **FEG Prototype Engineer** agent (`plugins/feg-prototype/agents/feg-prototype-engineer.md`), which builds interactive coded prototypes with designers. Philosophy: Design Enough, Build Early, Refine Together.

## Layout

- `plugins/feg-prototype/agents/feg-prototype-engineer.md`: the agent. Start it with `claude --plugin-dir ./plugins/feg-prototype --agent feg-prototype:feg-prototype-engineer`.
- `plugins/feg-prototype/skills/prototype-intake`, `prototype-build`, `prototype-qa`: the three phase skills.
- `plugins/feg-prototype/references/coded-prototype-workflow-v2.md`: the earlier master prompt the skills were distilled from. Background only; the agent and skills take precedence, and its approval gates are not used.
- `prototypes/<slug>/`: one folder per prototype, each with its own `PROTOTYPE.md` record. Gitignored here; each can become its own repo later, on approval.

## Rules for every session in this project

- Default delivery is Level 1. Never escalate to Level 2 or 3 without an explicit request.
- Never modify original Figma frames. Draft only on a `Prototype Working / <slug>` page.
- Never push, deploy, create repos or touch production repositories without explicit approval for that action.
- Never install external skills, plugins or MCP servers automatically.
- Default stack: React, TypeScript, Vite, CSS Modules, React Router, Motion.
- Load `figma:figma-use` before `use_figma`, and `figma:figma-design-to-code` before `get_design_context`.
- FEG design: brand-scoped NDS assets only (for example FTN for Fortuna), tokens or styles instead of raw hex, whole pixel values, one motion spec per interaction family.
- Delivered copy avoids em dashes, en dashes, middots, the ellipsis character and curly quotes. British English.

## Reference Materials

Before building a prototype, review relevant materials in the plugin’s references/ directory.

Use coded-prototype-workflow-v2.md for proven prototype implementation lessons.

The agent's current workflow and approval rules take precedence over legacy reference documents.
