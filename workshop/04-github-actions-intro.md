<!-- page-journey: all -->
<!-- page-adventure: core -->
# GitHub Actions in 5 Minutes

<details>
<summary><b>Already know GitHub Actions?</b> Confirm these three statements and skip ahead:</summary>

- You know workflows live in `.github/workflows/` as YAML files
- You can read `on`, `jobs`, and `steps` keys in a workflow file
- You know each step runs on a GitHub-hosted runner

**→ [Skip to What Are Agentic Workflows?](05-agentic-workflows-intro.md)**
(or [jump to Install gh-aw](06-install-gh-aw.md) if you know both)

</details>

## :dart: What You'll Do

You'll do a fast refresher on the Actions primitives used in this workshop: [triggers](https://github.github.com/gh-aw/reference/triggers/), jobs, steps, and workflow files. After this step, you'll be able to read any classic GitHub Actions workflow file.

## :clipboard: Before You Start

- Practice repository is set up from a previous step.
- No tools or credentials needed for this step.

## Quick Refresher

A GitHub Actions workflow is a YAML file in `.github/workflows/` that tells GitHub:

- _when_ to run (`on`)
- _what_ to run (`jobs`)
- _how_ each job executes (`steps`)

```text
.github/
  workflows/
    hello.yml   ← each workflow file lives here
```

Every workflow file is built from five named parts: **trigger**, **job**, **runner**, **step**, and **action**.

> [!TIP]
> <details>
> <summary><b>Optional Side Quest:</b> Want an annotated example and a hands-on labeling exercise to lock in these five terms?</summary>
>
> Work through [Side Quest: GitHub Actions Anatomy Primer](side-quest-04-01-actions-anatomy.md), then come back here.
>
> </details>

## Why This Matters for Agentic Workflows

Traditional workflows execute a fixed script path. [Agentic workflows](https://github.github.com/gh-aw/introduction/overview/) still use the **same Actions foundation** — same triggers, same runner, same [permissions](https://github.github.com/gh-aw/reference/permissions/) model — but add an AI agent that reads your repository, reasons about it, and writes back through guardrailed outputs.

| Concept | Classic Actions | Agentic Workflow |
|---|---|---|
| Trigger (`on:`) | Same — schedule, push, PR, dispatch | Same |
| Runner (`runs-on:`) | GitHub-hosted or self-hosted | Same |
| Steps | Shell commands you define | AI agent executes a plain-English task brief |
| Output | Files, logs, deploy artifacts | GitHub comments, issues, PRs, or other API writes |

The agentic workflow file you'll author in this workshop is a `.md` file with the same `on:` and `runs-on:` keys you see above, plus a Markdown task brief that tells the AI agent what to do.

## Try it: Explore a real workflow

Open a real workflow file and find the three core building blocks — no terminal or credentials required, just your browser.

1. Open any public repository on GitHub (for example, the [gh-aw-workshop](https://github.com/githubnext/gh-aw-workshop) repository).
2. Click the **Actions** tab.
3. Click any workflow in the left sidebar.
4. Click **View workflow file** (top right of the run list).
5. In the YAML, find and note:
   - The `on:` trigger — what event starts this workflow?
   - One `jobs:` entry — what is the job named?
   - One `steps` item — what command does it run?

## :white_check_mark: Checkpoint

- [ ] You can identify `on`, `jobs`, and `steps` in a workflow file
- [ ] You know workflows live in `.github/workflows/`
- [ ] You explored a real workflow and found its trigger, a job name, and a step command
- [ ] You can describe one way agentic workflows extend classic Actions (same trigger and runner; AI agent replaces fixed shell steps)

<!-- journey: all -->
**Next:** [What Are Agentic Workflows?](05-agentic-workflows-intro.md)
<!-- /journey -->
