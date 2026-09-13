<!-- page-journey: all -->
<!-- page-adventure: advanced -->
<!--
<research-metadata>
  <focus>Cross-repository operations in agentic workflows — target-repo and allowed-repos for safe outputs, and allowed-repos for GitHub tools reads, backed by an additional PAT or GitHub App token</focus>
  <sources>
    <source>https://github.github.com/gh-aw/reference/cross-repository/</source>
    <source>https://github.github.com/gh-aw/reference/github-tools/</source>
    <source>https://github.github.com/gh-aw/reference/safe-outputs/</source>
  </sources>
  <rationale>Steps 1-29 teach learners to build, secure, and orchestrate workflows entirely within one repository. Real organisations, especially enterprise ones, often want a single workflow to read from or write to a separate repository — for example, a central compliance-tracking repo that collects issues from many projects. No existing node covers `target-repo`, `allowed-repos`, or the additional token this requires. This step closes that gap and extends the most-travelled advanced path.</rationale>
</research-metadata>
-->

# Extend Your Agent Across Multiple Repositories

> _Most real organisations don't keep every workflow's output in one repository — learn how to read from and write to another repo safely._

## :dart: What You'll Do

You'll configure a workflow to post its output to a separate tracking repository instead of its own, and restrict which repositories the agent can read from. By the end, you'll understand the two settings that make cross-repository operations possible: `target-repo` and `allowed-repos`.

## :clipboard: Before You Start

- You completed [Teach Your Agent Domain Knowledge with Skills](29-skills-and-domain-knowledge.md).
- You understand how [safe-outputs](https://github.github.com/gh-aw/reference/safe-outputs/) work from earlier build steps.
- You have a way to store a credential as a secret — see [Side Quest: Storing Credentials with GitHub Secrets](side-quest-16-02-secrets-and-permissions.md) if you need a refresher.

## Steps

### Understand why cross-repo needs extra authentication

By default, an agentic workflow's built-in `GITHUB_TOKEN` only has authority over the repository it runs in. Reading from or writing to a different repository requires an additional token — a [PAT](https://github.github.com/gh-aw/reference/github-tools/#additional-authentication-for-github-tools) or GitHub App installation token — scoped to that other repository.

> [!NOTE]
> On GHES and GHEC, this is the same mechanism your enterprise already uses for cross-repo automation — the PAT or App must have access granted by an org admin, just like on github.com.

### Route output to a different repository

Add `target-repo` under `safe-outputs` to send created issues (or other safe outputs) to a different repository:

```yaml
---
name: Compliance Tracker
on:
  schedule: daily
permissions:
  contents: read
  issues: read
safe-outputs:
  github-token: ${{ secrets.CROSS_REPO_PAT }}
  create-issue:
    target-repo: "myorg/tracking-repo"
    title-prefix: "[compliance] "
---
```

The `github-token` at the top of `safe-outputs` supplies the extra authority; `target-repo` names the destination. Without `target-repo`, safe outputs default to the current repository.

### Restrict which repositories the agent can read

To let the agent read issues or pull requests from other repositories in your org — without letting it wander anywhere — restrict `tools.github.allowed-repos`:

```yaml
---
name: Compliance Tracker
on:
  schedule: daily
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  github:
    toolsets: [issues, pull_requests]
    allowed-repos:
      - "myorg/*"
    min-integrity: approved
    github-token: ${{ secrets.CROSS_REPO_PAT }}
---
```

The `"myorg/*"` pattern lets the agent read across every repository your organisation owns, but nothing outside it. Use `"current"` instead when you want to say "this repository only" without hard-coding its name. Whenever you set `allowed-repos`, you must also set `min-integrity` — it tells gh-aw the minimum trust level content must have before the agent reads it.

### Compile, push, and verify

```bash
gh aw compile
git add .
git commit -m "feat: add cross-repository target and allowed-repos restriction"
git push
```

Trigger a manual run from **Actions**, then open the destination repository and confirm the new issue landed there with the correct title prefix.

## :white_check_mark: Checkpoint

- [ ] You added a `CROSS_REPO_PAT` (or equivalent) secret scoped to the target repository
- [ ] Your `safe-outputs` block includes `github-token` and `target-repo`
- [ ] `gh aw compile` succeeded with no errors
- [ ] A manual run created output in the target repository, not the source repository
- [ ] You added `tools.github.allowed-repos` to restrict which repositories the agent can read
- [ ] You can explain the difference between `target-repo` (where output is written) and `allowed-repos` (what can be read)

<!-- journey: all -->
Want to choose another branch from the workshop hub? Return to [What's Next? Keep Exploring](14-next-steps.md).
<!-- /journey -->
