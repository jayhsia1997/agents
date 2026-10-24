---
name: ticket-table
description: Present tickets that were just created or supplied by the user as a compact table of each ticket's Linear identifier, project, title, and blocking dependencies. Use after a ticket-creation workflow such as to-tickets, or when the user asks for a ticket dependency table; do not create or modify tickets.
---

# Ticket Table

Summarize the completed ticket set as one dependency table. This skill is read-only: do not create, edit, label, close, or link tickets.

## Source data

Prefer the ticket identifiers returned by the immediately preceding creation workflow. If the user supplies ticket files, issue URLs, or a tracker query instead, read those sources before rendering the table. On Linear, read each issue's identifier, project, title, and `blockedBy` relations. Do not guess missing identifiers, projects, titles, or blockers; use `?` for an unavailable value and briefly note the missing source data after the table.

Include every ticket in the supplied or just-created set exactly once. Do not include a parent issue unless it was itself one of the created tickets.

## Normalize rows

Build these columns:

- `ID`: that issue's Linear identifier, `<TEAM identifier>-<number>`. Copy the team key from the issue. For local ticket files, use their dependency-order number, preserving leading zeroes.
- `Project`: the Linear project name. For local ticket files, use `—`.
- `Title`: the exact ticket title, without identifier prefixes.
- `Blocked by`: comma-separated blocker identifiers. Use each blocker's own Linear identifier for tracker issues and the local number for local tickets. Use `—` when there are no blockers.
- `Needs Migration`: `✅` if the ticket needs to be generated the alembic migration script, `❌` otherwise.

Order rows by dependency order when that order is known, with blockers before blocked tickets. Otherwise preserve the creation or input order. Never imply a dependency merely from row order.

## Render

Return a markdown table with the following columns:

```markdown
| ID     | Project  | Title                                    | Blocked by | Needs Migration |
| ------ | -------- | ---------------------------------------- | ---------- | --------------- |
| ABC-48 | core-api | Devotion schema and anonymous Today read | —          | ✅              |
| ABC-51 | core-api | Signed-in Today read                     | ABC-48     | ❌              |
```

Keep `ID` and `Project` compact. Give most of the available width to `Title`, then `Blocked by`, and target a total width that fits a typical terminal (about 100–120 display columns). Wrap only at word boundaries when possible. On continuation lines, leave `ID` and `Project` blank; continue `Title` and `Blocked by` independently. Pad every cell to its column's display width so borders remain aligned, including when text contains wide Unicode characters.

Do not add a prose recap that duplicates the table. After the table, mention only missing data or an ambiguity that affects its interpretation.
