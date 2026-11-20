# FEG Prototype Engineer

A Claude Code plugin for the FEG UX/UI team. It turns Figma screens, briefs or Jira tickets into interactive coded prototypes and refines them through browser QA and designer review.

## Install from the company private GitHub repository

Use a current Claude Code CLI. Each teammate needs read access to the private repository, their own Claude Code sign-in and Git credentials that work without an interactive prompt. If the company requires GitHub SSO, authorise your Git credentials for the organisation.

For HTTPS with GitHub CLI, run `gh auth login` and `gh auth setup-git` first.

```sh
cd /path/to/your/project
claude plugin marketplace add https://github.com/YJLondon/FEG-Prototype-Agent.git
claude plugin install feg-prototype@feg-ux --scope user
```

If your team uses SSH, use `git@github.com:YJLondon/FEG-Prototype-Agent.git` for the marketplace URL and load your company-approved SSH key into your agent first. For GitHub Enterprise Server, use the full URL for your host.

User scope makes the plugin available across your projects. Use `--scope local` from a project to enable it only for you in that project. For a new release, run `claude plugin marketplace update feg-ux` and `claude plugin update feg-prototype@feg-ux`, then start a new Claude Code session.

[Claude Code private marketplace and update documentation](https://code.claude.com/docs/en/plugins/host-marketplace)

## Use in your project

Start the agent as your main session so it can review the flow and ask you questions directly.

```sh
cd /path/to/your/project
claude --agent feg-prototype:feg-prototype-engineer
```

Or run a phase in a regular Claude Code session.

| Skill | Use it to |
| --- | --- |
| `/feg-prototype:prototype-intake` | Assess screens and requirements and propose the user flow |
| `/feg-prototype:prototype-build` | Build or revise the agreed coded prototype |
| `/feg-prototype:prototype-qa` | Verify functionality and visual fidelity in a browser |

Pass the feature, Figma link, ticket or existing prototype path in your prompt. The agent creates new prototypes under `prototypes/<slug>/` in the project you launched it from and records decisions in `PROTOTYPE.md`. Delivery defaults to Level 1. The plugin's ignore rules apply to this marketplace repository. In your own project, exclude any local prototype output you do not intend to commit.

### Connectors and tooling

- For design-led work, use the approved Figma plugin and authenticate through `/mcp` with access to the relevant FEG files. The workflow uses the Figma plugin's design-to-code and write guidance.
- Jira and Google Drive connectors are optional. You can paste a brief or ticket if the connector is unavailable.
- For browser QA, use your approved Playwright MCP setup or the fallback in the QA skill. Use a Node.js version supported by the target project's Vite version.

Each project uses its existing permissions and connector settings. The plugin ships prompts and a workflow reference. The agent requires explicit approval before pushing, deploying, creating repositories or touching production repositories. The dev server defaults to localhost. Request LAN access when you need an on-device review.

## Test locally before sharing

```sh
claude plugin validate ~/Projects/FEG-Prototype-Agent
claude plugin validate ~/Projects/FEG-Prototype-Agent/plugins/feg-prototype
cd /path/to/your/project
claude --plugin-dir ~/Projects/FEG-Prototype-Agent/plugins/feg-prototype --agent feg-prototype:feg-prototype-engineer
```

The root marketplace points to `plugins/feg-prototype`, which contains the manifest, agent, three skills and workflow reference. To test installation instead of loading directly, add the local checkout as the marketplace source, then run the same install command as above. Use an isolated `CLAUDE_CONFIG_DIR` if you want to keep that test out of your usual configuration.

## Smoke test

This dry run exercises design-led intake and stops before any code or Figma write. Replace the placeholders with existing key screens, for example the A-Z Sports frames.

```
Smoke test, dry run. Mode: Design-led. Level 1.
Feature: <e.g. A-Z Sports navigation: browse sports A to Z, pin favourites to the top>
Figma key screens: <Figma link 1>, <Figma link 2>, <Figma link 3>
Brand scope: Fortuna (FTN). Target: iPhone Safari, design width 375.

Do steps 1 to 3 only: read the screens, give me the inventory table with Exists /
Reference / Missing, your sufficiency verdict, your assumptions, and the proposed
numbered user flow. Create prototypes/smoke-test/PROTOTYPE.md with the record.
Do not write to Figma, do not scaffold code, do not install anything.
```

What should happen:
- It names the mode (Design-led) and level (1).
- It loads `figma:figma-design-to-code` before `get_design_context`, and makes no `use_figma` writes.
- It returns an inventory table, a one-line verdict, a numbered flow with node ids, and at most one batched question round.
- `prototypes/smoke-test/PROTOTYPE.md` exists, and nothing else changed.

Optional second stage: reply "Proceed and build V1" to test steps 5 to 7 (scaffold, Playwright QA, review message). Check that it stays at Level 1 and does not push or deploy.

## Prepare a private release

1. Confirm the company repository address, **Private** visibility and the FEG UX/UI team's read access before publishing.
2. Review the release files. Stage only `.claude-plugin/marketplace.json`, `.gitignore`, `CLAUDE.md`, `README.md` and the intended manifest, agent, skills and reference inside `plugins/feg-prototype`. Inspect `git diff --cached --name-only` and `git diff --cached` before committing. Keep credentials, local Claude/MCP settings, sessions, design exports and prototype output outside the release. Git ignore rules do not remove anything already tracked or committed.
3. Bump the version in the plugin manifest for each subsequent release. After approval and publication, have a teammate test a clean install with their own read-only repository access.
