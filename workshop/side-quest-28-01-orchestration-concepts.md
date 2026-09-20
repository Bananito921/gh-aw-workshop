<!-- page-journey: all -->
<!-- page-adventure: side-quest -->
# Side Quest: Understanding Workflow Orchestration and `dispatch-workflow`

> _Optional: work through this before designing your orchestrator if you want the full mental model for how `dispatch-workflow` composes specialist workflows into a pipeline._

## :dart: What You'll Do

You'll learn why orchestration exists, how the `dispatch-workflow` [safe-output](https://github.github.com/gh-aw/reference/safe-outputs/) works, and how to pick good signals and candidate workflows before you design your own orchestrator.

## :clipboard: Before You Start

- You've read [Orchestrate Multiple Agentic Workflows](28-orchestrate-workflows.md) up to **Design your orchestrator**.

## Why orchestrate instead of building one big workflow?

When a repository needs different kinds of AI work — status reports, PR reviews, cost audits — you can keep each concern in its own focused workflow. An orchestrator connects them: it reads signals from the repository and dispatches the right specialist, instead of one workflow trying to do everything.

<picture>
   <source media="(prefers-color-scheme: dark)" srcset="images/28-orchestrator-routing-dark.svg">
   <source media="(prefers-color-scheme: light)" srcset="images/28-orchestrator-routing-light.svg">
   <img alt="Diagram: an orchestrator workflow reads repository signals and dispatches exactly one specialist workflow, or logs a summary and exits when no condition matches." src="images/28-orchestrator-routing-light.svg">
</picture>

> :thinking: **Predict:** Look at your existing workflows. Which one handles the broadest task? Which handles the narrowest? The broadest is a natural orchestration candidate; the narrowest is a natural specialist.

## How `dispatch-workflow` works

The key primitive is `dispatch-workflow` in [`safe-outputs`](https://github.github.com/gh-aw/reference/safe-outputs/). It lets your orchestrator trigger another workflow in the same repository and optionally pass inputs to it.

```markdown .github/workflows/repo-orchestrator.md
---
safe-outputs:
  dispatch-workflow:
    workflows:
      - daily-status
      - pr-reviewer
    max: 1
---
```

The `workflows` list is an allowlist — your orchestrator can only dispatch workflows named here. The `max: 1` cap prevents one run from triggering many specialists at once.

> [!NOTE]
> `dispatch-workflow` triggers the named workflow with a `workflow_dispatch` event. The specialist runs asynchronously in its own Actions job. Your orchestrator does not wait for it to complete.

## Choose signals and candidates

Before writing any orchestrator brief, decide:

- **What signals will the orchestrator read?** (open issues count, PR age, recent commit activity, or a combination)
- **Which specialist workflows will it activate?** (at most one per run keeps behavior predictable)
- **What condition routes to each specialist?**

A simple decision table helps turn these questions into something you can hand to your agent:

| Signal | Action |
|--------|--------|
| Stale open PRs exist | Dispatch the PR reviewer |
| No status issue created today | Dispatch the daily-status reporter |
| Neither condition | Log a summary and exit |

Keep the table narrow at first — one or two signals and two specialists is enough to prove the pattern. You can add more rows once the first version is working reliably.

## :white_check_mark: Checkpoint

- [ ] You can explain, in one sentence, why an orchestrator is useful once you have more than one workflow
- [ ] You can describe what `workflows:` and `max:` do inside a `dispatch-workflow` safe-output block
- [ ] You wrote your own signal-to-action decision table with at least two rows

**Return to the main adventure:** [Orchestrate Multiple Agentic Workflows](28-orchestrate-workflows.md)
