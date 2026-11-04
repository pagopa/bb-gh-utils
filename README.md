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
| `.github/agents/`       | Shared custom agents                 |
| `.github/prompts/`      | Shared prompt files                  |
| `.github/instructions/` | Shared instruction files             |
| `.github/skills/`       | Shared agent skills                  |
| `docs/`                 | Usage and contribution documentation |

## Contributing

1. Create a branch from an up-to-date `main`.
2. Add or update the shared asset and document how projects consume it.
3. Open a pull request and wait for approval from the repository owners listed in [CODEOWNERS](CODEOWNERS).
