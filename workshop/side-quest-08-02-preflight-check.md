<!-- page-journey: all -->
<!-- page-adventure: side-quest -->
# Side Quest: Pre-Flight Workflow Checks

> _Optional: run this two-minute diagnostic before you click **Run workflow** to catch the leading cause of `model-access-not-configured` failures before it happens._

## :dart: What You'll Do

You'll confirm your compiled [lock file](https://github.github.com/gh-aw/reference/glossary/#workflow-lock-file-lockyml) is present and current, and that your [`permissions:`](https://github.github.com/gh-aw/reference/permissions/) block matches the billing path you chose in [Confirm Model Access](07d-confirm-model-access.md) — before triggering a run.

## :clipboard: Before You Start

- Completed [Write Your First Agentic Workflow](07-your-first-workflow.md) and [Confirm Model Access](07d-confirm-model-access.md)
- `daily-report-status.md` exists in `.github/workflows/` (the compiled lock file may or may not exist yet)

## Steps

A stale or missing lock file is the leading cause of `model-access-not-configured` failures. Run these two checks — each takes less than a minute.

### Check the lock file is present and current

Open `.github/workflows/` in your repository on GitHub and confirm both files are there:

- `daily-report-status.md` (source)
- `daily-report-status.lock.yml` (compiled lock file)

If either file is missing, return to [Write Your First Agentic Workflow](07-your-first-workflow.md) to complete the workflow creation steps. If the lock file is present but you are unsure it is current, recompile and push before continuing:

```bash
gh aw compile
git add .
git commit -m "chore: sync lock file" && git push
```

### Check billing configuration matches the lock file

Open `daily-report-status.lock.yml` (or `daily-report-status.md`) and confirm the `permissions:` block matches the billing path you chose in Step 7d:

| Billing path | `copilot-requests: write` present |
|---|---|
| Organization centralized billing | Yes |
| Personal billing | No — and `COPILOT_GITHUB_TOKEN` is set in **Settings → Secrets → Actions** |

Any mismatch means returning to [Confirm Model Access](07d-confirm-model-access.md) to fix the configuration and recompile.

## :white_check_mark: Checkpoint

- [ ] Both `daily-report-status.md` and `daily-report-status.lock.yml` exist in `.github/workflows/` on `main`
- [ ] The lock file is current (recompiled and pushed if you had any doubt)
- [ ] The `permissions:` block matches your chosen billing path

**Return to the main adventure:** [Run and Watch Your Workflow](08-run-your-workflow.md)
