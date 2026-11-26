# GitHub Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-blueviolet)](https://code.claude.com/docs/en/plugins)
[![Agent Plugins](https://img.shields.io/badge/Agent_Plugins-1.0-blue)](https://agent-plugins.org/)
[![Templates](https://img.shields.io/badge/Templates-6-brightgreen)](#templates)

<img src="https://github.com/proyecto26/sherlock-ai-plugin/blob/main/images/claude-mascot-jumping-animated.svg" width="150">

**Skills for the writing that surrounds code on GitHub.** Six templates for pull requests, design docs and issues, plus the style rules that keep them short. Works in Claude Code, OpenAI Codex, Cursor, GitHub Copilot CLI and any agent that reads [Agent Skills](https://agentskills.io/).

The idea behind all of them: reviewers read the diff and the linked issue already holds the context, so the text only carries what those two don't show.

---

## Quick Start

### Installation

#### Option 1: CLI Install (Recommended)

Use [npx skills](https://github.com/vercel-labs/skills) to install the skill directly:

```bash
# Install the skill
npx skills add proyecto26/github-skills

# List available skills
npx skills add proyecto26/github-skills --list
```

#### Option 2: Claude Code Plugin

Install via Claude Code's built-in plugin system:

```bash
# Add the marketplace
/plugin marketplace add proyecto26/github-skills

# Install the plugin
/plugin install github-skills
```

#### Option 3: Clone and Copy

```bash
git clone https://github.com/proyecto26/github-skills.git
mkdir -p .claude/skills
cp -r github-skills/skills/* .claude/skills/
```

#### Option 4: Git Submodule

```bash
git submodule add https://github.com/proyecto26/github-skills.git .claude/github-skills
```

Then reference skills from `.claude/github-skills/skills/`.

#### Option 5: Fork and Customize

1. Fork this repository
2. Change the templates to match your team's fields (approvers, rollout stages, priority tiers)
3. Install from your fork

### Write Your First PR Description

Once installed, describe what you need:

> *"Write the PR description for my branch"*

The skill picks the template, fills it in from the diff and the linked issue, and leaves a visible placeholder for anything it doesn't know.

---

## Templates

All six live in one skill, [`github-writing-templates`](skills/github-writing-templates/SKILL.md).

| # | Template | Use it for |
|:--|:---------|:-----------|
| 1 | **PR description** | A pull request, from a one-line fix to a migration with operator notes |
| 2 | **TDD / Spec** | A design doc, spec or RFC before building |
| 3 | **Bug report** | Broken behavior seen by a user, with severity and reach |
| 4 | **Prioritized request** | A customer or stakeholder request that needs impact and effort |
| 5 | **Feature request** | A new feature with requirements and acceptance criteria |
| 6 | **Generic engineering ticket** | A follow-up, tech debt item, or a bug found while working on something else |

### Style rules

The same rules apply to every template:

- Plain first-person sentences, written for a teammate.
- Length follows the size of the change. A small fix is one sentence.
- State the technical fact without the root-cause story.
- Unknown dates, names and links stay as `<placeholders>`. Nothing is invented.
- No mention of the tools used to write the code or the text.

### Templates in your repo

The skill can also commit the blank templates so GitHub offers them automatically: `.github/PULL_REQUEST_TEMPLATE.md`, one file per issue type under `.github/ISSUE_TEMPLATE/`, and the TDD under `docs/templates/`.

---

## Usage Examples

**"Open a PR for this branch"**
> Writes a short description with the issue link, type and testing checkboxes.

**"Spec this out before I build it"**
> Writes a TDD with every heading filled in or marked "None, because ...".

**"Write up this bug: checkout shows the wrong total after applying a coupon"**
> Writes a bug report with expected and actual behavior, steps and evidence.

**"File a ticket for the N+1 I found in the export job, it's not part of my PR"**
> Writes a generic engineering ticket with the technical detail in a collapsible section.

**"Add PR and issue templates to this repo"**
> Creates the files under `.github/` and asks before replacing existing ones.

---

## Standards

This plugin follows the [Agent Plugins](https://agent-plugins.org/) v1.0.0 packaging standard
(root [`plugin.json`](./plugin.json), validated against the official schema) and its skill follows
the [Agent Skills](https://agentskills.io/) spec. It also ships the Claude Code manifests
(`.claude-plugin/`) and the OpenAI Codex manifests (`.codex-plugin/`, `.agents/plugins/`).
Portable clients read the root `plugin.json`, and each host reads its own folder.

Before a release, run the gate that keeps the manifests in sync:

```bash
bash scripts/check-manifests.sh
```

## Project Structure

```
github-skills/
├── plugin.json                      # Agent Plugins 1.0 manifest (Cursor, Copilot CLI)
├── .claude-plugin/
│   ├── plugin.json                  # Claude Code manifest
│   └── marketplace.json             # Claude Code marketplace
├── .codex-plugin/
│   └── plugin.json                  # OpenAI Codex manifest
├── .agents/plugins/
│   └── marketplace.json             # Codex marketplace catalog
├── skills/
│   └── github-writing-templates/
│       └── SKILL.md                 # The six templates and the style rules
├── scripts/
│   └── check-manifests.sh           # Release gate for the manifests
├── test/
│   └── evals.json                   # Eval prompts for the skill
├── CHANGELOG.md
├── LICENSE
└── README.md
```

---

## 💜 Sponsors

This project is free and open source. Sponsors help keep it maintained and growing.

[**Become a Sponsor**](https://github.com/sponsors/proyecto26) | [Sponsorship Program](https://proyecto26.com/sponsors/)

## 🤝 Contribution

When contributing to this repository, please first discuss the change you wish to make via issue,
email, or any other method with the owners of this repository before making a change.

Contributions are what make the open-source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated** ❤️.

You can learn more about how you can contribute to this project in the [contribution guide](https://github.com/proyecto26/.github/blob/master/CONTRIBUTING.md).

## ⚖️ License

This repository is available under the [MIT License](./LICENSE).

## Happy vibe coding 💯

Made with ❤️ by [Proyecto 26](https://proyecto26.com) - Changing the world with small contributions.

One hand can accomplish great things, but many can take you into space and beyond! 🌌

Together we do more, together we are more ❤️ <img width="150px" src="https://avatars0.githubusercontent.com/u/28855608?s=200&v=4" align="right">
