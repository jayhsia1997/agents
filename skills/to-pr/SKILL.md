---
name: to-pr
description: Open a GitHub Pull Request for the current branch with gh, using the repo PR template when present. Use when the user asks to create, open, draft, or ship a PR; after /implement and /code-review; or when they ask for a release PR. Completes the idea-to-ship flow that Matt skills leave at commit.
disable-model-invocation: true
---

Respond to the user in Traditional Chinese (zh-TW). Keep code, paths, identifiers, commit messages, PR titles, and PR bodies in English.

# To PR

Turn **already committed** work on a **topic branch** into a GitHub Pull Request.

This skill fills the gap after `/implement` + `/code-review`. It does **not** review the diff (that is `/code-review`) and does **not** split a pile of work (that is `/split-to-prs`).

## Hard rules

- **PR-only onto the default branch** (usually `main`). Never `git merge` into local `main` as a substitute for merging on GitHub. Never push `main` with topic-branch commits that have not been merged via PR.
- **Do not merge** the PR, **do not tag**, **do not publish**. After create, wait for CI to go green; the human merges on GitHub.
- **Do not commit** unless the user explicitly asked to commit in this turn. Uncommitted work → stop and ask.
- **Do not** `--force` push, `--no-verify`, or skip hooks.
- Use **`gh`** for all GitHub operations. Infer the repo from `git remote`.
- If the work should be several PRs, stop and tell the user to run `/split-to-prs` first.
- Before creating the PR, users must review the content first.

## Kind of PR

Pick one:

| Kind | When |
|------|------|
| **Routine** | Feature, fix, docs, refactor, tooling. Default. |
| **Release** | User asked for a release PR, or the repo has a release PR template and the change is a version/release cut. |

If the repo documents a release workflow, follow **that**. Do not invent version-file or changelog conventions. A release PR stays dedicated (release-only diff) unless the user explicitly wants the bump in the same PR as feature work.

## Process

### 1. Inspect state (parallel)

In the current repo:

```bash
git status
git diff
git branch -vv
git log --oneline -15
git rev-parse --abbrev-ref HEAD
git remote show origin | sed -n 's/.*HEAD branch: //p'
```

Then, against the **default branch** (use `origin/main` unless `origin/HEAD` says otherwise):

```bash
git fetch origin
git log --oneline origin/<default>..HEAD
git diff origin/<default>...HEAD
```

Read **all** commits on the branch, not only the latest. Also look for a repo template:

- `.github/PULL_REQUEST_TEMPLATE/default.md`
- `.github/PULL_REQUEST_TEMPLATE/release.md` (release kind)
- `.github/pull_request_template.md` / `.github/PULL_REQUEST_TEMPLATE.md`

If the repo documents a PR policy (`.cursor/rules`, `AGENTS.md`, `docs/agents/`), follow that over this skill's defaults.

**Abort / ask if:**

- Uncommitted or unstaged changes exist
- `HEAD` is `main` (or the default branch) — move work to a topic branch first; do not open a PR from `main`
- Local `main` has commits that are not on `origin/main` — realign (`git fetch` + reset local `main` to `origin/main` **only if the user asks**; otherwise explain and stop)
- Empty diff vs the default branch

### 2. Draft title and body

- **Title:** conventional, imperative. Why belongs in the body. Examples: `feat(auth): refresh token on 401`, `fix(booking): reject overlapping slots`, `docs: clarify PR policy`.
- **Body:** Use [templates.md](./templates.md) as a starting point.
- Link originating issues/tickets (`Closes #123`) when commits or the conversation reference them.
- Testing checkboxes must use **this repo's** documented check commands (CI workflow, README, or `AGENTS.md`). Do not copy commands from another stack.
- PR **base** is the default branch (`main` unless the repo says otherwise).

### 3. Branch, push, create

Only after the draft is ready:

1. Create a topic branch if needed (`feat/…`, `fix/…`, `chore/…`).
2. Push: `git push -u origin HEAD` (request permissions the environment needs).
3. Create the PR:

```bash
gh pr create --title "the pr title" --body "$(cat <<'EOF'
…filled template…
EOF
)"
```

If a template file should be applied by GitHub itself, you may pass `--template <filename>` when the repo has multiple templates; still fill the body so checkboxes are not left empty.

4. Return the **PR URL**. Remind: merge on GitHub after CI is green; do not merge local `main`.

### 4. After create (do not do unless asked)

- Do **not** `gh pr merge`.
- Do **not** `git tag` / `git push origin <tag>`.
- If the repo has a post-merge release/tag step, only list it; do not run it unless the user asks.

## What not to do

- Merge a topic branch into **local `main`** and push `main`.
- Open a PR whose base is not the default integration branch.
- Mix unrelated feature work into a release PR (or the reverse) unless the user chose that exception.
- Assume another repo's version, changelog, or publish conventions.
- Use `/triage` on a PR you just opened — triage is for incoming external requests.
