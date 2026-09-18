<!-- page-journey: all -->
<!-- page-adventure: advanced -->
# Control What Your Agent Can Reach on the Network

> _Every agentic workflow runs inside a sandboxed firewall — nothing leaves the box unless you say so._

## :dart: What You'll Do

You'll add a `network:` block to your daily-status workflow's [frontmatter](https://github.github.com/gh-aw/reference/frontmatter/), see how the [Agent Workflow Firewall (AWF)](https://github.github.com/gh-aw/reference/sandbox/#awf-agent-workflow-firewall) enforces it, and learn to read the resulting egress log. By the end, you'll know exactly which domains your agent can reach and why that boundary matters.

## :clipboard: Before You Start

- You have a working daily-status workflow with an MCP `tools:` block from [Give Your Agent More Tools with MCP](17-add-mcp-tools.md).
- You're comfortable running `gh aw compile` and pushing workflow changes.

## Steps

### Understand the default posture

By default, [network access](https://github.github.com/gh-aw/reference/network/) for an agentic workflow is deny-by-default: the AWF blocks every outbound connection except a short list of infrastructure domains (certificate authorities, Ubuntu package verification). This is why a workflow can call MCP tools through the GitHub proxy but can't silently reach an arbitrary third-party API — that boundary is what keeps a misdirected or poisoned agent from exfiltrating data or fetching unexpected code.

```yaml
network: defaults
```

`network: defaults` alone is enough for GitHub-only workflows, but it is never sufficient once your workflow installs packages or calls an external API — you need to add the matching ecosystem or domain explicitly.

### Add an ecosystem identifier for package installs

If your workflow's task brief ever asks the agent to run `npm install`, `pip install`, or similar, add the matching ecosystem keyword instead of guessing a domain list:

```markdown .github/workflows/daily-status.md
---
network:
  allowed:
    - defaults
    - node
---
```

`node` expands to a curated list that includes `registry.npmjs.org` and `npmjs.com`. Bare shorthands like `npm` or `pypi` look plausible but are **not** recognized — they cause a compile-time error, so always reach for the ecosystem identifier (`node`, `python`, `go`, and so on).

### Add a custom domain for an external API

Now extend your daily-status workflow to call an external API alongside its MCP tools. Open your Copilot CLI session and send:

```prompt
/agentic-workflows update .github/workflows/daily-status.md to add a `network:` block
with `allowed: [defaults, github, "*.example.com"]` to the frontmatter, so the agent
can reach a partner status API in addition to GitHub's MCP tools.
```

Here is the resulting block — the skill will add this for you:

```markdown .github/workflows/daily-status.md
---
network:
  allowed:
    - defaults
    - github
    - "*.example.com"
---
```

A wildcard entry like `*.example.com` matches any subdomain plus the bare domain itself. Never write a raw protocol prefix (`https://...`) — the firewall only matches fully-qualified domain names.

<details>
<summary>:pencil2: Manual edit path</summary>

Add the `network:` block directly below your `permissions:` block in the frontmatter, then run `gh aw compile` to catch any typos before you push.

</details>

### Recompile and push

```bash
gh aw compile
git add .
git commit -m "feat: scope network access for daily status workflow"
git push
```

`gh aw compile` fails fast on invalid ecosystem names or malformed wildcards, so you catch mistakes before a run wastes an Actions minute.

### Verify the egress log after a run

Trigger a run, then inspect what the firewall actually allowed or blocked:

```bash
gh aw audit <run-id> --parse
```

Open the generated `firewall.md` file — it lists every domain the agent tried to reach during the run, marked allowed or blocked. If a domain you expected is missing from `network.allowed`, you'll see it blocked here first, which is a faster diagnostic than waiting for the agent to report a failure in its final output.

## :white_check_mark: Checkpoint

- [ ] Your workflow's frontmatter has an explicit `network:` block instead of relying on default assumptions
- [ ] You added an ecosystem identifier (`node`, `python`, etc.) rather than a bare shorthand like `npm`
- [ ] You added a wildcard or exact domain for an external API without a protocol prefix
- [ ] `gh aw compile` succeeds with no network-related errors
- [ ] You ran `gh aw audit --parse` and read `firewall.md` to confirm which domains were allowed or blocked

<!-- journey: all -->
**Next:** [Share and Reuse Your Agentic Workflows](18-share-and-reuse.md)
<!-- /journey -->

<!--
<research-metadata>
  <focus>network egress control via the `network:` frontmatter field and the AWF firewall</focus>
  <sources>
    <source>https://github.github.com/gh-aw/llms.txt</source>
    <source>https://raw.githubusercontent.com/github/gh-aw/main/.github/aw/network.md</source>
    <source>https://github.github.com/gh-aw/reference/network/</source>
    <source>https://github.github.com/gh-aw/reference/sandbox/</source>
    <source>https://github.github.com/gh-aw/reference/audit/</source>
  </sources>
  <rationale>
    The existing workshop path (steps 16 and 17) teaches learners to fetch live data and add MCP tools, and several side quests reference `network.allowed` in passing for security framing (token exfiltration, supply chain risk), but no core step teaches the `network:` field itself as a configuration surface — its default deny posture, ecosystem identifiers versus invalid bare shorthands, wildcard domain rules, and how to verify the result via `gh aw audit --parse` and `firewall.md`. This left a practical gap: learners who add MCP tools or custom API calls have no guided path to correctly scope egress, and are likely to either over-allow (defeating the security model already introduced) or hit confusing compile-time errors from invalid ecosystem shorthands. This node fills that gap with a hands-on, testable configuration step placed directly after MCP tools are introduced.
  </rationale>
</research-metadata>
-->
