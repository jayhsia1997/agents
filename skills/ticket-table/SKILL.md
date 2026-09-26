---
name: ticket-table
description: Present tickets that were just created or supplied by the user as a compact Unicode table showing each ticket's repository, title, and blocking dependencies. Use after a ticket-creation workflow such as to-tickets, or when the user asks for a ticket dependency table; do not create or modify tickets.
---

# Ticket Table

Summarize the completed ticket set as one dependency table. This skill is read-only: do not create, edit, label, close, or link tickets.

## Source data

Prefer the ticket identifiers returned by the immediately preceding creation workflow. If the user supplies ticket files, issue URLs, or a tracker query instead, read those sources before rendering the table. Do not guess missing identifiers, repositories, titles, or blockers; use `?` for an unavailable value and briefly note the missing source data after the table.

Include every ticket in the supplied or just-created set exactly once. Do not include a parent issue unless it was itself one of the created tickets.

## Normalize rows

Build these columns:

- `#`: the tracker issue number. For local ticket files, use their dependency-order number, preserving leading zeroes.
- `Repo`: the repository's short name, not its owner or full URL.
- `Title`: the exact ticket title, without issue-number prefixes.
- `Blocked by`: comma-separated blocker references. Use `<repo>#<number>` for tracker issues and `<repo>#<local-number>` for local tickets. Use `—` when there are no blockers. Preserve a configured or already-established repository alias; otherwise use the same short name shown in `Repo`.
- `Needs Migration`: `✅` if the ticket needs to be generated the alembic migration script, `❌` otherwise.

Order rows by dependency order when that order is known, with blockers before blocked tickets. Otherwise preserve the creation or input order. Never imply a dependency merely from row order.

## Render

Return a markdown table with the following columns:


```markdown
| #   | Repo     | Title                                        | Blocked By  | Needs Migration |
| --- | -------- | -------------------------------------------- | ----------- | --------------- |
| 48  | core-api | Devotion schema and anonymous<br/>Today read | —           | ✅              |
| 51  | core-api | Signed-in Today read                         | core-api#48 | ❌              |
```

Keep `#` and `Repo` compact. Give most of the available width to `Title`, then `Blocked by`, and target a total width that fits a typical terminal (about 100–120 display columns). Wrap only at word boundaries when possible. On continuation lines, leave `#` and `Repo` blank; continue `Title` and `Blocked by` independently. Pad every cell to its column's display width so borders remain aligned, including when text contains wide Unicode characters.

Do not add a prose recap that duplicates the table. After the table, mention only missing data or an ambiguity that affects its interpretation.
