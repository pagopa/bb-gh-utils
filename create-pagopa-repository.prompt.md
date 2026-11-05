---
name: create-pagopa-repository
description: "Create a PagoPA GitHub repository with the embedded Building Blocks configuration and register organization authorizations."
argument-hint: "Repository name, README context, authorization area, team ownership model, configuration overrides and permitted remote operations"
agent: agent
---

# Create a PagoPA Repository

Provision a repository using the workflow below. Treat this as an operational guide, not authorization to make every change. Follow the user's current constraints and applicable repository instructions.

## Inputs

Establish these values from the request; ask only for missing decisions:

- Workspace parent directory and new repository name.
- GitHub organization: `pagopa` unless specified otherwise.
- README context, optionally a Confluence URL.
- Authorization repository: `eng-github-authorization` unless specified otherwise.
- Authorization area, such as `technology`, and the owning JSON configuration.
- Team ownership model: use `engineering-tier-zero` or create a repository-specific team; identify the intended repository permission and, for a new team, its members.
- Visibility (public by default), default branch (`main` by default) and any requested overrides to the configuration below.
- Allowed operations: local scaffolding, commits, branch creation, remote creation, pushes, team creation, permission changes and PR creation. Obtain explicit approval for operations not authorized by the request.

Example: create public `pagopa/<new-repository-name>` under `<workspace-parent>/<new-repository-name>`; README context: `<repository-purpose>`; register in `pagopa/eng-github-authorization`, `<authorization-area>`, a new `<new-repository-name>.json`; use existing `engineering-tier-zero` with `admin` (no new team); default branch `main`; apply the embedded configuration without overrides; authorize local scaffolding, branch creation, commits, remote creation/push, repository settings and authorization PR.

## Safety Boundaries

- Prefer Git CLI for local Git operations, fetches and pushes. Do not use GitKraken.
- Git pushes commits but cannot create a GitHub repository or change GitHub settings. Use an available GitHub integration, API or authenticated `gh` for those operations; `gh` is not mandatory.
- If `gh` is needed and authentication is unavailable, stop the affected remote operations and ask the user to run `gh auth login`. Do not request, print or handle tokens, passwords or private keys.
- Do not install missing tools without permission. If a requested operation is unavailable or forbidden, stop and explain the blocker; honor any response wording explicitly requested by the user.
- Never revert existing user changes, force-push, reset branches or bypass protections. Preserve unrelated changes and stage only files belonging to this task.
- Do not copy application code, infrastructure, workflows or configuration merely to imitate directory structure. Never add dummy files or `.gitkeep` unless requested.
- Git does not track empty directories. Explain that a folders-only skeleton stays local until real files are added.
- Use read-only subagents for independent comparisons only when delegation is permitted. Keep all writes and validation under the main agent's control.

## 1. Inspect the Local Inputs

Read applicable instructions and inspect only the relevant paths. Check the destination is absent or understand its existing contents before modifying it.

Inspect Git status, remotes, current branches and default branch in the authorization repository. Read its contribution conventions, PR templates, the relevant area configuration and shared team definitions.

Identify the abstraction that actually controls repository access. In `eng-github-authorization`, repository entries and team associations do not themselves create a GitHub repository or set visibility. Confirm this still holds in the current implementation.

For Confluence-backed context, retrieve the document through an available authenticated integration. Produce a short public-safe README, not a copy of internal design notes. Distinguish implemented functionality from planned automation; omit sensitive links, diagrams and details unless approved for publication.

## 2. Scaffold the New Repository

- Create a sibling directory under the requested workspace parent.
- Add a concise README and a generic `.gitignore`; do not exclude expected source or template files.
- Create root `CODEOWNERS` with `* @<organization>/<selected-owner-team>`, using the ownership decision in step 3.
- Create only useful directories for the new repository's purpose; do not copy product-specific files, workflows, secrets or environments from other repositories.
- Initialize Git with the agreed default branch, normally `main`.

Create `.github/copilot/settings.json` in the new repository to enable the Building Blocks Copilot plugin:

```json
{
  "extraKnownMarketplaces": {
    "bb-tools": {
      "source": {
        "source": "github",
        "repo": "pagopa/bb-gh-utils"
      }
    }
  },
  "enabledPlugins": {
    "bb-gh-utils@bb-tools": true
  }
}
```

If the file already exists, merge these entries without removing unrelated settings; ask before replacing conflicting values.

Immediately validate the touched files: Markdown diagnostics, Copilot settings JSON syntax and required entries, ignore behavior, CODEOWNERS ownership and the absence of dummy files. Real source files, `.github/copilot/settings.json` and `.env.example` must remain trackable; local secrets and generated output must be ignored.

Commit only when authorized. If the user requests temporary local files without commit or push, leave them untracked and stop before publication.

## 3. Register Organization Authorizations

Choose the repository's owning team based on its purpose; do not inherit another repository's team associations or permissions:

- For shared Building Blocks engineering utilities or infrastructure, prefer the existing `engineering-tier-zero` team when it fits the requested ownership.
- For a repository with a distinct product or service owner, use a dedicated team. Confirm the team name, members and permission with the user when these are not specified; add its definition to the correct shared team configuration as well as associating it with the repository.
- If the repository purpose does not make the appropriate model clear, ask the user to choose before changing authorization files. Do not create a team, add members or grant permissions without authorization.

Verify that the selected team exists in the authorization configuration or define it when creating a new team. Confirm the intended permission level. Register the repository in a dedicated `<new-repository-name>.json` under the selected authorization area, following its schema. Avoid duplicate repository declarations and unnecessary collaborators.

For example, the shared utilities model may associate the repository with `engineering-tier-zero`:

```json
{
  "repo_name": "<new-repository-name>",
  "team_associations": [
    { "permission": "admin", "team_name": "engineering-tier-zero" }
  ]
}
```

For a dedicated team, use that team's confirmed name and permission instead; do not also add `engineering-tier-zero` unless requested.

Set CODEOWNERS to the selected owner team. A CODEOWNERS team must be visible and have explicit write access to the repository; verify these prerequisites remotely before claiming CODEOWNERS is effective. If authorization is pending Terraform application, report it as a prerequisite rather than bypassing that workflow.

Run the repository's structural JSON checks, uniqueness checks and available pre-commit checks. Propagate failures correctly, using `pipefail` if filtering command output. Do not change unrelated invalid entries.

Fetch the remote base branch before inspecting the PR diff. Prefer a dedicated authorized branch based on the current remote default branch. If reusing an existing branch, verify its commits and three-dot diff contain only intended changes; do not silently reset or rebase user work.

Commit and push the authorization change only when authorized. Confirm the remote branch SHA matches the local commit.

## 4. Create and Publish the GitHub Repository

Check whether the remote repository already exists and whether the current account can administer it. Distinguish a missing repository from insufficient access; do not assume every 404 means absence.

Create an empty repository with the agreed visibility, without generating a second README, gitignore or license. An authenticated GitHub API/integration, `gh repo create`, or manual creation in GitHub are all valid options.

Configure and verify `origin`, then push the default branch with Git. Do not overwrite an existing remote or unrelated history. Verify visibility, default branch, remote commit SHA and published file contents.

## 5. Apply Repository Configuration

Use the following embedded baseline; no reference repository is required. Apply remote settings only if authorized, honoring explicit user overrides. Read existing settings and show proposed access changes before writing.

### General settings

Apply through the repository settings API, with visibility/default branch taken from the confirmed inputs:

```json
{
  "has_issues": true,
  "has_projects": true,
  "has_wiki": true,
  "has_discussions": false,
  "allow_forking": true,
  "is_template": false,
  "web_commit_signoff_required": false,
  "allow_squash_merge": true,
  "allow_merge_commit": false,
  "allow_rebase_merge": false,
  "allow_auto_merge": false,
  "allow_update_branch": false,
  "delete_branch_on_merge": true,
  "squash_merge_commit_title": "PR_TITLE",
  "squash_merge_commit_message": "COMMIT_MESSAGES",
  "merge_commit_title": "MERGE_MESSAGE",
  "merge_commit_message": "PR_TITLE"
}
```

`delete_branch_on_merge` enables **Automatically delete head branches** after PR merge.

### Release immutability

Enable immutable releases with `PUT /repos/{owner}/{repo}/immutable-releases`. Verify with `GET` on the same endpoint that `enabled` is `true`. Published releases cannot have their tags or assets changed or deleted; prepare assets in a draft before publishing.

### GitHub Actions

- Actions enabled, `allowed_actions: "all"`, `sha_pinning_required: false`.
- Workflow permissions: `default_workflow_permissions: "write"`, `can_approve_pull_request_reviews: true`.
- These are permissive baseline settings, not least-privilege recommendations. Disclose them before applying; do not override stricter organization policies.

### Security

Enable vulnerability alerts through their dedicated endpoint. Apply these `security_and_analysis` statuses through supported settings endpoints:

```json
{
  "dependabot_security_updates": { "status": "disabled" },
  "secret_scanning": { "status": "enabled" },
  "secret_scanning_ai_detection": { "status": "disabled" },
  "secret_scanning_delegated_alert_dismissal": { "status": "disabled" },
  "secret_scanning_delegated_bypass": { "status": "enabled" },
  "secret_scanning_non_provider_patterns": { "status": "enabled" },
  "secret_scanning_push_protection": { "status": "enabled" },
  "secret_scanning_validity_checks": { "status": "enabled" }
}
```

Do not treat read-only or unsupported response fields as writable. If an option requires organization/security-configuration management or manual setup, report the exact pending setting; do not silently omit it or change organization-wide policy.

### Default branch protection

Apply classic protection to the confirmed default branch using this payload:

```json
{
  "required_status_checks": { "strict": true, "checks": [] },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": true,
    "require_last_push_approval": false,
    "required_approving_review_count": 1
  },
  "restrictions": null,
  "required_linear_history": false,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "block_creations": false,
  "required_conversation_resolution": true,
  "lock_branch": false,
  "allow_fork_syncing": false
}
```

Signed commits are not required (`required_signatures.enabled: false`); manage this through the dedicated endpoint. No named status checks are required by the baseline: do not invent workflows/checks. If the user adds required checks, verify they exist before enabling them.

Create or update one repository-owned ruleset with this payload:

```json
{
  "name": "Copilot review for default branch",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "exclude": [], "include": ["~DEFAULT_BRANCH"] } },
  "rules": [{ "type": "deletion" }, { "type": "non_fast_forward" }],
  "bypass_actors": []
}
```

Despite its name, this ruleset does not request Copilot reviews. Preserve inherited organization rulesets without duplicating them; do not weaken existing protections without explicit approval.

### Access and verification

Compare teams, direct collaborators and pending invitations against the selected ownership model. Apply only confirmed permissions through the authorized management workflow; preserve inherited access and organization membership.

After each meaningful write, read back and compare the settings against the baseline and user overrides. Verify release immutability, Actions permissions, vulnerability alerts, security statuses, branch protection, signature requirements and ruleset contents separately. If a write fails, stop dependent operations and report exactly what succeeded and what remains pending.

The repository creator may have a direct admin grant outside the selected ownership model. Remove it only when explicitly authorized, after confirming active membership in an admin team and that the team has admin access to the new repository. Verify administrative access remains available after removal.

## 6. Open the Authorization PR

Use the repository's current PR template and contribution conventions; inspect organization-level templates when available. If there is no template, use a concise summary, changes, validation and prerequisites.

Open against the verified default branch. Before creating, check for an existing open PR for the head branch. Include only the authorization diff, not files from the newly created repository.

If the target repository has not yet been published, keep the PR in draft and state that the authorization configuration requires the repository to exist. After publication, update the prerequisite in the PR body and mark it ready for review when authorized. Verify `isDraft: false` and the correct base branch. Do not merge the PR.

## Completion Report

Report the repository URL, PR URL and draft status, local and remote commit state, validations performed, and any unresolved blockers or deliberate overrides to the embedded baseline. Mention that empty folders are local-only when applicable.

Do not claim team permissions, protections, publication or PR status were applied without verifying their remote state.
