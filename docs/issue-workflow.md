# Issue workflow setup and verification

This records [issue #5](https://github.com/fm-budget-planner/backend/issues/5): validate issue-to-branch automation and remove the association workflow, retaining branch creation, actionlint, and manual issue/PR association review. The [CI/CD plan](ci-cd-foundation.md) remains the technical baseline, and [AGENTS.md](../AGENTS.md) governs collaboration. The initial workflows were merged in [PR #4](https://github.com/fm-budget-planner/backend/pull/4); the user subsequently assigned removal of the association workflow to issue #5 on `5-validate_issue_to_branch_automation`. Changes take effect on GitHub after the user reviews, commits, pushes, and merges them. A workflow file does not make its check required in repository settings.

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
| [automation-validation.yml](../.github/workflows/automation-validation.yml) | actionlint on every PR to `main` and push to `main`, including documentation-only changes |

The branch workflow keeps JavaScript inline in YAML using [actions/github-script](https://github.com/actions/github-script/tree/v8). The action provides its Node.js runtime and authenticated GitHub API client, so no standalone scripts, Node setup, npm installation, or custom unit-test suite are needed. The validation workflow invokes the checksum-pinned actionlint release directly through a short shell step. Application TypeScript/Node.js decisions remain unchanged.

The branch workflow uses `contents: write` and `issues: write`. It calls GitHub's `createLinkedBranch` mutation rather than creating an unrelated Git ref. No PAT, cloud credentials, application dependencies, commits, PRs, or automatic merges are needed.

The ordinary validation workflow uses a read-only token without persisted checkout credentials or application secrets. It checks all workflow files with actionlint. It does not run custom automation unit tests. Actions are pinned to full commit IDs. Keep workflow permission changes under the agreed human review rules.

## Manual issue/PR association review

The user accepts the branch name as sufficient branch-to-issue association. Reviewers verify a branch in this repository named `<issue-number>-<title_slug>`, an existing issue with that number created by the user, and a PR targeting `main` with a closing link to that same issue. Correctly named manually created branches qualify. The existing hyphenated bootstrap branch retains its documented exception.

The slug uses lowercase letters/numbers separated by underscores and does not have to track later issue-title edits. Put `Closes #<issue-number>` in the PR body or use GitHub's Development association. Reviewers check this link before approving and merging; naming and linking mistakes do not produce an automated failure. Actionlint does not validate issue/PR associations.

Native issue linking still happens when branch automation creates a new branch. The automation does not retroactively link or alter existing branches. No origin artifacts, retention policy, workflow-history lookup, or ancestry verification is required.

References: [native issue branches](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-a-branch-for-an-issue) and [GraphQL issue API](https://docs.github.com/en/graphql/reference/issues).

## Bootstrap and activation

The user created the bootstrap branch through issue #3's native Development control before this automation existed. The actual branch is `3-add-issue-to-branch-automation-and-issue-association-checks`, based on `60ca30af4fb597288051859f2bd86359a8233b52`. Preserve that existing branch. Its hyphenated title is a bootstrap exception; subsequent automated names use the selected underscores.

The initial bootstrap PR is already merged. For this removal and remaining activation:

1. Review the removal and documentation changes, then commit and push them yourself and open a PR into `main`. Link this PR to issue #5 with `Closes #5`. Confirm `automation-validation` passes, manually review the issue/branch/PR association, and obtain the agreed non-author human approval before merging.
2. If `issue-association` was configured as a required status in a ruleset or branch protection, remove that requirement so deleting the workflow cannot leave PRs waiting for a status that will no longer run. Preserve unrelated protections. This repository change does not modify GitHub settings.
3. After you merge, check the `main` validation run. The old association workflow runs from the default branch and may still run on the removal PR until that deletion is merged; it is not part of the retained design.
4. Create `ready-for-development` if it does not exist. Confirm GitHub Actions permits the pinned checkout and github-script actions and their explicit token permissions. No new secret or `pull_request_target` event configuration is needed for the retained workflows.
5. Use the next real user-created issue, or a verification issue you create and scope yourself, to verify branch creation and repeated triggers. Adding a label before workflow installation does not replay afterward; remove/reapply it when ready. Branch creation works independently of actionlint and branch protection.
6. Protect `main` with a ruleset or branch protection requiring PRs, **one non-author human approval**, and **`automation-validation`**, plus any other applicable checks. Require branches to be up to date, block force pushes/deletion, and preserve unrelated protections. Verify a failed or missing required validation result blocks merging. These settings enforce merge policy; they do not enable branch creation.
7. Review the revised scope and remaining hosted verification before treating activation as complete. Keep issue #5 open while its agreed work remains, or explicitly track any remaining scope before closing it; issue #3 records the historical installation. Codex does not create or edit issues on the user's behalf without authorization.

No branch protection, labels, event policies, or repository settings are changed by the local implementation. Their activation is a user/admin handoff, not a claim of completed remote configuration. Preserve the existing restrictions on Codex committing, pushing, or merging.

## Validation

With actionlint 1.7.12 available locally, run from the repository root:

```sh
actionlint
git diff --check
```

The validation workflow downloads actionlint 1.7.12 for its Ubuntu x64 runner, verifies the embedded SHA-256 checksum, and runs it against every YAML workflow file. It runs on all PRs to `main` and pushes to `main`, without path filters that could leave a required check missing. No repository script or package installation is used. Optional shellcheck checks depend on that tool being available to actionlint.

actionlint checks workflow syntax and expressions; it does not establish correctness of the inline JavaScript, live permissions, event delivery, or branch-creation behavior. Validate those through the targeted GitHub checks below. No application tests or custom workflow unit tests are included in issue #5; the future application-test decisions remain in the CI/CD plan.

Hosted verification, using only user-created issues and approved work:

| Scenario | Expected result |
|---|---|
| Open an issue without the label | No branch creation |
| A different user applies the label or reruns creation | No branch creation |
| `fabiomoggi` labels a user-created open issue | Native linked branch at the current `main` if no issue-number branch exists |
| Reapply the label after commits or title changes | Existing issue-number branches and commits unchanged; no additional branch |
| PR issue/branch/link review | Reviewer verifies the association manually; no automated association status |
| Invalid workflow syntax in an approved validation change | actionlint fails and `automation-validation` blocks merge |

A passing actionlint result does not prove live token permissions, hosted event delivery, or required-check enforcement. Record the corresponding run/PR links during activation. No application code or deployment workflow is included.

## Verification recorded for issue #5

GitHub reports the [Issue branch #5 run](https://github.com/fm-budget-planner/backend/actions/runs/35546386463) completed successfully. The branch `5-validate_issue_to_branch_automation` exists at `0bc1e68c82127eb7f3471fbc71993aa851fe684b`, the main revision used by that run. Issue #5 is open and carries `ready-for-development`. These observations verify the successful run and resulting branch reference; native Development-link visibility, repeat-trigger behavior, and other scenarios above remain to be checked. This inspection did not create or relabel an issue, rerun a workflow, or change repository protections.
