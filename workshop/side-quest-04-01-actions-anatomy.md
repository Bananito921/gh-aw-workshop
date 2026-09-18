# Side Quest: GitHub Actions Anatomy Primer

> _If workflow YAML still feels like a wall of text, this hands-on primer breaks it into five named parts you'll recognize everywhere._

## :dart: What You'll Do

You'll walk through the core parts of a GitHub Actions workflow file — trigger, job, runner, step, and action — with an annotated example, then label a second workflow yourself to check your understanding.

## :clipboard: Before You Start

- Practice repository is set up from a previous step.
- No tools or credentials needed for this side quest.

## Steps

### Read the quick refresher

A GitHub Actions workflow is a YAML file in `.github/workflows/` that tells GitHub:

- _when_ to run (`on`)
- _what_ to run (`jobs`)
- _how_ each job executes (`steps`)

```text
.github/
  workflows/
    hello.yml   ← each workflow file lives here
```

Annotated example — each comment names the key term (this is a standard Actions workflow, not an agentic workflow):

```yaml .github/workflows/hello-workflow.yml
# Standard GitHub Actions workflow — not an agentic workflow
name: Hello Workflow

on: workflow_dispatch         # trigger: the event that starts this workflow

jobs:
  hello:                      # job: a named group of steps on one machine
    runs-on: ubuntu-latest    # runner: the machine GitHub provisions for this job
    steps:
      - run: echo "Hello from GitHub Actions"   # step: a shell command on the runner
```

<details>
<summary>What is a runner?</summary>

A **runner** is the machine GitHub provisions for each job — fresh and isolated for every run.

```markdown .github/workflows/hello-workflow.md
---
runs-on: ubuntu-latest   # also: windows-latest, macos-latest
---
```

You can also bring a **[self-hosted runner](https://github.github.com/gh-aw/reference/self-hosted-runners/)** for custom hardware or private networks. Agentic workflows use the same hosted runners.

</details>

### Label a sample workflow

The diagram below shows how the five key parts fit together in every workflow file.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="images/04-actions-anatomy-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="images/04-actions-anatomy-light.svg">
  <img alt="GitHub Actions workflow anatomy: trigger, job, runner, steps, and actions shown as nested layers" src="images/04-actions-anatomy-light.svg">
</picture>

Before reading on, label each highlighted part of the workflow below with its type:
`trigger`, `job`, `runner`, `step`, or `action`.

```yaml .github/workflows/hello-workflow.yml
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "All checks passed"
```

Write a label beside each line:

1. `on: [push]`
2. `test:` (the job name under `jobs:`)
3. `runs-on: ubuntu-latest`
4. `uses: actions/checkout@v4`
5. `run: echo "All checks passed"`

<details>
<summary>Reveal the labels</summary>

- `on: [push]` → **trigger** (when this workflow runs)
- `jobs: test:` → **job** (a group of steps that runs on one machine)
- `runs-on: ubuntu-latest` → **runner** (the machine type GitHub provisions)
- `uses: actions/checkout@v4` → **action** (a reusable step from the Actions marketplace)
- `run: echo "All checks passed"` → **step** (a shell command run directly on the runner)

</details>

## :white_check_mark: Checkpoint

- [ ] You can identify `on`, `jobs`, and `steps` in a workflow file
- [ ] You labeled all five parts of the sample workflow above (trigger, job, runner, action, step)
- [ ] You know workflows live in `.github/workflows/`

**Return to the main adventure:** [GitHub Actions in 5 Minutes](04-github-actions-intro.md)
