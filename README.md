# Personal AI agent config

Single source of truth for global coding-agent instructions and skills.

This repo is meant to live at `~/Projects/github/personal/agents` and be linked as `~/.agents`. Harness-specific paths (`~/.claude`, `~/.codex`) only hold discovery symlinks.

## Layout

```text
.
├── AGENTS.md           # Global instructions (canonical)
├── CLAUDE.md           # Pointer → ~/.agents/AGENTS.md
├── .skill-lock.json    # Registry install metadata (source / hash)
├── rules/              # Cursor user rules (canonical); ~/.cursor/rules → here
│   ├── *.mdc
│   └── …
├── skills/             # All skills tracked in git (registry + personal)
│   ├── python/         # Personal
│   ├── golang/         # Personal
│   └── …               # e.g. mattpocock/skills installs
└── .gitignore
```

## Design

- **One instruction file:** edit `AGENTS.md` only. `CLAUDE.md` is a text path so Claude Code can follow it without duplicating content.
- **Lean globals:** communication, engineering, git safety. Stack rules live in skills.
- **Cursor rules in git:** edit `rules/*.mdc` here; `~/.cursor/rules` is a symlink so Cursor loads them globally.
- **Repo wins:** project-local `AGENTS.md` / `CLAUDE.md` override this repo.
- **Skills in git:** track the whole `skills/` tree so personal edits are diffable and recoverable. `.skill-lock.json` still records upstream source/hash for reinstalls.

## Machine setup

```bash
# Clone (or use your existing checkout), then:
ln -sfn "$HOME/Projects/github/personal/agents" "$HOME/.agents"

mkdir -p "$HOME/.claude" "$HOME/.codex"
ln -sfn "$HOME/.agents/CLAUDE.md" "$HOME/.claude/CLAUDE.md"
ln -sfn "$HOME/.agents/skills"    "$HOME/.claude/skills"
ln -sfn "$HOME/.agents/AGENTS.md" "$HOME/.codex/AGENTS.md"

# Cursor global rules (replace a real ~/.cursor/rules dir if present)
mkdir -p "$HOME/.cursor"
ln -sfn "$HOME/.agents/rules" "$HOME/.cursor/rules"
```

After clone, skills are already in the tree. Optionally refresh from upstream with the Skills CLI (see below).

### Updating registry skills

This checkout _is_ the global agents home (`~/.agents` → this repo). Registry installs are **global**, tracked in `.skill-lock.json`.

```bash
npx skills update -g
npx skills list -g
```

**Gotcha — scope:** inside this repo, bare `npx skills list` / `npx skills update` default toward **project** scope. Always pass `-g` here.

**Gotcha — `update` is lock-hash only:** it compares `skillFolderHash` to the remote GitHub tree SHA and does **not** repair local edits or deletions. Prefer **git** to restore your tracked copy; use a forced reinstall only when you intentionally want upstream to overwrite local:

```bash
npx skills add mattpocock/skills --skill ask-matt -g -y
```

If the CLI prints `Failed to fetch tree for …` and then `All global skills are up to date`, the check failed — fix network / GitHub auth and retry.

**Conflict note:** `skills add` / a successful `update` can overwrite local changes. Diff or commit before refreshing from upstream.

## What is versioned

| Tracked                               | Ignored    |
| ------------------------------------- | ---------- |
| `AGENTS.md`, `CLAUDE.md`, `README.md` | `.cursor/` |
| `.skill-lock.json`                    |            |
| Entire `rules/` tree                  |            |
| Entire `skills/` tree                 |            |
| `.gitignore`                          |            |

## Personal skills

| Skill                 | When                                                                  |
| --------------------- | --------------------------------------------------------------------- |
| `python`              | uv, ruff, Pydantic v2, FastAPI layering, pytest                       |
| `golang`              | Wrapped errors, no panic-for-control-flow, interfaces at the consumer |
| `conventional-commit` | Conventional Commits message from the diff, then create the commit    |
