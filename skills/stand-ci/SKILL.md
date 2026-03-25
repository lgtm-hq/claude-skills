---
name: stand-ci
description: CI/CD and GitHub Actions guidelines. Use when writing workflows or Actions. Shell script code must be in dedicated .sh or .py files. Actions must be pinned to SHAs, not versions.
---

# CI/CD Standards

Standards for CI/CD pipelines and GitHub Actions.

## GitHub Actions / Workflows

- Shell script code should NOT be inline in Actions/Workflow YAML files
- Extract scripts to dedicated `.sh` or `.py` files in a `scripts/` directory
- Reference these scripts from the workflow
- **ALWAYS pin actions to full commit SHAs, NOT version tags**

## Action Pinning

Actions MUST be pinned to SHA hashes for security and reproducibility:

```yaml
# WRONG - version tag (can be moved, vulnerable to supply chain attacks)
- uses: actions/checkout@v4
- uses: actions/setup-python@v5

# CORRECT - pinned to SHA (immutable, secure)
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1
- uses: actions/setup-python@0a5c61591373683505ea898e09a3ea4f39ef2b9c  # v5.0.0
```

### Finding SHAs

1. Go to the action's releases page
2. Click on the version tag
3. Copy the full commit SHA
4. Add version as comment for readability

## Why

- Easier to test scripts locally
- Better syntax highlighting and linting
- Reusable across workflows
- Cleaner workflow files
- Proper version control diffs

## Examples

```yaml
# WRONG - inline script in workflow
jobs:
  build:
    steps:
      - name: Build and deploy
        run: |
          echo "Building..."
          npm install
          npm run build
          if [ -f dist/index.js ]; then
            aws s3 sync dist/ s3://bucket/
          fi

# CORRECT - reference external script
jobs:
  build:
    steps:
      - name: Build and deploy
        run: ./scripts/ci/build-and-deploy.sh
```

## Script Organization

```
scripts/
└── ci/
    ├── build.sh
    ├── deploy.sh
    ├── test.sh
    └── utils/
        └── helpers.py
```

## Script Requirements

- Scripts must be executable (`chmod +x`)
- Include shebang line (`#!/usr/bin/env bash` or `#!/usr/bin/env python3`)
- Follow shell script best practices (set -euo pipefail for bash)
