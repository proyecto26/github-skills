# Changelog

## 1.0.0 - 2026-11-26

- Initial release with the `github-writing-templates` skill: six templates (PR description,
  TDD / spec, bug report, prioritized request, feature request, generic engineering ticket),
  the shared style rules, and instructions for installing the blank templates in a repo's
  `.github` folder.
- Multi-host packaging from the same repository:
  - `plugin.json` (repo root): [Agent Plugins 1.0](https://agent-plugins.org/specification)
    portable manifest, read by Cursor and Copilot CLI.
  - `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json`: Claude Code.
  - `.codex-plugin/plugin.json` + `.agents/plugins/marketplace.json`: OpenAI Codex.
- `scripts/check-manifests.sh`: release gate. Fails if `name`, `version` or `description`
  differ across the host manifests, if `plugin.json` has fields outside the Agent Plugins 1.0
  schema, or if the install wiring drifts. Also runs `claude plugin validate` and the Agent
  Plugins 1.0.0 schema check.
- `test/evals.json`: seven eval prompts, one per template plus installing the templates in a repo that already has one.
