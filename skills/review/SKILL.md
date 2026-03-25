---
name: review
description: Run CodeRabbit to review code changes. Use when asked to review code, get feedback, or analyze uncommitted changes. Max 3 runs per change set.
---

# Review

Run CodeRabbit to review code changes.

## Commands

- `cr -h` - Show CodeRabbit help
- `coderabbit --prompt-only -t uncommitted` - Review uncommitted changes (most common)

## Rules

- IMPORTANT: Do NOT run CodeRabbit more than 3 times for a given set of changes
- Always use the `--prompt-only` flag
- Primary use case is reviewing uncommitted changes

## Usage

When asked to review code:
1. Run `coderabbit --prompt-only -t uncommitted` to review uncommitted changes
2. Analyze the review output
3. Address any issues identified
4. Track the number of review runs (max 3 per change set)
