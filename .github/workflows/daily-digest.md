---
name: Daily Digest

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