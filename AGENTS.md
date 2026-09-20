# Working instructions

## Authorization and scope

- Before adding, modifying, or deleting code, including workflow configuration, present the exact proposed diff and explain the reasoning. Wait for explicit user approval of that diff before applying it. Authorization to work on an issue does not approve unseen code; subsequent code corrections also require approval of their exact diff.
- Preserve the TypeScript/Node.js implementation stack. For issue #3, keep automation inside YAML workflows using inline JavaScript through `actions/github-script` and shell commands for actionlint. Do not introduce standalone automation scripts or a custom workflow unit-test suite.

- The user creates all GitHub issues and chooses the implementation scope.
- Implement only when the user explicitly authorizes work on a specific issue. Limit changes to that issue's authorized scope; an approved plan, backlog item, readiness action, or review finding is not independent authorization to implement more work.
- Each implementation must happen on a branch associated with its issue. Before editing, verify the repository remote, current branch, issue details when GitHub access is available, and existing files and uncommitted changes. Report unavailable access without claiming verification.
- Never create GitHub issues, commit, push, or merge. The user reviews changes and commits them personally. Leave all implementation changes uncommitted for that review.
- Preserve existing and unrelated work. Inspect existing files before editing and integrate changes without overwriting, reverting, or discarding unrelated content.
- When a decision is needed, ask one question at a time and explain the trade-offs of each alternative. Flag ambiguities; do not silently change approved decisions or expand scope.

## Planning documents

Read these repository-local documents before implementation:

- [Backend foundation strategy](docs/backend-foundation-strategy.md)
- [CI/CD foundation](docs/ci-cd-foundation.md)
- [VS Code handoff](docs/vscode-handoff.md)

Preserve the approved architecture and technical CI/CD decisions. The plans describe the intended foundation, not blanket authorization to implement it. These collaboration rules govern how Codex works on each explicitly authorized issue.

The user manually creates issues and chooses scope. Separately, the approved issue-to-branch automation creates a linked branch from `main` when `fabiomoggi` applies `ready-for-development` to an existing issue. Only that account may trigger or rerun branch creation. Names use `<issue-number>-<title_slug>`, with spaces and punctuation converted to underscores. Opening an issue alone does not create a branch. The user accepts a matching branch name plus an existing issue and the PR’s matching closing link; manually created branches qualify. Do not add branch-origin artifacts or provenance/ancestry verification. Preserve this automation decision and its issue/branch/PR association checks; do not interpret it as permission for Codex to create issues or start implementation. See [issue workflow setup and verification](docs/issue-workflow.md) for implementation and activation details.

## Validation and handoff

- Perform validation appropriate to the authorized change. For documentation-only changes, check instruction consistency, local references, and the final diff; application tests are unnecessary.
- Summarize the changed files, validation performed and results, any limitations, and unresolved questions. Do not claim checks passed if they were not run.
- Leave changes uncommitted for the user's review. Do not proceed to another issue without explicit authorization.

## Issue #1 scope

[Issue #1](https://github.com/fm-budget-planner/backend/issues/1) is documentation only: bring the three approved planning documents into `docs/`, reconcile collaboration instructions, and add this file. Do not add application code, dependencies, or CI/CD workflows as part of this issue. Future implementation requires separate explicit authorization for the user-selected issue and scope.

## Issue #3 scope

The user authorized [issue #3](https://github.com/fm-budget-planner/backend/issues/3): issue-to-branch automation, issue/branch/PR association checks, actionlint validation, targeted verification on GitHub, and setup documentation. Application code and deployment workflows are excluded. Issue #1's documentation-only restriction applies to issue #1, not this separately authorized work.
