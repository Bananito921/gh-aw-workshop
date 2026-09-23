<!-- page-journey: all -->
<!-- page-adventure: advanced -->
<!--
<research-metadata>
  <focus>Configuration governance for agentic workflows — the `gh aw env` command, GH_AW_DEFAULT_* variables, GH_AW_POLICY_* runtime policy gates, and layered percolation across enterprise, organization, and repository scope</focus>
  <sources>
    <source>https://github.github.com/gh-aw/reference/governance/</source>
    <source>https://github.github.com/gh-aw/guides/using-at-scale/</source>
    <source>https://github.github.com/gh-aw/reference/environment-variables/</source>
  </sources>
  <rationale>The curriculum (steps 1-29) teaches learners to build, secure, and cost-manage a single workflow, but no step shows how to keep settings consistent once a team has many workflows across many repositories. `gh aw env` and GH_AW_POLICY_* variables close that gap — they let a platform team set model, budget, and capability defaults once and have them percolate down, which is directly relevant to enterprise and organization adoption per the workshop's enterprise-preference guideline.</rationale>
</research-metadata>
-->

# Apply Governance Defaults Across Your Organization

> _Set a budget or model default once at the right scope, and every workflow that doesn't override it inherits the same value._

## :dart: What You'll Do

You'll export your repository's current agentic workflow defaults with `gh aw env`, edit one value, and apply it back with a dry run first. You'll also learn how `GH_AW_POLICY_*` variables can turn off a capability — like creating pull requests — across an entire organization without touching any workflow file.

## :clipboard: Before You Start

- You completed [Teach Your Agent Domain Knowledge with Skills](29-skills-and-domain-knowledge.md).
- You have at least one working agentic workflow in your repository.
- _(Enterprise or org admins)_ You have permission to set organization or enterprise-level Actions variables, or you know who to ask.

## Steps

### Understand the three scopes

`gh aw env` manages `GH_AW_DEFAULT_*` values as GitHub Actions variables at three scopes: repository, organization, and enterprise. When a workflow runs, the **most specific scope wins**:

1. Workflow frontmatter value (if set explicitly)
2. Repository variable
3. Organization variable
4. Enterprise variable
5. Built-in compiler fallback

This lets a platform team set an organization-wide baseline — a default model, a spending cap, a timeout — while individual repositories or workflows still override it when they have a real reason to.

### Export your repository's current defaults

Run this from your Codespace terminal to see what is currently set (an empty file just means nothing is overridden yet, so every value falls back to the compiler default):

```bash
gh aw env get repo-defaults.yml --scope repo --repo OWNER/REPO
cat repo-defaults.yml
```

### Edit one default and preview the change

Open `repo-defaults.yml` and set a spending guardrail:

```yaml title="repo-defaults.yml"
default_max_ai_credits: "5"
default_timeout_minutes: "10"
```

Preview before writing anything:

```bash
gh aw env update repo-defaults.yml --scope repo --repo OWNER/REPO --dry-run
```

> [!NOTE]
> `--dry-run` shows exactly which Actions variables would change. Nothing is written until you drop the flag.

### Apply the default

```bash
gh aw env update repo-defaults.yml --scope repo --repo OWNER/REPO
```

Any workflow you compile after this point inherits `default_max_ai_credits: "5"` unless its own frontmatter sets `max-ai-credits` explicitly.

### Recognize a policy gate in the wild

Policy variables (`GH_AW_POLICY_*`) are different from defaults — they are boolean on/off switches for a capability, not tunable numbers. For example, an org admin can disable pull-request creation everywhere:

```bash
gh variable set GH_AW_POLICY_ALLOW_CREATE_PULL_REQUEST --org my-org --body "false"
```

Any workflow with `safe-outputs.create-pull-request` configured then fails fast at startup with a clear message instead of silently attempting a blocked write:

```text
create-pull-request is disabled by runtime policy: GH_AW_POLICY_ALLOW_CREATE_PULL_REQUEST=false.
Remove safe-outputs.create-pull-request or set GH_AW_POLICY_ALLOW_CREATE_PULL_REQUEST=true.
```

> :thinking: **Predict:** If your repository sets `GH_AW_POLICY_ALLOW_CREATE_PULL_REQUEST=true` but your organization sets it to `false`, which one wins? Check the precedence order above before you answer, then confirm by reading the [Environment Variables reference](https://github.github.com/gh-aw/reference/environment-variables/#runtime-policy-variables).

### Follow the rollout pattern

> [!TIP]
> <details>
> <summary><b>Enterprise teams: use a layered rollout.</b></summary>
>
> Set enterprise baseline defaults first, add organization defaults only where a group of repositories genuinely differs, and reserve repository-scope defaults for true one-off exceptions. Keep workflow frontmatter overrides rare and explicit — they should be the exception you can `grep` for, not the norm.
>
> </details>

## :white_check_mark: Checkpoint

- [ ] You exported your repository's current defaults with `gh aw env get`
- [ ] You can name the five-level precedence order from workflow frontmatter down to compiler fallback
- [ ] You edited a `default_max_ai_credits` or `default_timeout_minutes` value and previewed it with `--dry-run`
- [ ] You applied the change with `gh aw env update` and confirmed it did not touch your workflow file
- [ ] You can explain the difference between a `GH_AW_DEFAULT_*` value and a `GH_AW_POLICY_*` gate

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
