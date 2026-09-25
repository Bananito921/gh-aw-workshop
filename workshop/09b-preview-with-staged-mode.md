<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Preview Safe Outputs Before They Run: Staged Mode

> _Every prompt tweak you tested in the last step created a real issue. Staged mode lets you see what a change would do before it actually happens._

## :dart: What You'll Do

You'll enable [staged mode](https://github.github.com/gh-aw/reference/staged-mode/) on your `daily-report-status` workflow so its [`create-issue`](https://github.github.com/gh-aw/reference/safe-outputs/#issue-creation-create-issue) [safe output](https://github.github.com/gh-aw/reference/safe-outputs/) becomes a preview instead of a real write. By the end, you can iterate on your prompt as many times as you want, reading each preview in the Actions run summary, without cluttering your repository with test issues.

## :clipboard: Before You Start

- You completed [Refine, Test, and Improve Your Workflow](09-agentic-editing.md).
- Your `daily-report-status` workflow has a `safe-outputs:` block with `create-issue:`.
- You know how to trigger a run with **workflow_dispatch** from the **Actions** tab.

## Steps

### Understand the problem staged mode solves

Every time you changed the workflow brief in the previous step and triggered a run, the agent created a real issue if the run succeeded. That's fine once you're confident in the prompt, but during rapid iteration it means cleaning up test issues after every experiment — or worse, losing track of which issue came from which prompt version.

Staged mode skips every real write and replaces it with a detailed preview in the Actions step summary instead. The agent still runs fully — it reads data, reasons, and decides what to write — but nothing actually happens in your repository until you turn staged mode off.

### Enable staged mode for your workflow

In your Codespace terminal, run `gh copilot` and send:

```prompt
/agentic-workflows update .github/workflows/daily-report-status.md to add
`staged: true` at the top of the `safe-outputs:` block so all outputs preview
instead of writing for real.
```

The skill adds the setting and recompiles. Review the diff before committing.

<details>
<summary>:pencil2: Manual edit path</summary>

Open `.github/workflows/daily-report-status.md` and add `staged: true` as the first line inside `safe-outputs:`:

```markdown .github/workflows/daily-report-status.md
safe-outputs:
  staged: true
  create-issue:               # the only write action the agent may perform
```

Run `gh aw compile`, then commit and push both files.

</details>

:thinking: Pause and predict: With `staged: true` set globally, does the agent still call any tools to gather data, or does it stop before doing any work?

### Trigger a run and read the preview

Commit and push, then trigger a manual run from the **Actions** tab:

```bash
git add .
git commit -m "test: enable staged mode for daily-report-status"
git push
```

Open the completed run and scroll to the step summary. Look for a heading marked with the 🎭 emoji — for example, **🎭 Staged Mode: Issue Creation Preview**. It shows the exact title, body, and labels the agent would have written, along with a summary line confirming no GitHub resources were created.

:pencil2: Try it: Make one more prompt change (as you did in the previous step) and re-run. Compare the two previews side by side — you should see the difference in output without any new issues appearing in your **Issues** tab.

### Scope staged mode to one output type

If your workflow has more than one safe output and you only want to preview one of them, move `staged: true` under that specific output instead of the top-level setting:

```markdown .github/workflows/daily-report-status.md
safe-outputs:
  staged: false        # default: everything else executes normally
  create-issue:
    staged: true        # only issue creation previews
```

A type-level `staged` setting overrides the global one, so you can pilot a new or risky output type while trusted ones keep writing for real.

### Turn staged mode off when you're ready

Once your prompt produces the output you want in the preview, remove `staged: true` (or set it to `false`) and recompile:

```prompt
/agentic-workflows update .github/workflows/daily-report-status.md to remove
`staged: true` from the safe-outputs block so the workflow writes for real again.
```

Trigger one final run and confirm a real issue appears in your **Issues** tab.

## :white_check_mark: Checkpoint

- [ ] Your workflow's `safe-outputs:` block includes `staged: true`
- [ ] A triggered run completed and showed a 🎭 preview in the Actions step summary instead of a real write
- [ ] You compared previews across two different prompt versions without creating extra issues
- [ ] You can explain the difference between a global `staged: true` and a type-scoped one
- [ ] You removed staged mode and confirmed a real issue was created on the final run
- [ ] `gh aw compile` succeeded and both workflow files are committed and pushed

<!-- journey: all -->
**Next:** [What's Next? Keep Exploring](14-next-steps.md)
<!-- /journey -->

<!--
<research-node-metadata>
  <focus>Staged mode for previewing safe outputs during iterative prompt editing</focus>
  <sources>
    <source>https://github.github.com/gh-aw/llms.txt</source>
    <source>https://raw.githubusercontent.com/github/gh-aw/main/docs/src/content/docs/reference/staged-mode.md</source>
    <source>https://github.github.com/gh-aw/reference/staged-mode/</source>
    <source>https://github.github.com/gh-aw/reference/safe-outputs/</source>
  </sources>
  <rationale>
    Reviewing the gh-aw LLMs.txt agent-prompt index alongside the safe-outputs and staged-mode
    reference docs surfaced a documented, current capability — `staged: true` — that has no
    coverage anywhere in this workshop, despite the curriculum repeatedly having learners
    iterate on prompts that trigger real safe-output writes (Step 7's `create-issue`, Step 9's
    edit/debug/optimize loop, and Step 14b's PR review). Learners currently have no way to
    preview a prompt change before it creates a real issue or comment, which makes rapid
    iteration noisy and error-prone. This node slots in immediately after Step 9, where the
    edit/test loop is introduced, so learners can immediately apply staged mode to the exact
    iteration workflow they just learned, then turn it off once satisfied with a final real run.
  </rationale>
</research-node-metadata>
-->
