---
name: python
description: Personal Python conventions — uv, ruff, type hints, Pydantic v2, FastAPI layering, pytest. Use when working in a Python codebase, or when the user mentions uv, ruff, FastAPI, Pydantic, or pytest.
---

# Python

Apply only while the current task is in a Python project. Repository-local tooling and layout override this skill.

## Tooling

- Manage dependencies with **uv**, not pip or poetry.
- Lint and format with **ruff**; line length **160**.

## Typing and models

- Add type hints everywhere practical.
- Use **Pydantic v2** syntax.

## FastAPI

When the project is FastAPI (or clearly following that shape):

- Route handlers do I/O conversion only.
- Business logic lives in a use-case layer.
- Inject dependencies with `Depends`; no module-level globals for collaborator wiring.

## Tests

- Use **pytest**, not `unittest.TestCase`.
