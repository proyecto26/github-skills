---
name: github-writing-templates
description: Templates and style rules for engineering writing on GitHub. Use whenever the user asks to write, draft, fill in, rewrite or review a pull request description, a technical design doc (TDD, spec, design doc, RFC), or a GitHub issue of any kind (bug report, feature request, customer-driven or prioritized request, follow-up, tech debt ticket), and when they want to add PR or issue templates to a repo's .github folder. Use it even when the user only says "open a PR", "write up this bug", "file a ticket for this" or "spec this out" without mentioning a template.
---

# GitHub writing templates

Six templates for the writing that surrounds code on GitHub, plus the style rules that make them worth reading. Everything is GitHub-native: issues and PRs are referenced as `#123`, collapsible sections use `<details>`, and checklists use `- [ ]`.

The shared idea behind all of them: reviewers read the diff and the linked issue already holds the context, so the text only carries what those two don't show. Short and precise beats complete.

## Pick the template

| The user wants | Template |
| -- | -- |
| A pull request description | 1. PR description |
| A design doc, TDD, spec or RFC before building | 2. TDD / Spec |
| To report broken behavior seen by a user | 3. Bug report |
| To log a customer or stakeholder request that needs prioritizing | 4. Prioritized request |
| To propose a new feature with requirements | 5. Feature request |
| A follow-up, tech debt item, or a bug found while working on something else | 6. Generic engineering ticket |

If two fit, prefer the smaller one. A bug an engineer found in the code is a generic ticket (6); a bug a user reported is a bug report (3). If the request is ambiguous between a feature request and a TDD, ask: a feature request says what and why, a TDD says how.

## How to fill one in

1. Gather the facts from what is available: the diff or branch, the linked issue, the conversation. Ask only for what blocks writing. For anything else that is unknown, leave the angle-bracket placeholder in place (for example `<date>`), because an invented date, name or link is worse than a visible gap. In the filled-in output, wrap every placeholder that is left in backticks (`` `<date>` ``): GitHub treats a bare `<date>` as an HTML tag and renders nothing, which hides the gap.
2. Keep the template's headings and their order. Fill in every heading or write "None, because <reason>" so the reader knows it was considered and not forgotten. Optional lines marked "only if" are dropped entirely when they don't apply.
3. Apply the style rules below, then reread the result once and cut anything the diff or the linked issue already says.
4. Output the finished markdown in a single code block or file so it can be pasted into GitHub or passed to `gh pr create --body-file` / `gh issue create --body-file`. Don't wrap it in commentary.

Projects differ. If the repo already has its own `.github` templates or the user names their own fields (team names, approvers, rollout stages, priority tiers), use theirs and keep these style rules.

## Style rules for everything

- Plain sentences, first person where there is an author ("I added", "We store"). Write for a teammate, not for an audience.
- Length follows the size of the change. A small fix is one sentence.
- No bold labels opening a paragraph, and no "Root cause:", "Summary:" or "Overview:" openers. The exceptions are the labels that are part of a template (for example `**Expected:**`).
- State the technical fact without the consequence story. "Stored count can drift across bulk add/remove/import/merge so count rows every time."
- Avoid the tells of generated text: "rather than", chains of ", so ...", "worth noting", "it's important to", em dashes, and three-item lists added for rhythm.
- No links to AI chat sessions and no mention of the tools used to write the code or the text.
- One document is the source of truth. If the approach changes, update the text. An outdated paragraph is worse than a short one.

---

## 1. PR description

```markdown
issue: #<number>   <!-- or "closes #<number>" when merging should close it -->
design: <link>   <!-- only if there's UI -->
flag: `<FEATURE_FLAG>`   <!-- only if it's behind a flag or permission -->

## Description

<One plain first-person sentence per real change: what changed and what the user sees.>

<Optional: one more sentence only for what the diff doesn't show
(deploy order, a known limitation, a decision the reviewer needs to make).>

## Type

- [ ] Bug fix (#<issue>)
- [ ] New functionality
- [ ] Updated functionality
- [ ] Cleanup / Security

## Testing

- [ ] Manual (please describe)
- [ ] Unit
- [ ] None

<Only what the diff doesn't show: commands in order (dry run first), the scope of each run,
operator notes. For backfills and migrations, the exact commands.>

## Screenshots and video

<Required when there's a UI or layout change. Otherwise "None, no UI change.">
```

Rules for PR descriptions:

- Check the boxes that apply (`- [x]`) and leave the others unchecked so the list stays scannable.
- A small fix is one sentence. Don't explain how the code works, repeat the issue, tell the root-cause story, list out-of-scope items, or say "PR X of N". The reviewer reads the code, and the story already lives in the issue.
- When the user supplies a long debugging story, it stays out. If part of it matters for the future, suggest adding it to the issue.
- Screenshots can't be generated: leave a clear placeholder naming what to capture.
- When PRs depend on each other, publish them as a stack: follow the `github-stacked-prs` skill if it is installed.

## 2. TDD / Spec

````markdown
# [#<issue>] <Feature name>

- Team: <team>
- DRI: <name>
- Tech lead: <name>
- Issue: #<number>
- Design: <link, or "None, no UI">
- Feature flag: `<FLAG_NAME>`
- QA type: <e.g. canary, staging sign-off>
- Rollout: <e.g. 1 week staged, 2 weeks staged, instant (say who approved the exception)>
- Status: `Not started`
- Approvers:
  - [ ] <name> - `Not reviewed`

# Context

<What we're building and why, in 2-3 sentences. Start with the new work, not with
what's out of scope. What already exists gets one line. A design screenshot if it helps.>

# Technical Decisions

- **<Area / surface>:**
  - <Decision in one sentence. The constraint that decided it, the shape of the cost, no numbers.>
  - <Rejected alternative and why, next to the decision, if a reviewer would ask about it.>
- **<Another area>:**
  - ...

# Schema and Data Changes

## Database schema changes

```sql
-- the real DDL, following the repo's migration conventions (for example: nullable
-- column first, index built concurrently, NOT NULL in a later migration)
```

## Changes to other data stores

<Search index, cache, queues, object storage. "None, because X" instead of deleting the heading.>

## Migration of existing data

<Plan or commands, order relative to the deploys, why it's safe to re-run.>

# Testing Strategy

- Unit: <the test that would fail if the central mechanism breaks>
- E2E: <flow>

# Monitoring and Alerts

<What proves it works during and after the rollout: threshold, window, and the check
for the riskiest failure mode.>

# Feature Breakdown & Timeline

- Subfeature 1 (PR): <date>
- Subfeature 2 (PR): <date>
- Delivered for QA: <date>
- Rollout day 1 (first cohort, on its own date): <date>

# Product / Design Questions

1. <Who it's for, then the question, then the supporting fact, phrased as impact on the user.
   One decision per question. Say which ones block. Answer inline when it comes back.>

# References

<Design files, issues, PRs, related docs and threads.>

# Appendix

<Rejected approaches, how a decision was reached, corrections worth keeping.>
````

Rules for TDDs:

- Decisions go only under Technical Decisions. Context describes; it doesn't decide.
- Support a decision with the shape of the cost ("one extra query per page load", "grows with list size"), not the measurements. Keep the numbers for when someone asks, or put them in the Appendix.
- The bold area labels in Technical Decisions are part of the template. Everywhere else the no-bold-labels rule applies.
- Fill in every heading or write "None, because ...". Deleting a heading hides whether it was considered.
- No slide deck or second doc alongside the TDD: all decisions live in one place.
- Questions for product or design open by naming who they're for, never use a code block, and are about user impact, not implementation.

## 3. Bug report

```markdown
**Severity**

- [ ] Critical (data loss, security, or blocked with no workaround)
- [ ] High (core flow broken, workaround exists)
- [ ] Medium (secondary flow broken or degraded)
- [ ] Low (cosmetic or minor annoyance)

**Reach**

- [ ] All users
- [ ] Many users or a key customer
- [ ] A few users
- [ ] One report so far

**Expected Behavior**
<What is the user expecting the button or feature to do?>

**Actual Behavior**
<What is the button or feature actually doing?>

**Steps to Reproduce**
1. <step>

**Environment**
<App version or commit, browser or device, account or workspace if relevant.>

**Evidence**
<Screenshots, video (highly recommended), error messages, links to logs or the error tracker.>
```

Check one box in each list. If severity or reach can't be judged from what the user said, leave both lists unchecked and say so in one line after the template.

## 4. Prioritized request

For customer or stakeholder requests that go through prioritization. The scales are defaults; swap in the project's own tiers when the user has them.

```markdown
**Requester**
<Customer, account or stakeholder, with an ID or link if there is one.>

**Request**
<One or two sentences: what they need and why.>

**Impact**
<Product owner fills this in.>

- [ ] 5 (blocks a strategic renewal or launch, or causes churn of a top account)
- [ ] 4 (contracted work, or a blocker for a large account)
- [ ] 3 (meaningful revenue or several accounts attached)
- [ ] 2 (requested by or blocking a small account)
- [ ] 1 (logged, not critical)

**Effort**
<Design and engineering provide the estimate.>

- [ ] Size TBD
- [ ] XS (1-2 days)
- [ ] S (3-4 days)
- [ ] M (1 week)
- [ ] L (2-3 weeks)
- [ ] XL (1 month)
- [ ] XXL (2-3 months)

**Critical Delivery Date**
<A date and what it's tied to: renewal, opt-out, launch deadline. "None" if there isn't one.>

**Target Start Date**
<Set once impact and effort are known.>
```

Impact and Effort belong to other people. Check a box only when the user gives the value; otherwise check "Size TBD" for effort and leave impact unchecked.

## 5. Feature request

````markdown
## Feature Overview

<2-3 sentences: what this feature does and who benefits.>

## Problem Statement

|  |  |
| -- | -- |
| **User Pain Point** | <What problem does this solve?> |
| **Current Workaround** | <How do users handle this today?> |
| **Business Impact** | <Revenue, retention, competitive advantage> |

## User Stories

1. **As a** <role>, **I want to** <action>, **so that** <benefit>

## Requirements

### Functional Requirements

**FR-1: <Requirement name>**

- Description:
- User Flow:
  1. User navigates to...
  2. User clicks...
  3. System displays...
- Acceptance Criteria:
  - [ ] Criterion 1
  - [ ] Criterion 2

## Design

**Design:** <link or TBD>

**UI States:**

- [ ] Loading state
- [ ] Empty state
- [ ] Error state
- [ ] Success state

## Data Model

<New tables or fields needed, or "No database changes".>

```
Table: <table_name>
- field_name (type)
```

## Dependencies

| Type | Details |
| -- | -- |
| **Depends On** | <Issues that must be done first, as #123> |
| **Blocks** | <Issues waiting on this> |
| **External** | <Third-party APIs, approvals> |

## Success Criteria

- [ ] All acceptance criteria met
- [ ] Tests and linting pass
- [ ] Manual QA completed
- [ ] Code reviewed

## Future Considerations

<Out of scope for this issue.>
````

Acceptance criteria are verifiable by someone who didn't write the feature. "Works well" is not a criterion; "Saving with an empty name shows the inline error" is.

## 6. Generic engineering ticket

For follow-ups, tech debt, or bugs found while working on something else. Short sentences and plain text for people to read, with the agent detail hidden in a collapsible section at the end.

```markdown
<One or two sentences: what's wrong or what's missing, and who it affects.>

<Where it shows up: screen, flow, customer or environment if applicable. A link to the PR,
thread or error-tracker item where it came up.>

**Expected:** <what should happen>
**Actual:** <what happens today>

**Done when:**
- [ ] <verifiable criterion>
- [ ] <verifiable criterion>

**Out of scope:** <one line, only if someone would otherwise ask>

<details>
<summary>Agent notes</summary>

- Files: `path/to/file.tsx:123`, `app/models/x.rb:45`
- Root cause / repro: <steps or query>
- Suggested fix: <smallest change>
- Related: #<issue>, #<pr>

</details>
```

Rules for the generic ticket:

- The first line answers "what is it and who does it affect". No headline fragments ("Root cause found.") and no bold labels in the opening text.
- One ticket per decision or problem. Two problems means two tickets that link to each other.
- Technical detail (files, queries, the fix plan) goes in Agent notes so it doesn't crowd the part people read. Keep the blank lines inside `<details>` or GitHub won't render the markdown.
- If it came out of work on another task, link the PR or thread where it was found and keep it out of that task's scope.
- Give the issue a title too: a plain statement of the problem, not the fix.

---

## Installing the templates in a repo

When the user wants the blank templates committed to a repository so GitHub offers them automatically:

- PR template: `.github/PULL_REQUEST_TEMPLATE.md` with template 1 as is. The HTML comments stay as hints because GitHub hides them when rendered.
- Issue templates: one file per type under `.github/ISSUE_TEMPLATE/` (`bug_report.md`, `prioritized_request.md`, `feature_request.md`, `engineering_ticket.md`), each starting with front matter:

  ```markdown
  ---
  name: Bug report
  about: Broken behavior seen by a user
  title: ""
  labels: bug
  ---
  ```

- The TDD is a document, not an issue: put it at `docs/templates/tdd.md` or wherever the repo keeps design docs.
- If the repo already has templates, show the difference and ask before replacing them.
