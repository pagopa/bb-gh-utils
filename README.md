# bb-gh-utils

Shared GitHub utilities for PagoPA Building Blocks projects.

## Purpose

This repository collects the dependencies common to all Building Blocks projects, so each project can reuse them instead of maintaining its own copy:

- **CI**: reusable GitHub Actions workflows and composite actions.
- **Agent customizations**: shared Copilot agents, prompts, instructions and skills.

## Structure

| Path                    | Content                              |
| ----------------------- | ------------------------------------ |
| `.github/workflows/`    | Reusable GitHub Actions workflows    |
| `.github/actions/`      | Composite GitHub Actions             |
| [`.github/plugin/marketplace.json`](.github/plugin/marketplace.json) | Copilot plugin marketplace catalog |
| [`plugins/bb-gh-utils/`](plugins/bb-gh-utils/) | Shared AI plugin: rules, agents and skills |
| `docs/`                 | Usage and contribution documentation |

## Use the Copilot plugin

### Install

Requires Copilot CLI and read access to this repository.

```shell
copilot plugin marketplace add pagopa/bb-gh-utils
copilot plugin install bb-gh-utils@bb-tools
copilot plugin list
```

Start a new Copilot session after installation.

### Enable only for a VS Code workspace

1. Run **Chat: Open Customizations** in the target workspace.
2. Open **Plugins** > **bb-gh-utils**.
3. Select **Disable** for the current profile if globally enabled.
4. Select **Enable (Workspace)** from the dropdown.
5. Start a new Copilot chat.

Requires VS Code Copilot plugin support. Workspace activation applies to VS Code,
not standalone CLI sessions; the CLI has no `install --scope workspace` option.

### Update

```shell
copilot plugin marketplace update bb-tools
copilot plugin update bb-gh-utils
```

Start a new Copilot session after updating.

**Known limitation:** automatic rule loading is not yet verified. Keep existing
project instructions. See [validation status](docs/shared-ai.md#validation-status).

## Contributing

1. Create a branch from an up-to-date `main`.
2. Add or update the shared asset and document how projects consume it.
3. Open a pull request and wait for approval from the repository owners listed in [CODEOWNERS](CODEOWNERS).
