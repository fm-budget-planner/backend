# Continue authorized issue work in Codex for VS Code

## User instruction

Use Codex inside Visual Studio Code, working in the local backend repository checkout. The user creates all GitHub issues and chooses the implementation scope. Implement only when the user explicitly authorizes work on a specific issue, and only on a branch associated with that issue. Approval of the architecture and CI/CD plan does not authorize the entire setup.

Never create GitHub issues, commit, push, or merge. The user reviews changes and commits personally. Preserve existing and unrelated work; perform appropriate validation, summarize the results and unresolved questions, and leave changes uncommitted. When a decision is needed, ask one question at a time and explain the trade-offs of each alternative. Follow the root [AGENTS.md](../AGENTS.md).

## Read the approved plans

- [Backend foundation strategy](backend-foundation-strategy.md)
- [CI/CD foundation](ci-cd-foundation.md)

These files and this handoff are in the repository's `docs/` directory. Read them and [AGENTS.md](../AGENTS.md) before implementation; no external planning directory is required. Preserve the approved architecture and technical CI/CD decisions, while following the current collaboration rules and the explicitly authorized issue scope.

## Verified state for issue #1 — September 19, 2026

- The user manually created the organization at https://github.com/fm-budget-planner. Its public page was verified during the earlier planning session.
- The user chose `backend` as the repository name and reconfirmed `fm-budget-planner` as the owner.
- Target: `fm-budget-planner/backend`, with public visibility selected; current visibility/settings were not inspected in this issue.
- The local checkout exists. Its fetch and push origin is `https://github.com/fm-budget-planner/backend.git`.
- The current branch was verified as `1-add-foundation-documentation-and-codex-working-instructions`.
- [Issue #1: Add foundation documentation and Codex working instructions](https://github.com/fm-budget-planner/backend/issues/1) was read through the GitHub connector. Its scope covers the three planning documents and root working instructions, with no application code or CI/CD workflows, and requires uncommitted results for user review. The user explicitly authorized implementation of this issue.
- Before implementation, the checkout contained no project files, root `AGENTS.md`, destination documents, or uncommitted changes. There was no existing content to integrate.
- GitHub Free is selected; the account's actual plan has not been inspected.
- No backend code, pipeline, or cloud configuration has been implemented in this work.
- VS Code was installed at the earlier handoff; the Codex extension's current installation state was not checked in this issue.
- The earlier standalone application's GitHub connection did not expose the intended organization. The connector used for issue #1 now provides read access. Local Git write access and administrative permissions remain unverified. Verify access independently when needed; do not assume one connection's state applies to another.

## Start here

1. Read [AGENTS.md](../AGENTS.md), this handoff, and both approved plans. Identify the issue and scope the user has explicitly authorized. Do not select or create another issue.
2. Verify the local repository, origin, and current issue-associated branch. Read the issue if GitHub access is available; otherwise report the limitation without claiming verification. Inspect existing files and uncommitted changes and preserve unrelated work.
3. Implement only the authorized issue scope on its associated branch. Issue #1 is documentation only: no application code, dependencies, or CI/CD workflows.
4. If a decision or ambiguity needs resolution, ask one question at a time and explain the trade-offs of each alternative. Preserve settled architecture and CI/CD decisions.
5. Perform validation appropriate to the change. For issue #1, check collaboration-instruction consistency, local references, source preservation, and the final diff; application tests are unnecessary.
6. Summarize changed files, validation results and limitations, and unresolved questions. Leave all changes uncommitted for the user to review and commit. Never push or merge, and do not continue to another issue without explicit authorization.

## Issue creation, branch automation, and bootstrap background

The user manually creates every issue and chooses its scope. The approved future automation has a separate responsibility: an authorized teammate marks an existing issue ready for development, triggering creation of a linked working branch from the current `main`. Opening an issue alone does not create a branch, and marking it ready does not itself authorize Codex implementation. Preserve the issue/branch/PR association check, repeat-trigger protection, and other technical automation decisions in the [CI/CD foundation](ci-cd-foundation.md).

Before that automation exists, GitHub's native issue-linked branch mechanism can supply the branch for a user-created, explicitly authorized setup issue. The readiness automation can be exercised after installation on the default branch through the normal reviewed process. The exact readiness label, permitted actors, and branch naming convention remain questions for that future issue; they do not block this documentation change.

The earlier handoff proposed an empty-tree root commit referencing the bootstrap issue, checked to contain zero files, as an interpretation of the issue-first policy for an entirely empty repository. This would supply the initial `main` reference without introducing project files there. Under the current collaboration rules, any such issue creation, commit, and push belongs to the user. GitHub's [Create Reference API](https://docs.github.com/en/rest/git/refs#create-a-reference) cannot initialize a repository without branches, and [native linked branches](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-a-branch-for-an-issue) need an existing base commit. The current checkout already has the issue #1 branch; do not repeat initialization. This background is not permission for Codex to commit or push.

## Policies to preserve

- TypeScript/Node.js; hexagonal architecture and DDD; domain requirements and model remain deferred.
- One public monorepo with separate Core and Firebase Runtime packages and validation workflows. Runtime consumes Core from the same immutable commit.
- Core is Firebase-free. Dependency impact can trigger separate runtime validation for Core changes.
- Every implementation starts with a user-created issue and its linked branch; this applies to people and automation. Codex implements only the explicitly authorized scope.
- Mandatory applicable CI checks and one non-author human approval before merge.
- Separate SonarQube Cloud projects using Sonar way, mandatory from first integration; verify free-plan fit.
- Dependency Review blocks introduced high/critical vulnerabilities. Native secret protection, actionlint, and Dependabot alerts are selected. Automatic Dependabot update PRs remain disabled.
- Codex GitHub PR reviews are on-demand and advisory, separate from using Codex in VS Code.
- Automatic staging for merged deployable changes. Production requires a manual request and designated approval of a fixed staging-validated revision.
- No new paid subscription or credits are authorized. No automatic rollback.
- Frontend implementation, scheduled backups/PITR, account cancellation, and application/runtime monitoring and budget alerts remain deferred.
- The user performs organization creation manually; do not create another organization.

The full plans supply the technical detail behind this abbreviated reminder; [AGENTS.md](../AGENTS.md) records the collaboration rules. Do not reopen settled strategic choices or invent product requirements. Do not assume this VS Code chat has the previous conversation's history, app connections, or permissions. Flag conflicts for clarification instead of silently expanding scope.

## First prompt

> Read AGENTS.md and the three planning documents under docs/. Verify the fm-budget-planner/backend checkout, the current issue-associated branch, available GitHub issue access, and existing work. Implement only the issue and scope I have explicitly authorized. I create issues, review changes, and commit myself. Never create issues, commit, push, or merge. Preserve unrelated work and the approved technical decisions. Ask one decision question at a time with trade-offs. Validate the authorized change, summarize changed files, results, and unresolved questions, and leave everything uncommitted for my review.
