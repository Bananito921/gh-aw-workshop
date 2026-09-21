<!-- page-journey: all -->
<!-- page-adventure: side-quest -->
<!--
<research-metadata>
  <focus>Running agentic workflows on Actions Runner Controller (ARC) with Docker-in-Docker (DinD) for Kubernetes-based self-hosted runner fleets, using the runner.topology: arc-dind frontmatter field</focus>
  <sources>
    <source>https://github.github.com/gh-aw/reference/arc-dind-copilot-agent/</source>
    <source>https://github.github.com/gh-aw/reference/self-hosted-runners/</source>
  </sources>
  <rationale>The existing self-hosted runner content (Step 24 and its infrastructure deep-dive side quest) covers runner labels, ephemeral/JIT runners, and proxy configuration, but never mentions Kubernetes-based runner fleets managed by Actions Runner Controller — a common enterprise pattern. Learners running Kubernetes-hosted runners have no guidance on the runner.topology: arc-dind field, which is required for agentic workflows to work correctly on ARC. This side quest closes that gap for enterprise learners on Kubernetes infrastructure.</rationale>
</research-metadata>
-->
# Side Quest: Running on Actions Runner Controller (ARC) with Docker-in-Docker

> _If your organisation runs Kubernetes-hosted runners, one frontmatter field is the difference between a working agent and a mysterious failure._

## :dart: What You'll Do

You'll learn what Actions Runner Controller (ARC) is, why it needs Docker-in-Docker (DinD) mode for agentic workflows, and how to set the `runner.topology: arc-dind` frontmatter field so your workflow runs correctly on Kubernetes-managed runners.

## :clipboard: Before You Start

- You completed [Run Your Agentic Workflow on a Self-Hosted Runner](24-self-hosted-runners.md).
- Your organisation uses Kubernetes to host self-hosted runners (via ARC), rather than standalone VMs.
- You have access to your cluster or can consult your platform admin.

## Steps

### Understand why ARC needs a special mode

**Actions Runner Controller (ARC)** is a Kubernetes operator that manages self-hosted GitHub Actions runners as pods. Agentic workflows need a Docker daemon to run the agent sandbox, so ARC runner pods must use **Docker-in-Docker (DinD) mode** — a privileged sidecar container running its own Docker daemon alongside the runner container.

> [!NOTE]
> Kubernetes mode (`containerMode.type="kubernetes"`) works for classic Actions jobs, but it is **not supported** for agentic workflows. Only DinD mode works.

Without the right frontmatter setting, a workflow on an ARC DinD runner can fail confusingly: an empty workspace, a missing Docker socket, or tool-cache warnings. One field fixes all of this.

### Add `runner.topology: arc-dind` to your frontmatter

Open your workflow file and add a `runner:` block alongside your existing `runs-on:` value:

```markdown .github/workflows/daily-status.md
---
runs-on: arc-runner-set
runner:
  topology: arc-dind
---
```

Replace `arc-runner-set` with the label your platform admin assigned to your ARC runner scale set.

`runner.topology: arc-dind` tells the compiler to handle ARC's DinD quirks automatically: sharing system binaries with the Docker daemon over a named volume, mounting your checked-out repository into the agent's sandbox container, matching user identities so file permissions line up, and collecting agent output to a predictable path. You do not need to configure any of this by hand.

### Compile and verify

```bash
gh aw compile
```

Commit both the `.md` file and the regenerated `.lock.yml`, then trigger a manual run and confirm it completes on your ARC runner.

> [!TIP]
> Ask your platform admin to confirm your ARC controller is running in DinD mode (`containerMode.type="dind"`) before troubleshooting further — the two most common blockers are a non-DinD runner and a missing shared work volume.

### Know where to look when something goes wrong

ARC DinD runners write sandbox logs to `$RUNNER_TEMP/gh-aw/sandbox/firewall/logs/`, not the usual `/tmp/gh-aw/` path, because `$RUNNER_TEMP` sits on the volume shared with the Docker daemon. Check `awf.log` there first for sandbox setup and network-isolation details.

> [!NOTE]
> If your cluster enforces `allowPrivilegeEscalation: false`, the default Copilot CLI installer needs `sudo`, which will be blocked. Ask your admin about installing with the `--rootless` flag in your `copilot-setup-steps` instead.

## :white_check_mark: Checkpoint

- [ ] You can explain why agentic workflows require ARC's Docker-in-Docker mode rather than Kubernetes mode
- [ ] You added `runner: topology: arc-dind` to your workflow's frontmatter
- [ ] `gh aw compile` completed without errors after the change
- [ ] Your workflow ran successfully on your ARC runner scale set
- [ ] You know where ARC DinD sandbox logs are written when you need to troubleshoot

<!-- journey: all -->
Return to [Run Your Agentic Workflow on a Self-Hosted Runner](24-self-hosted-runners.md).
<!-- /journey -->
