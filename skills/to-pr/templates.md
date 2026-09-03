# PR body templates

Use these **only when the current repo has no** `.github/PULL_REQUEST_TEMPLATE*` file. If a repo template exists, fill that file's structure instead.

Bodies are English. Check real boxes; delete optional sections that do not apply.

Testing lines must be this repo's actual commands (from CI, README, or `AGENTS.md`).

## Routine (feature, fix, docs, refactor, tooling)

```markdown
## Summary

<one short paragraph: what changed and why>

## Type of change

- [ ] Bug fix
- [ ] New feature or API
- [ ] Documentation only
- [ ] Refactor (no intended behavior change)
- [ ] Dependency or tooling

## Implementation notes

<!-- Optional: design decisions, trade-offs, or follow-ups. Delete if unused. -->

## Testing

- [ ] <this repo's check commands>

## Checklist

- [ ] PR targets **`main`** (or this repo's default branch)
- [ ] CI green before merge
```

## Release

Use only when the user asked for a release PR **and** the repo has no release template. Fill from **this repo's** release docs (version source, notes file, tag format). Do not invent files or commands.

```markdown
## Summary

Release **`<version>`**.

## What is in this release

- <short list for reviewers>

## Release checklist

- [ ] Version identifier matches this repo's release convention
- [ ] Release notes updated if this repo keeps them

## Testing

- [ ] <this repo's check commands>

## Post-merge (maintainer)

<!-- Copy the repo's documented post-merge steps. Delete this section if none. -->
```
