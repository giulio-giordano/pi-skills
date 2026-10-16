# Python reference (dev-workflow)

Read this before writing or modifying Python code.

## Toolchain

Look at: `pyproject.toml` (`[tool.ruff]`, `[tool.mypy]`, `[tool.pytest]`),
`ruff.toml`, `setup.cfg`, `requirements*.txt`. Use the project's commands; if
ruff/mypy are declared but not installed, say so instead of skipping silently.

- Typical check: `uv run ruff check . --fix && uv run ruff format .`
- This repo's config (`pyproject.toml`): ruff rules `E,W,F,I,UP,B,SIM,ANN`,
  `line-length = 100`, `target-version = "py314"`, `known-first-party` per app.

## Style and types

- PEP 8, 4-space indent, `snake_case` functions/variables, `PascalCase` classes,
  module-level constants in `UPPER_CASE`, line length from the project config.
- **Complete type annotations** on every parameter, return value and attribute.
  No bare `Any` when a real type exists, no implicit `Any`; use `X | None` and
  built-in generics (`list[str]`, `dict[str, int]`), never `typing.List`.
- Prefer `dataclass`/`TypedDict`/`Enum`/`Protocol` over loose dicts and strings.
- Narrow exceptions: never `except:` bare, never `except Exception: pass`
  silently; catch what you can actually handle and let the rest propagate.
- Docstrings: PEP 257, imperative summary line, then `Args:`, `Returns:`,
  `Raises:` when meaningful.

## Layout

Follow the project's layout instead of imposing a new one. This repo: Django
per-app modules (`models.py`, `views.py`, `services.py`, `forms.py`) with the
business logic in `services.py`. Split a file past ~300-400 lines.

## Test

- Project's own runner first: `uv run python manage.py test` (Django) or
  `uv run pytest`.
- Add a test for every new behaviour: success path, edge cases, and the bug you
  just fixed. Keep the test in the project's existing test package.
