---
name: to-pr
description: Open a GitHub Pull Request for the current branch with gh, using the repo PR template when present. Use when the user asks to create, open, draft, or ship a PR; after /implement and /code-review; or when they ask for a release PR. Completes the idea-to-ship flow that Matt skills leave at commit.
disable-model-invocation: true
---

Keep code, paths, identifiers, commit messages, PR titles, and PR bodies in English

# To PR

Turn **already committed** work on a **topic branch** into a GitHub Pull Request.

This skill fills the gap after `/implement` + `/code-review`. It does **not** review the diff (that is `/code-review`) and does **not** split a pile of work (that is `/split-to-prs`).

Before creating the PR, users must review the content first.

## Hard rules

- **PR-only onto the default branch** (usually `main`). Never `git merge` into local `main` as a substitute for merging on GitHub. Never push `main` with topic-branch commits that have not been merged via PR.
- **Do not merge** the PR, **do not tag**, **do not publish**. After create, wait for CI to go green; the human merges on GitHub.
- **Do not commit** unless the user explicitly asked to commit in this turn, **except** the format-only commit allowed in [process.md](process.md) after `/format`.
- **Do not** `--force` push, `--no-verify`, or skip hooks.
- Use **`gh`** for all GitHub operations. Infer the repo from `git remote`.
- If the work should be several PRs, stop and tell the user to run `/split-to-prs` first.

## Kind of PR

Pick one:

| Kind        | When                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------- |
| **Routine** | Feature, fix, docs, refactor, tooling. Default.                                                             |
| **Release** | User asked for a release PR, or the repo has a release PR template and the change is a version/release cut. |

If the repo documents a release workflow, follow **that**. Do not invent version-file or changelog conventions. A release PR stays dedicated (release-only diff) unless the user explicitly wants the bump in the same PR as feature work.

## Process

1. **Inspect** — follow [process.md](process.md). Abort when it says to.
2. **Format** — run `/document-format` before drafting. If the leftover diff is format-only, let `/document-format` create the style commit; if substantive uncommitted work remains, stop and ask. Details in process.md.
3. **Draft** title and body — use the repo template if present; otherwise [templates.md](templates.md). Details in process.md.
4. **Push and create** — only after the draft is ready. Commands in process.md. Return the **PR URL**.
5. **After create** — do nothing unless asked (no merge, no tag). List any documented post-merge steps; do not run them.

## What not to do

- Merge a topic branch into **local `main`** and push `main`.
- Open a PR whose base is not the default integration branch.
- Mix unrelated feature work into a release PR (or the reverse) unless the user chose that exception.
- Assume another repo's version, changelog, or publish conventions.
- Use `/triage` on a PR you just opened — triage is for incoming external requests.
