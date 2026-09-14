<!-- page-journey: all -->
<!-- page-adventure: side-quest -->
# Side Quest: Recovering When the Review Skill Is Not Applied

> _Optional: use this recovery checklist if your PR reviewer's review does not cite the `pr-review-standards` skill, then return to [Build a PR Reviewer with an Agent and Skill](14b-pr-reviewer-workflow.md)._

## :dart: What You'll Do

You'll diagnose why the `pr-reviewer` inline agent from [Step 14b](14b-pr-reviewer-workflow.md) submitted a review that ignores the `pr-review-standards` skill, then apply the fix and confirm the skill is picked up on the next run.

## :clipboard: Before You Start

- You completed the **Test the Ready-for-Review Trigger** section of [Build a PR Reviewer with an Agent and Skill](14b-pr-reviewer-workflow.md).
- Your PR reviewer workflow ran and submitted a review, but the review does not mention the `pr-review-standards` skill and does not cite changed files and lines for its findings.

## Steps

### Confirm the skills directory exists

The most common cause is a missing or unpushed skills directory. Check for it in your terminal:

```bash
ls .github/skills/
```

If `.github/skills/agentic-workflows/` is missing, run `gh aw init`, commit the generated files, and push:

```bash
gh aw init
git add .
git commit -m "Initialize agentic workflow skills"
git push
```

Re-trigger `/review` on your pull request and check whether the new review cites the skill's evidence format.

### Reinforce the instruction if the directory already exists

If `.github/skills/` is present and pushed but the reviewer still is not applying the skill, the `pr-reviewer` agent's brief may not be explicit enough about discovering it. Ask your agent to strengthen the instruction:

```prompt
/agentic-workflows Update .github/workflows/pr-reviewer.md so the pr-reviewer agent explicitly searches for and applies the pr-review-standards skill before returning findings.
```

Review the diff before accepting it — the agent should add or sharpen language telling `pr-reviewer` to look up and apply the skill, not just remove the symptom.

### Compile, commit, and re-verify

```bash
gh aw compile
git add .
git commit -m "fix: reinforce skill lookup in pr-reviewer agent"
git push
```

Comment `/review` again on your pull request and confirm the new submitted review:

- Cites the changed file and line for each finding
- Reflects the evidence-based format defined by `pr-review-standards`

## :white_check_mark: Checkpoint

- [ ] You confirmed whether `.github/skills/agentic-workflows/` exists and is pushed
- [ ] If it was missing, you ran `gh aw init` and pushed the generated files
- [ ] If the directory existed but the skill still was not applied, you reinforced the instruction in the `pr-reviewer` agent's brief
- [ ] You recompiled, committed, and pushed the updated workflow
- [ ] A fresh `/review` run produced a review that cites the `pr-review-standards` format with evidence for each finding

**Return to the main adventure:** [Build a PR Reviewer with an Agent and Skill](14b-pr-reviewer-workflow.md)
