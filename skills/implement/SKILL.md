---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Before you start, checkout to main and pull the latest changes and create a new branch for the work.

After creating a new branch, commit any updates to the ADR and Context files made before the branch was created (potentially in the stash).

Branch name should be in the format `{type}/{issue-number}-{short-description}` except for release and spike branches.
For release branches, the branch name should be `release/{version}`
For spike branches, the branch name should be `spike/{short-description}`

Type:

- feat: new feature
- fix: bug fix
- hotfix: hotfix from production tag
- refactor: refactor
- perf: performance
- test: test only
- docs: documentation
- chore / build / ci: dependencies, tools, pipeline
- release: release branch like `release/{version}`
- spike: spike branch for exploratory work like `spike/{short-description}`

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the current branch.

Do not open a Pull Request from this skill. When the user asks to ship, they invoke `/to-pr`.
