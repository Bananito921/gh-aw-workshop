<!-- page-journey: all -->
<!-- page-adventure: side-quest -->
# Side Quest: Pre-Flight Checks Before Your First Run

> _Optional: two 60-second checks that catch the leading cause of `model-access-not-configured` failures before you ever click Run workflow._

## :dart: What You'll Do

You'll verify your workflow's [lock file](https://github.github.com/gh-aw/reference/glossary/#workflow-lock-file-lockyml) is present and current, and confirm your `permissions:` block matches the billing path you chose in [Confirm Model Access](07d-confirm-model-access.md). Both checks take less than a minute and catch the most common reason a first run fails.

## :clipboard: Before You Start

- Completed [Confirm Model Access](07d-confirm-model-access.md)
- `daily-report-status.md` and `daily-report-status.lock.yml` are committed to `.github/workflows/` on `main`

## Steps

### Confirm the lock file is present and current

A stale or missing lock file is the leading cause of `model-access-not-configured` failures at this step. Open `.github/workflows/` in your repository on GitHub and confirm both files are there:

- `daily-report-status.md` (source)
- `daily-report-status.lock.yml` (compiled lock file)

If either file is missing, return to [Write Your First Agentic Workflow](07-your-first-workflow.md) to complete the workflow creation steps. If the lock file is present but you are unsure it is current, recompile and push before continuing:

```bash
gh aw compile
git add .
git commit -m "chore: sync lock file" && git push
```

### Confirm billing configuration matches the lock file

Open `daily-report-status.lock.yml` (or `daily-report-status.md`) and confirm the `permissions:` block matches the billing path you chose in [Confirm Model Access](07d-confirm-model-access.md):

| Billing path | `copilot-requests: write` present |
|---|---|
| Organization centralized billing | Yes |
| Personal billing | No — and `COPILOT_GITHUB_TOKEN` is set in **Settings → Secrets → Actions** |

Any mismatch means returning to [Confirm Model Access](07d-confirm-model-access.md) to fix the configuration and recompile.

## :white_check_mark: Checkpoint

- [ ] I confirmed both `daily-report-status.md` and `daily-report-status.lock.yml` exist on `main`
- [ ] I recompiled and pushed if I was unsure the lock file was current
- [ ] I confirmed the `permissions:` block matches my chosen billing path
- [ ] I resolved any mismatch before triggering a run

**Return to the main adventure:** [Run and Watch Your Workflow](08-run-your-workflow.md)
