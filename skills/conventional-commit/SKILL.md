---
name: conventional-commit
description: Draft a Conventional Commits message from the current diff and create the commit.
---

# Conventional Commit

Create one commit for the current change. Running this skill is the request to commit.

If the repo's recent subjects already follow a different convention, match that convention. Otherwise use [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/#specification).

## Inspect

Run in parallel:

- `git status`
- `git diff` and `git diff --cached`
- `git log -8 --oneline`

Done when you can name the files this commit will contain and which message convention the repo uses. If there is nothing to commit, stop and say so.

## Stage

Stage the paths that belong in this commit with `git add <path>`. Leave secrets unstaged (`.env`, credential files, keys) and say which paths you left out.

If the diff mixes unrelated changes, ask which ones belong in this commit before staging.

Done when `git diff --cached` is exactly the change this commit should record.

## Message

```
<type>(<scope>): <description>

<body>

<footer>
```

- **type** (required): `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`
- **scope**: optional noun for the area, no spaces
- **description** (required): imperative mood (`add`, not `added`), lowercase, no trailing period
- **body**: optional; why the change was made
- **footer**: breaking changes and issue references

A breaking change puts `!` after the type or scope, and a `BREAKING CHANGE:` footer.

```
feat(parser): add ability to parse arrays
fix(ui): correct button alignment
docs: update README with usage instructions
refactor: improve performance of data processing
chore: update dependencies
feat!: send email on registration

BREAKING CHANGE: email service required
```

Done when every required field is present, the type is one of the allowed types, and the description is imperative.

## Commit

Pass the message with a HEREDOC. Omit the blank body or footer when unused. Include both when present, separated by a blank line.

```bash
git commit -m "$(cat <<'EOF'
type(scope): description

body

footer
EOF
)"
```

Done when a new commit exists. Then run `git status` and report the subject line.

If a hook rejects the commit, fix the cause and create a new commit. Do not amend, and do not pass `--no-verify`.
