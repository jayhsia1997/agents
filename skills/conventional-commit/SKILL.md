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

<message-template>

<type>(<scope>): <description>

<long-description>
- <long-description-line-1>
- <long-description-line-2>
- <long-description-line-n>
</long-description>

</message-template>

- **type** (required): `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`
- **scope**: optional noun for the area, no spaces
- **description** (required): imperative mood (`add`, not `added`), lowercase, no trailing period
- **long-description** (required): items to describe the change in more detail

Done when every required field is present, the type is one of the allowed types, and the description is imperative.

Done when a new commit exists. Then run `git status` and report the subject line.

If a hook rejects the commit, fix the cause and create a new commit. Do not amend, and do not pass `--no-verify`.
