---
name: Daily Digest
model: gpt-5.4

on:
  schedule: daily on weekdays
  workflow_dispatch:

permissions:
  contents: read
  issues: read
  pull-requests: read
  copilot-requests: write

network: defaults

safe-outputs:
  create-issue:
    max: 1
---

# Daily Digest

Every weekday, create exactly one GitHub issue containing a daily digest
of all currently open issues and pull requests in this repository.

You MUST use the `create_issue` safe-output tool to create the digest issue.

First, inspect all open issues and pull requests in the repository.

Group the issues and pull requests by label.

For each item, include:
- The title
- The author
- How long it has been open
- Its labels

Include:
- The total number of open issues
- The total number of open pull requests

Create the issue with this title:

Daily Digest - <date>

If there are no open issues or pull requests, still create the Daily Digest
issue and clearly state that there are no open issues or pull requests.

Do not finish without calling the `create_issue` safe-output tool.

Every weekday, create a GitHub issue that summarises all open issues
and pull requests in this repository.

Group the issues and pull requests by label.

For each item, include:
- The title
- The author
- How long it has been open
- Its labels

Include the total number of open issues and the total number of open
pull requests.

Title the new issue:

Daily Digest - <date>

If there are no open issues or pull requests, clearly state that in
the digest.
