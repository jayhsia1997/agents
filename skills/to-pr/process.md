# To PR: process

Read this when running `/to-pr`. Follow the hard rules in [SKILL.md](SKILL.md).

## 1. Inspect state (parallel)

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

- `HEAD` is `main` (or the default branch) — move work to a topic branch first; do not open a PR from `main`
- Local `main` has commits that are not on `origin/main` — realign (`git fetch` + reset local `main` to `origin/main` **only if the user asks**; otherwise explain and stop)
- Empty diff vs the default branch (committed work only; ignore the temporary dirty tree that `/format` may still clear)

Do **not** abort solely because the tree is dirty here. Step 2 (`/format`) classifies leftover uncommitted changes. If the tree is already dirty with clearly substantive edits before format, stop and ask instead of running format on top of unfinished work.

## 2. Format (before draft)

Call the Skill tool with `"format"` (or follow the `format` skill). Purpose: one last formatter / import-sort pass so the PR does not fail CI style checks.

After `/format` returns:

| Verdict         | Action                                                                                           |
| --------------- | ------------------------------------------------------------------------------------------------ |
| **clean**       | Continue to draft.                                                                               |
| **format-only** | `/format` creates the `style: apply formatter and import sort` commit. Then continue.            |
| **substantive** | **Abort.** Show the diff summary. Ask the user to commit, discard, or split; do not open the PR. |

This is the **only** commit `/to-pr` may create without an explicit "commit" ask in the turn.

## 3. Draft title and body

- **Title:** conventional, imperative. Why belongs in the body. Examples: `feat(auth): refresh token on 401`, `fix(booking): reject overlapping slots`, `docs: clarify PR policy`.
- **Body:** Prefer the repo's own template file. If none, use [templates.md](templates.md) as a starting point.
- Link originating issues/tickets (`Closes #123` ) when commits or the conversation reference them.
- Link originating tickets (Linear or GitHub) in the PR body when commits or the conversation reference them.
  - Linear ticket: end the PR body with `Closes ROO-<ticket number>`.
  - GitHub issue: end the PR body with `Closes #<github-n>`.
- Testing checkboxes must use **this repo's** documented check commands (CI workflow, README, or `AGENTS.md`). Do not copy commands from another stack.
- PR **base** is the default branch (`main` unless the repo says otherwise).

## 4. Branch, push, create

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

## 5. After create (do not do unless asked)

- Do **not** `gh pr merge`.
- Do **not** `git tag` / `git push origin <tag>`.
- If the repo has a post-merge release/tag step, only list it; do not run it unless the user asks.

## 6. Change ticket status(If using Linear)

- Wait ~10s, set the ticket to `In Review`, and attach the GitHub PR URL via Linear `links` (Linear Diffs / PR review).
- Done-on-merge still requires `Closes ROO-<n>` in the PR body; the attachment alone is not enough.
