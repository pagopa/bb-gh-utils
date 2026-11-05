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

Building Block repositories declare the `bb-tools` marketplace and
`bb-gh-utils` plugin in `.github/copilot/settings.json`. Supported clients install
and activate the plugin for that repository; no manual global installation is
required.

Requires read access to this repository. Keep the plugin disabled globally to
avoid activating it in unrelated projects.

**Known limitation:** automatic rule loading is not yet verified. Keep existing
project instructions. See [validation status](docs/shared-ai.md#validation-status).

## Contributing

1. Create a branch from an up-to-date `main`.
2. Add or update the shared asset and document how projects consume it.
3. Open a pull request and wait for approval from the repository owners listed in [CODEOWNERS](CODEOWNERS).
