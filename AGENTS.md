# Working instructions

## Authorization and scope

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

The user manually creates issues and chooses scope. Separately, the approved future issue-to-branch automation creates a linked branch from `main` when an authorized teammate marks an existing issue ready for development. Opening an issue alone does not create a branch. Preserve this automation decision and its issue/branch/PR association checks; do not interpret it as permission for Codex to create issues or start implementation. The readiness label and permitted actors remain setup details to clarify in an authorized automation issue.

## Validation and handoff

- Perform validation appropriate to the authorized change. For documentation-only changes, check instruction consistency, local references, and the final diff; application tests are unnecessary.
- Summarize the changed files, validation performed and results, any limitations, and unresolved questions. Do not claim checks passed if they were not run.
- Leave changes uncommitted for the user's review. Do not proceed to another issue without explicit authorization.

## Issue #1 scope

[Issue #1](https://github.com/fm-budget-planner/backend/issues/1) is documentation only: bring the three approved planning documents into `docs/`, reconcile collaboration instructions, and add this file. Do not add application code, dependencies, or CI/CD workflows as part of this issue. Future implementation requires separate explicit authorization for the user-selected issue and scope.
