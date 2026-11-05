# Shared AI assets

## Scope

This repository holds reusable AI guidance alongside future shared GitHub Actions
components. AI instructions describe how to work; workflows and actions implement
CI automation. Keep these responsibilities separate.

| Location | Purpose | Status |
| --- | --- | --- |
| [`plugins/bb-gh-utils/`](../plugins/bb-gh-utils/) | Installable common AI plugin | Version `0.1.0` |
| [`.github/plugin/marketplace.json`](../.github/plugin/marketplace.json) | Marketplace catalog | `bb-tools`, one plugin |
| [`com.github.copilot/rules/`](../plugins/bb-gh-utils/com.github.copilot/rules/) | Copilot-specific shared instructions | Four rules |
| [`skills/`](../plugins/bb-gh-utils/skills/) | Portable skills and bundled resources | Commit-message skill |
| [`com.github.copilot/agents/`](../plugins/bb-gh-utils/com.github.copilot/agents/) | Copilot-specific shared agents | AI asset migrator |
| `.github/workflows/` | Reusable GitHub Actions workflows | Future work |
| `.github/actions/` | Composite GitHub Actions | Future work |

Rule and skill paths in the table are relative to the plugin root. Only populated
directories are created. Shared agents belong under the plugin's
`com.github.copilot/agents/`, not the repository's `.github/agents/`.
Reusable workflows must live directly under `.github/workflows/` for GitHub
Actions to support external callers.

## Copy provenance

The following instruction topics exist in both `pagopa/onemail` and
`pagopa/oneidentity`. Their contents were copied from OneIdentity unchanged;
the plugin reorganizes those copies without merging the two project versions.

| Copied file | Comparison with OneMail |
| --- | --- |
| [Bash](../plugins/bb-gh-utils/com.github.copilot/rules/bash.instructions.md) | Same baseline, with additional script scope and deterministic-behavior guidance in OneIdentity |
| [Commit messages](../plugins/bb-gh-utils/skills/bb-commit-message/references/commit-message-instructions.md) | Same rules; optional-scope wording differs |
| [GitHub Actions](../plugins/bb-gh-utils/com.github.copilot/rules/github-actions.instructions.md) | Same guidance; Markdown and YAML formatting differs |
| [Markdown](../plugins/bb-gh-utils/com.github.copilot/rules/markdown.instructions.md) | Same baseline, with additional scope and explicit repository-link guidance in OneIdentity |
| [Terraform](../plugins/bb-gh-utils/com.github.copilot/rules/terraform.instructions.md) | Same guidance; Markdown formatting differs |

Source paths are the matching files under `.github/instructions/` in OneIdentity.
Both application repositories retain their original files.
The commit-message text is now a resource for the `bb-commit-message` skill.
There is no second manually maintained copy under `.github/instructions/`.

## Deliberate exclusions

- Node.js instructions differ substantially: OneMail includes its monorepo and
  Lambda conventions; OneIdentity includes its React/Vite conventions.
- Repository-specialist agents and root `AGENTS.md` files describe their owning
  application and are not shared assets.
- The source skill inventories differ. No product skill is copied merely because
  it might be reusable in the other project. The new commit-message skill wraps
  the existing shared commit guidance.
- Java and Python instructions are not present in both source inventories.

## Try without installing

From the repository root, inspect the plugin using a temporary mount:

```shell
copilot --plugin-dir ./plugins/bb-gh-utils plugin list --json
copilot --plugin-dir ./plugins/bb-gh-utils instruction list --json
copilot --plugin-dir ./plugins/bb-gh-utils
```

In the interactive session, inspect `/skills list` and invoke
`/bb-commit-message` with a change description. Confirm that it drafts a message
without staging, committing, or pushing. A mount does not persist an installation.

## Migrate an AI asset into common

Select the [bb-ai-asset-migrator agent](../plugins/bb-gh-utils/com.github.copilot/agents/bb-ai-asset-migrator.agent.md)
from the agent picker, or use `/agent` in Copilot CLI, after loading the plugin.
Provide the common source checkout and one of:

- A local file or skill directory in a Building Block repository.
- A GitHub file/directory link, preferably with an explicit revision.
- A repository link; the agent inventories candidates and asks which to migrate.

For example:

```text
Copy the skill at /path/to/building-block/.github/skills/example into the
bb-gh-utils checkout at /path/to/bb-gh-utils. Keep the source unchanged.
```

```text
Migrate the AI asset at https://github.com/OWNER/REPOSITORY/blob/COMMIT/PATH
into the common plugin. Preserve required resources and report adaptations.
```

The agent maps assets to plugin locations, checks required resources and
product-specific assumptions, and reports client-compatibility limitations.
It must not modify source repositories, stage, commit, push, or install anything
in the user's profile. These are agent instructions, not filesystem enforcement.
Model-driven end-to-end migration has not been tested.

## Install with Copilot CLI

This package is not yet validated as a replacement for automatic instructions.
Read [Validation status](#validation-status) before adopting it.

To try the local marketplace from the repository root:

```shell
copilot plugin marketplace add .
copilot plugin install bb-gh-utils@bb-tools
copilot plugin list
```

The installed CLI loads this local marketplace plugin directly from its source
directory. Edits take effect in the next session; this is a development workflow,
not a cached remote release.

After these files are published to the remote repository, register the remote
marketplace instead:

```shell
copilot plugin marketplace add pagopa/bb-gh-utils
copilot plugin install bb-gh-utils@bb-tools
```

Use one source for `bb-tools`; do not register local and remote copies under the
same name. The remote commands require read access to the repository. No npm
package or VS Code extension publication is required.

Remote installations keep components locally for subsequent sessions. To refresh
the catalog and update the installed plugin:

```shell
copilot plugin marketplace update bb-tools
copilot plugin update bb-gh-utils
```

Start a new session after updating. Do not edit the installation cache manually.
Refreshing a catalog is distinct from updating an installed plugin.

The manifest version identifies the plugin; it does not pin a Git revision or
guarantee compatibility with every CLI or editor version.

## Use in VS Code

With plugin support available and enabled, VS Code discovers CLI-installed
plugins. Open **Chat: Open Customizations**, select **Plugins**, and verify
`bb-gh-utils` is listed and enabled for the intended Copilot harness and workspace.
Do not install a second copy from another source.

Start a new chat. Verify the commit-message skill is available and test that the
rules are applied to relevant files, rather than assuming installation proves
behavior. Rule files retain their `applyTo` patterns; installation must not be
treated as a guarantee of identical rule activation across clients.

When using SSH, WSL, or a container, the plugin must be available in the
environment where the Copilot harness runs.

## Scope and rollout

- Keep application architecture, commands, and product exceptions in the owning
  repository. Do not assume local instructions override conflicting plugin rules.
- Enable the plugin only for intended projects. Installing it alone does not
  restrict it to OneMail and OneIdentity.
- The application repositories still contain the original instructions.
  Evaluate duplicate guidance before adopting the plugin there; no consumer
  files are changed by this packaging work.
- The `bb-tools` marketplace contains only the common `bb-gh-utils` plugin. It
  does not add product plugins or imply automatic dependency resolution.
- The AI asset migrator is a shared agent. No hooks, MCP servers, or CI workflows
  are added.
- No user-level plugin installation is performed as part of creating these files.

## Validation status

With Copilot CLI `1.0.103`, both a temporary `--plugin-dir` mount and a local
installation into an isolated `COPILOT_HOME` recognize `bb-gh-utils` version
`0.1.0` and discover the `bb-commit-message` skill.

Registering the local marketplace and installing `bb-gh-utils@bb-tools` also
succeeds. The CLI reports a live source rather than a copied installation.
Direct installation succeeds but emits a deprecation warning; the marketplace
is used to avoid relying on that deprecated distribution method.

However, `copilot instruction list --json` returns an empty list for the mounted,
directly installed, and marketplace-installed plugin. A temporary unscoped
Markdown rule did not change that result.

As a diagnostic control, setting `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` to the rule
directory for a single command discovers all four instructions with their
expected `applyTo` patterns. This confirms instruction file parsing but does not
validate plugin-native rule discovery or runtime behavior.

Automatic instruction loading remains an acceptance requirement. Do not replace
repository instructions with this package yet. No persistent environment
variables, client settings, or client updates were applied. VS Code behavior and
model-driven skill execution have not been tested.

The five original guidance documents retain their committed contents unchanged.

## References

- [CLI plugin format](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference)
- [CLI installation and updates](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing)
- [VS Code plugin discovery](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
