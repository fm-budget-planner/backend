# Issue workflow setup and verification

This implements the scope of [issue #3](https://github.com/fm-budget-planner/backend/issues/3). The [CI/CD plan](ci-cd-foundation.md) remains the technical baseline, and [AGENTS.md](../AGENTS.md) governs collaboration. Adding workflow files does not aci: add issue branch automation and association checks (#3)ctivate them on `main` or make a check required in repository settings. GitHub-hosted execution and repository enforcement must be verified after the user reviews, commits, pushes, and merges the bootstrap PR.

## Selected behavior

- The user manually creates every issue and chooses its scope. Automation never creates issues.
- Only `fabiomoggi` (GitHub user ID `2768565`) may trigger branch creation. A rerun must also be requested by that account.
- Applying `ready-for-development` to an open, user-created issue triggers the workflow. Opening an issue, another label, or an unauthorized actor does not create a branch. The workflow rechecks current issue state, author, and label before writing.
- Branches start at the current `main` commit resolved immediately before native linked-branch creation.
- Names are `<issue-number>-<title_slug>`. Titles are lowercased, accents normalized to ASCII, and spaces/punctuation (including hyphens) replaced by underscores. Repeated separators collapse, the slug is limited to 100 characters, and an empty slug becomes `issue`. Example: `3-add_issue_to_branch_automation_and_issue_association_checks`.
- If any branch starts with `<issue-number>-`, a readiness trigger leaves all matching branches unchanged and creates nothing, even after title or `main` changes. This includes manually created branches and the existing bootstrap branch. With no matching branch, the workflow creates one from current `main`; deleting a branch and labeling the issue again can therefore create a new branch. No branch is reset, renamed, or overwritten.
- Explicit authorization for Codex implementation remains separate from the label event. Before applying code changes, present the exact diff and reasoning and obtain the user’s approval under [AGENTS.md](../AGENTS.md).

## Files and permissions

| File | Responsibility |
|---|---|
| [issue-branch.yml](../.github/workflows/issue-branch.yml) | Native linked-branch creation and per-issue serialization; preserves existing issue-number branches |
| [issue-association.yml](../.github/workflows/issue-association.yml) | Trusted metadata-only PR validation; publishes `issue-association` on the PR head commit |
| [automation-validation.yml](../.github/workflows/automation-validation.yml) | actionlint on every PR to `main` and push to `main`, including documentation-only changes |

The issue workflows keep JavaScript inline in YAML using [actions/github-script](https://github.com/actions/github-script/tree/v8). The action provides its Node.js runtime and authenticated GitHub API client, so no standalone scripts, Node setup, npm installation, or custom unit-test suite are needed. The validation workflow invokes the checksum-pinned actionlint release directly through a short shell step. Application TypeScript/Node.js decisions remain unchanged.

The branch workflow uses `contents: write` and `issues: write`. It calls GitHub's `createLinkedBranch` mutation rather than creating an unrelated Git ref. No PAT, cloud credentials, application dependencies, commits, PRs, or automatic merges are needed.

The association workflow uses `issues: read`, `pull-requests: read`, and `statuses: write`. It runs via `pull_request_target` using inline code from the trusted default-branch workflow; it does not check out repository code or execute PR code. It publishes the result on the PR's actual head SHA, not the base SHA. Stale events do not report success for a newer revision. API errors or invalid/missing issue associations fail the check.

The ordinary validation workflow uses a read-only token without persisted checkout credentials or application secrets. It checks all workflow files with actionlint. It does not run custom automation unit tests. Actions are pinned to full commit IDs. Keep workflow permission changes and association-check implementation under the agreed human review rules.

## Naming-based association

The user accepts the branch name as sufficient branch-to-issue association. The PR check requires a branch in this repository named `<issue-number>-<title_slug>`, an existing issue with that number created by `fabiomoggi`, and a PR targeting `main` with a GitHub-recognized closing link to the same issue. Correctly named manually created branches qualify. The issue may be open or closed; a PR number cannot substitute for an issue number.

The slug must use lowercase letters/numbers separated by underscores. It does not have to track later edits to the issue title. Put `Closes #<issue-number>` in the PR body or use GitHub's Development association. A casual mention such as `Related to #7` is insufficient. The same-repository branch policy remains; fork branches do not qualify.

No origin artifacts, retention policy, workflow history lookup, or base-commit ancestry verification is used. Native issue linking is performed when the automation creates a new branch, but is not required for a manually created branch to pass the PR check. The automation does not attempt to retroactively link or alter existing branches.

A passing status reflects the association when it was checked. Body edits trigger another check; rerun validation if the Development association changes without an event. API errors or an interrupted check must not be treated as success.

References: [native issue branches](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-a-branch-for-an-issue) and [GraphQL issue API](https://docs.github.com/en/graphql/reference/issues).

## Bootstrap and activation

The user created the bootstrap branch through issue #3's native Development control before this automation existed. The actual branch is `3-add-issue-to-branch-automation-and-issue-association-checks`, based on `60ca30af4fb597288051859f2bd86359a8233b52`. Preserve that existing branch. Its hyphenated title is a bootstrap exception; subsequent automated names use the selected underscores.

1. Review the local changes and validation results, then commit and push the bootstrap branch yourself. Open its PR into `main` and link it with `Closes #3`.
2. Confirm `automation-validation` runs on that PR and passes. The association and issue-label workflows execute from the default branch, so their first installation requires manual review of issue #3’s branch/PR link and the agreed non-author human approval. The existing hyphenated bootstrap branch is preserved for that initial PR; the installed naming check requires underscore slugs on subsequent branches. There is no hidden success fallback or generic bootstrap bypass.
3. Merge the reviewed bootstrap PR yourself. Check the `main` validation run. Keep normal implementation merges paused until the required checks and protections below are active.
4. Create the repository label `ready-for-development`. Confirm GitHub Actions permits the pinned checkout and github-script actions and the workflows’ explicit token permissions. No new secret is required.
5. Check the repository's Actions event policy permits this metadata-only `pull_request_target` workflow. GitHub documents an evolving default policy for public repositories; do not assume that installing the file alone makes this event available. See [secure use of pull_request_target](https://docs.github.com/en/actions/reference/security/securely-using-pull_request_target).
6. Use the next real user-created issue, or a verification issue you create and scope yourself, for the hosted checks below. Do not expect adding a label before workflow installation to replay afterward; remove/reapply it when ready.
7. Protect `main` with a repository ruleset or branch protection requiring PRs, **one non-author human approval**, and the exact checks **`automation-validation`** and **`issue-association`**. Require branches to be up to date before merging, block force pushes/deletion, and apply protections without routine bypasses. Select GitHub Actions as the expected status source where available. Preserve any unrelated existing protections. A workflow/job name alone does not make a check required; an administrator must configure and verify this setting after the statuses have appeared.
8. Before normal merges, verify a failed/missing association status and a failed validation result really block merging. Close issue #3 only when its agreed acceptance criteria, including hosted verification and enforcement, have been reviewed as complete. The linked bootstrap PR may automatically close it on merge; reopen it if activation remains outstanding, or explicitly track that remaining scope. Do not claim enforcement is active.

No branch protection, labels, event policies, or repository settings are changed by the local implementation. Their activation is a user/admin handoff, not a claim of completed remote configuration. Preserve the existing restrictions on Codex committing, pushing, or merging.

## Validation

With actionlint 1.7.12 available locally, run from the repository root:

```sh
actionlint
git diff --check
```

The validation workflow downloads actionlint 1.7.12 for its Ubuntu x64 runner, verifies the embedded SHA-256 checksum, and runs it against every YAML workflow file. It runs on all PRs to `main` and pushes to `main`, without path filters that could leave a required check missing. No repository script or package installation is used. Optional shellcheck checks depend on that tool being available to actionlint.

actionlint checks workflow syntax and expressions; it does not establish correctness of the inline JavaScript, live permissions, event delivery, or association behavior. Validate those through the targeted GitHub checks below. No application tests or custom workflow unit tests are included in issue #3; the future application-test decisions remain in the CI/CD plan.

Hosted verification, using only user-created issues and approved work:

| Scenario | Expected result |
|---|---|
| Open an issue without the label | No branch creation |
| A different user applies the label or reruns creation | No branch creation |
| `fabiomoggi` labels a user-created open issue | Native linked branch at the current `main` if no issue-number branch exists |
| Reapply the label after commits or title changes | Existing issue-number branches and commits unchanged; no additional branch |
| PR from that branch with its correct issue link | `issue-association` succeeds on the PR head |
| PR only mentions an issue, names a nonexistent issue, or uses an invalid/fork branch | Association fails; required-check configuration blocks merge |
| Remove/change the PR's closing reference | Rerun the association check; it fails until repaired |
| Correctly named manual branch with its matching issue/PR link | Association passes without proving how the branch was created |
| Invalid workflow syntax in an approved validation change | actionlint fails and `automation-validation` blocks merge |
| API outage or interrupted applicable check | No passing result; required-check configuration blocks merge |

A passing actionlint result does not prove live token permissions, hosted event delivery, or required-check enforcement. Record the corresponding run/PR links during activation. No application code or deployment workflow is included.
