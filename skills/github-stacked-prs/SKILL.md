---
name: github-stacked-prs
description: Step-by-step workflow for splitting work into a stack of small, dependent pull requests on GitHub with the official gh-stack CLI extension. Use whenever the user asks for stacked PRs, a PR stack, dependent or chained pull requests, splitting a large change or a set of connected fixes into small PRs, keeping PRs under a size limit, or linking, rebasing or merging PRs that build on each other. Use it even when the user only says "break this into small PRs" or "these PRs depend on each other" without naming gh stack.
---

# GitHub stacked PRs

A stack is an ordered chain of branches on top of the trunk. Each branch has one pull request based on the branch below it, so a reviewer sees one small diff per PR and the PRs merge from the bottom up.

This skill is the workflow: when to stack, how to prepare the layers, and how to publish them with descriptions the team wrote. The command reference belongs to GitHub's own `gh-stack` skill, which this one installs and defers to.

## Setup, once per machine

Check first, install only what is missing, and say what you installed.

```bash
gh extension list | grep -q 'github/gh-stack' || gh extension install github/gh-stack
gh skill install github/gh-stack --all --agent <agent>   # the official command reference
git config rerere.enabled true                           # remembers conflict resolutions across rebases
```

`gh skill install` takes the agent's id (`claude-code`, `codex`, `cursor`, `github-copilot`, and others listed by `gh skill install --help`). It installs into the project by default; pass `--scope user` to make it available everywhere. The project copy is a set of untracked files: keep it out of the commits unless the team wants it in the repo.

After installing, read the `gh-stack` skill for the exact flags. It is the source of truth for commands, and it changes with the extension.

## When to stack

Stack when a PR depends on another one that is still open: the same function changes twice, a later fix needs an earlier one, or every PR touches a shared file such as the changelog.

Do not stack PRs that are independent. Open them against the trunk so they can be reviewed and merged in any order. A stack forces an order, and a problem in a lower PR blocks everything above it.

## The workflow

1. **Write the issues first.** One issue per problem, using `github-writing-templates` when it is installed. The stack is a list of issues in the order they have to land.
2. **Plan the layers before writing code.** One issue per branch, foundations at the bottom, what depends on them above. Agree a size limit with the user (200 changed lines is a common one) and count tests and changelog lines in it. Show the user the list of layers and wait for a yes.
3. **Build bottom to top, one branch per layer.** Create each branch from the one below it. Each layer has to pass the tests by itself, because it is reviewed and can be merged by itself.

   ```bash
   gh stack init fix/first-layer          # or adopt existing branches: gh stack init a b c
   git add <files> && git commit -m "fix: ..."
   gh stack add fix/second-layer
   ```

4. **Fix a lower layer in its own branch.** Check out the branch that owns the change, commit there, and replay the layers above with `gh stack rebase --upstack`. A fix for layer 2 committed on layer 5 defeats the stack.
5. **Show the PR texts before publishing.** Opening PRs is visible to other people. Give the user the title and description of each PR and wait for approval.
6. **Publish with written descriptions.** `gh stack submit --auto` generates the titles and opens drafts with no description. When the PRs need a real description, create them yourself with the base chained, then link them:

   ```bash
   git push origin fix/first-layer fix/second-layer fix/third-layer
   gh pr create --base main            --head fix/first-layer  --title "..." --body-file pr-1.md
   gh pr create --base fix/first-layer --head fix/second-layer --title "..." --body-file pr-2.md
   gh pr create --base fix/second-layer --head fix/third-layer --title "..." --body-file pr-3.md
   gh stack link 41 42 43              # PR numbers, bottom to top
   ```

   `gh stack link` creates the stack on GitHub from PR numbers, URLs or branch names and prints the stack URL. Give that URL to the user: it is the page a reviewer starts from.
7. **Keep it in sync.** After a review change or a merge, `gh stack sync` fetches, rebases the layers and pushes with `--force-with-lease`.
8. **Merge from the bottom.** `gh stack merge <pr-number> --yes` merges that PR and every unmerged PR below it in one operation. Merging is the user's decision: do it only when they ask, and never with a plain `gh pr merge`.

## Rules

- Every PR body starts with the issue it closes (`closes #<n>`), one issue per PR.
- A shared file that every layer edits, such as a changelog, is a real dependency: keep those PRs in one linear stack so they do not conflict with each other.
- Screenshots and videos cannot be uploaded from the CLI. Leave a placeholder that names what to capture, save the files, and tell the user which file goes in which PR.
- Always pass the non-interactive form of a command (`view --json`, `submit --auto`, `merge <target> --yes`, `init <branch>...`). Run bare, several of them open a prompt or a full-screen view and block.
- With more than one remote, set `git config remote.pushDefault <name>` or pass `--remote`.
- Report what you did not verify. A stack that was pushed and linked has not been merged, and a rebase that ran without conflicts has not been re-tested until the tests ran on the rebased layers.

## If the extension cannot be used

Without `gh stack` (an old `gh`, a restricted machine, or the feature is not available on the repository), chain the PRs by hand: each PR targets the branch below it, and after the bottom PR merges, rebase the next branch onto the trunk and push with `--force-with-lease`. Say that the stack is not linked on GitHub, so the reviewer has to follow the order from the PR list.
