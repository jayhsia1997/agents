# Agent Instructions

Personal defaults for any AI coding agent. When this file conflicts with repository-local `AGENTS.md` / `CLAUDE.md` / project docs, the repo wins. The project's existing toolchain and conventions override stack preferences in this file.

## Communication

- Respond to the user in Traditional Chinese (zh-TW); keep code, paths, identifiers, technical terms, and commit messages in English. Switch to English when the user does, or when the repo / task requires it.
- Lead with the answer and code. No preamble, no restating what the user just said.
- Ask when uncertain; do not guess and keep writing.
- Pointing out problems in a plan is more valuable than implementing it as-is.

## Engineering

- Read existing code before writing. Match existing patterns; do not introduce a parallel style.
- Before fixing a bug, state the root cause. Fix the cause; do not pile on defensive checks that mask symptoms.
- Keep changes within the requested scope. No drive-by refactors of unrelated files.
- Do not add comments unless the logic is not self-explanatory; comments in English.
- Handle exceptions at boundaries only. Do not swallow exceptions or use bare `except` / empty `catch`.
- Before claiming done, run the project's existing checks (typecheck / lint / test / build / real usage). Do not report an unrun check as passed.

## Environment

- macOS / zsh.
- Conventional commits; commit messages in English. Follow the repo's style when it differs.
- Without an explicit ask: do not commit, do not push, do not run destructive git operations.

## Skills

Skills live under `~/.agents/skills`. Load one when the task matches.

- Matt Pocock engineering / productivity flows → `ask-matt`
- Authoring or editing skills, `AGENTS.md` / `CLAUDE.md` → `writing-for-agents`
- Python (uv, ruff, Pydantic, FastAPI, pytest) → `python`
- Go (error wrapping, interface placement) → `golang`
