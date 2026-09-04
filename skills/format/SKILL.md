---
name: format
description: >-
  Run the repo's formatter and import sorter, then classify the resulting diff as
  format-only or substantive. Use when the user asks to format, fix formatting,
  sort imports, or when /to-pr needs a pre-PR format pass.
---

# Format

Apply **this repo's** documented format / lint-fix / import-sort tools. Do not invent a formatter the project does not use.

Respond to the user in Traditional Chinese (zh-TW). Keep commands, paths, and commit messages in English.

## Discover commands

Prefer, in order:

1. Explicit scripts in `package.json` / `Makefile` / `justfile` / `Taskfile` / `pyproject.toml` that the repo documents for format (e.g. `fmt`, `format`, `lint:fix`, `fix`).
2. Tool configs that imply a command: `ruff` (`ruff format` + `ruff check --fix` when the repo uses ruff), Prettier, Biome, `gofmt` / `goimports`, Black + isort (only if already in the toolchain).
3. CI or `AGENTS.md` / README format instructions.

If nothing is discoverable, stop and ask. Do not add a formatter as a side effect of this skill.

Scope: format the files this branch touches when that is what the tool supports; otherwise run the project's usual whole-tree format command.

## Run

1. Snapshot `git status` and `git diff` (including unstaged) **before** formatting.
2. Run the discovered format / import-sort commands.
3. Snapshot again. Review the full post-format diff against the pre-format tree (`git diff` and `git status`).

## Classify the leftover uncommitted diff

Judge **only** the uncommitted changes after the format run (staged + unstaged).

| Verdict         | Meaning                                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------------------- |
| **clean**       | Working tree clean. Done.                                                                                     |
| **format-only** | Diff is limited to formatting and import ordering (see below).                                                |
| **substantive** | Anything else: logic, types, strings (non-quote-style), new/removed symbols, renames, config meaning changes. |

**format-only** means every hunk is explained by one or more of:

- Whitespace, line wrapping, indentation
- Quote / semicolon / trailing-comma style required by the formatter
- Reordering, grouping, or blank-line rules inside import / `from` / `require` / `use` blocks, with **no** import added, removed, or path-changed except as the sorter rewrites equivalent paths the tool already allowed

If any file has a hunk you cannot confidently call format-only, verdict is **substantive**. Prefer asking over guessing.

## Commit policy

- **Standalone `/format`:** report the verdict. Commit **only** if the user asked to commit, or explicitly asked to fix formatting into a commit.
- **Called from `/to-pr`:** if verdict is **format-only**, create one commit and return control to `/to-pr`. If **substantive**, do **not** commit; return the verdict so `/to-pr` can abort and ask.

Commit message (English, conventional):

```text
style: apply formatter and import sort
```

Do not use `--no-verify` or skip hooks. Do not amend unless the user asked and amend rules in the environment allow it. Do not mix format-only files with unrelated dirty paths: if the tree mixes format-only hunks and substantive hunks, verdict is **substantive** (stop; do not partial-commit unless the user directs a split).

## What not to do

- Install or configure a new formatter "to be helpful"
- Autofix lint rules that change behavior (e.g. removing unused exports, rewriting APIs) under the name of format
- Force-push or rewrite published history
