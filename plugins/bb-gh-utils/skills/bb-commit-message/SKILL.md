---
name: bb-commit-message
description: "Draft a Conventional Commit message for Building Blocks changes. Use when asked to write, suggest, or review a commit message."
---

# Building Blocks commit message

## Procedure

1. Read the supplied diff or inspect the relevant Git changes using read-only commands.
2. Read the [commit message instructions](./references/commit-message-instructions.md).
3. Choose the type and optional scope from the actual changes, not the branch name.
4. Draft an imperative English summary and add a body only when context is needed.
5. Return the message in a code block. Preserve any trailers required by applicable instructions.

## Boundaries

- Drafting a message does not authorize staging, committing, or pushing.
- Do not include unrelated changes in the message.
- If no diff or change description is available, ask for it rather than inventing changes.
