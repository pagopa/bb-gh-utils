# Shared AI assets

## Plugin contents

The `bb-gh-utils` plugin is distributed through the `bb-tools` marketplace.
Keep product architecture, commands, and exceptions in the owning repository;
only reusable guidance belongs here.

| Component | Location |
| --- | --- |
| Plugin manifest | [`plugin.json`](../plugins/bb-gh-utils/plugin.json) |
| Marketplace catalog | [`marketplace.json`](../.github/plugin/marketplace.json) |
| Bash, GitHub Actions, Markdown, and Terraform rules | [`com.github.copilot/rules/`](../plugins/bb-gh-utils/com.github.copilot/rules/) |
| Commit-message skill | [`skills/bb-commit-message/`](../plugins/bb-gh-utils/skills/bb-commit-message/) |
| AI asset migration agent | [`com.github.copilot/agents/`](../plugins/bb-gh-utils/com.github.copilot/agents/) |
| Atlassian MCP configuration | [`mcp.json`](../plugins/bb-gh-utils/mcp.json) |

## Activation

Building Block repositories declare the marketplace and plugin in
`.github/copilot/settings.json`. Supported clients activate them for that
repository. Keep the plugin disabled globally to avoid loading it in unrelated
projects. VS Code multi-root activation still needs verification.

In VS Code, inspect the plugin under **Chat: Open Customizations** > **Plugins**.
For SSH, WSL, or containers, the plugin must be available where Copilot runs.

## Migrate an asset

Select [bb-ai-asset-migrator](../plugins/bb-gh-utils/com.github.copilot/agents/bb-ai-asset-migrator.agent.md)
in the agent picker, or use `/agent` in Copilot CLI. Provide:

- The local `bb-gh-utils` source checkout.
- A source file/directory path or GitHub link, preferably at a commit.

For a repository-only link, the agent asks which assets to migrate. It copies
required resources, adapts references and client-specific formats, and leaves
the source unchanged. It must not stage, commit, or push.

## Atlassian MCP

The plugin bundles the official remote server at
`https://mcp.atlassian.com/v2/mcp` using Streamable HTTP.

- Complete Atlassian OAuth sign-in when prompted by your client. Never put tokens
  or authorization headers in the shared configuration.
- Inspect the connection with **MCP: List Servers** in VS Code or `/mcp` in CLI.
  Use **Configure Tools** in VS Code to select tools available to chat.
- The connection can start automatically when the plugin is enabled. Plugin MCP
  servers are implicitly trusted in VS Code; enabling tools does not require
  their use for every task.
- Access follows your account permissions, OAuth scopes, and organization
  policies. Review write operations and avoid duplicate Atlassian connections.
- Atlassian MCP calls may consume [Rovo credits](https://support.atlassian.com/rovo/docs/rovo-usage-limits/).

See [Atlassian OAuth setup](https://support.atlassian.com/atlassian-ai-gateway/docs/configure-oauth-2-1/)
for authentication requirements.

## Validate local changes

Inspect the package without a persistent installation:

```shell
copilot --plugin-dir ./plugins/bb-gh-utils plugin list --json
copilot --plugin-dir ./plugins/bb-gh-utils skill list --json
copilot --plugin-dir ./plugins/bb-gh-utils instruction list --json
```

These are discovery checks, not proof of runtime behavior. Starting an
interactive session with the plugin may also start its MCP connection.
Do not edit installed caches or assume local instructions override conflicting
plugin rules.

## Validation status

- Previous checks with CLI `1.0.103` recognized the plugin, marketplace
  installation, and commit-message skill.
- Native plugin instruction discovery returned an empty list. The four rule
  files were parsed successfully only when supplied as external instructions.
  **Automatic rule loading remains unverified.**
- VS Code runtime behavior, end-to-end migration, and model-driven skill
  execution have not been tested.
- Atlassian configuration has been checked statically only; connection and OAuth
  authentication have not been tested.

## References

- [CLI plugin reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference)
- [VS Code plugins](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
