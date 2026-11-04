# Shared AI assets

## Initial scope

This repository holds reusable AI guidance alongside future shared GitHub Actions
components. AI instructions describe how to work; workflows and actions implement
CI automation. Keep these responsibilities separate.

| Location | Purpose | Status |
| --- | --- | --- |
| [`.github/instructions/`](../.github/instructions/) | Shared AI instructions | Five files copied |
| `.github/agents/` | Shared custom agents | Not populated |
| `.github/skills/` | Shared skills and bundled resources | Not populated |
| `.github/prompts/` | Shared prompt files | Not populated |
| `.github/workflows/` | Reusable GitHub Actions workflows | Future work |
| `.github/actions/` | Composite GitHub Actions | Future work |

Only populated directories are created. Reusable workflows must live directly
under `.github/workflows/` for GitHub Actions to support external callers.

## Copy provenance

The following instruction topics exist in both `pagopa/onemail` and
`pagopa/oneidentity`. This initial snapshot copies the OneIdentity files unchanged;
it does not merge the two versions or establish that they are byte-identical.

| Copied file | Comparison with OneMail |
| --- | --- |
| [Bash](../.github/instructions/bash.instructions.md) | Same baseline, with additional script scope and deterministic-behavior guidance in OneIdentity |
| [Commit messages](../.github/instructions/copilot-commit-message-instructions.md) | Same rules; optional-scope wording differs |
| [GitHub Actions](../.github/instructions/github-actions.instructions.md) | Same guidance; Markdown and YAML formatting differs |
| [Markdown](../.github/instructions/markdown.instructions.md) | Same baseline, with additional scope and explicit repository-link guidance in OneIdentity |
| [Terraform](../.github/instructions/terraform.instructions.md) | Same guidance; Markdown formatting differs |

Source paths are the matching files under `.github/instructions/` in OneIdentity.
Both application repositories retain their original files.

## Deliberate exclusions

- Node.js instructions differ substantially: OneMail includes its monorepo and
  Lambda conventions; OneIdentity includes its React/Vite conventions.
- Repository-specialist agents and root `AGENTS.md` files describe their owning
  application and are not shared assets.
- The current skill inventories differ. No skill is copied merely because it
  might be reusable in the other project.
- Java and Python instructions are not present in both source inventories.

## Consumption

This is a source snapshot, not an installed plugin or a synchronization mechanism.
The standard instruction files can apply when working in this repository; they
are not automatically discovered from OneMail or OneIdentity.

The commit-message file retains its original name and has no `applyTo` metadata.
Its presence alone does not configure commit-message generation.

Plugin packaging, version selection, local caching, client configuration, and
updates remain separate decisions. No consumer configuration or CI implementation
is introduced by this initial copy.
