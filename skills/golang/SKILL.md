---
name: golang
description: Personal Go conventions — wrapped errors, no panic for control flow, interfaces at the consumer. Use when working in a Go codebase, or when the user mentions Go packages, interfaces, or error wrapping.
---

# Go

Apply only while the current task is in a Go project. Repository-local conventions override this skill.

## Errors

- Wrap errors: `fmt.Errorf("...: %w", err)`.
- Do not use `panic` for control flow.

## Interfaces

- Define interfaces at the **consumer**, not at the implementer.
