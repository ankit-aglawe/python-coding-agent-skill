# Production Python Skill

A Claude Code / Codex skill that makes AI coding agents write production-grade Python — no AI slop, no over-engineering, just clean human-readable code.

## Installation

### Claude Code Plugin (recommended)

Add the marketplace and install:

```bash
/plugin marketplace add ankit-aglawe/python-coding-agent-skill
```
```bash
/plugin install prod-python@python-coding-agent-skill
```

### Clone into skills directory

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/ankit-aglawe/python-coding-agent-skill.git ~/.claude/skills/prod-python
```

### Manual install (skill file only)

```bash
mkdir -p ~/.claude/skills/prod-python
cp prod-python/skills/prod-python/SKILL.md ~/.claude/skills/prod-python/
```

### Codex

Copy `prod-python/skills/prod-python/SKILL.md` into your Codex agents skill directory (`~/.agents/skills/prod-python/`).

### Any AI Coding Agent

The skill is a single markdown file (`SKILL.md`). Add it to your agent's system prompt or instruction set.

## Usage

The skill activates automatically whenever Claude Code or Codex writes Python code. No slash command needed — it runs in the background enforcing clean patterns.

You can also reference it directly:

```
Write this using prod-python patterns: [describe your code]
```

## Overview

AI coding agents default to verbose, over-engineered Python — legacy `typing` imports, unnecessary classes, docstrings longer than the function, comments that narrate every line. This skill fixes that.

Based on PEP standards, modern Python 3.13+ features, and real-world production patterns used by experienced developers.

## 12 AI Slop Patterns Eliminated (with Before/After)

### Code Structure

| # | Pattern | Before (AI Slop) | After (Clean) |
|---|---------|-------------------|---------------|
| 1 | **Module docstrings everywhere** | `"""User auth module."""` on every file | Skip — filename is enough |
| 2 | **Over-documented obvious code** | 15-line docstring for a 3-line function | One-line docstring or skip entirely |
| 3 | **Comment narration** | `# Initialize list` / `# Loop through items` | Delete — code speaks for itself |
| 4 | **Legacy typing imports** | `from typing import List, Dict, Optional` | `list[str]`, `dict`, `X \| None` |

### Architecture

| # | Pattern | Before (AI Slop) | After (Clean) |
|---|---------|-------------------|---------------|
| 5 | **Premature abstraction** | Class with one method and no state | Plain function |
| 6 | **Over-engineered returns** | `ValidationResult(is_valid=True, error=None)` | `return error_msg or None` |
| 7 | **Wrapper functions** | Function that just calls another function | Call directly |
| 8 | **Defensive impossibility checks** | `if order is None` in internal code | Trust your own code, validate at boundaries |

### Style

| # | Pattern | Before (AI Slop) | After (Clean) |
|---|---------|-------------------|---------------|
| 9 | **Noise logging** | `logger.info` on every step | Log errors and key events only |
| 10 | **`_helper`/`_util` suffixes** | `_process_helper`, `_validate_internal` | Name by what it does |
| 11 | **`**kwargs` when params are known** | `def create(**kwargs)` | `def create(name: str, age: int)` |
| 12 | **`from __future__ import annotations`** | Unnecessary on 3.13+ | Delete the import |

## Full Example

**Before (AI slop — 30 lines):**

```python
from typing import List, Dict, Any, Optional

class UserValidator:
    """Validator for user data."""

    def __init__(self) -> None:
        self.required_fields: List[str] = ['email', 'name']

    def validate(self, user: Dict[str, Any]) -> Dict[str, Any]:
        """
        Validate user data.

        Args:
            user: User dictionary to validate

        Returns:
            Validation result with is_valid and errors
        """
        errors: List[str] = []
        for field in self.required_fields:
            if not user.get(field):
                errors.append(f"{field} is required")

        return {
            'is_valid': len(errors) == 0,
            'errors': errors
        }

validator = UserValidator()
result = validator.validate(user_data)
if not result['is_valid']:
    handle_errors(result['errors'])
```

**After (clean — 8 lines):**

```python
def validate_user(user: dict) -> list[str]:
    errors = []
    if not user.get('email'):
        errors.append("Email required")
    if not user.get('name'):
        errors.append("Name required")
    return errors

if errors := validate_user(user_data):
    handle_errors(errors)
```

82% less code. More readable. Same functionality. No imports needed.

## What's Covered

| Area | What It Enforces |
|---|---|
| **Typing** | Modern builtins (`list`, `dict`), `\|` unions, `type` aliases (3.12+), generic classes (3.12+), TypeVar defaults (3.13+) |
| **AI Slop** | No module docstrings everywhere, no comment narration, no legacy `typing` imports, no wrapper functions |
| **Architecture** | Functions over classes, no premature abstraction, validate at boundaries only |
| **Patterns** | `dataclass(slots=True)`, `pathlib`, `StrEnum`, context managers, walrus operator, pattern matching |
| **Error Handling** | Specific exceptions with context, no bare `except`, no silent swallowing |
| **Async** | `asyncio.gather`, `httpx.AsyncClient`, proper error collection |
| **Imports** | PEP 8 ordering (stdlib → third-party → local), `isort` compatible |
| **Project Layout** | `src/` layout, `pyproject.toml` for all config, no `setup.py` |
| **Logging** | Errors and key events only, no step-by-step noise |

## Quick Reference

| Do | Don't |
|---|---|
| `list[str]`, `dict[str, int]` | `List[str]`, `Dict[str, int]` |
| `str \| None` | `Optional[str]` |
| `type JSON = dict[str, ...]` | `JSON = Dict[str, Any]` |
| One-line docstrings (or skip) | Multi-paragraph for obvious code |
| Comments explain WHY | Comments narrate WHAT |
| Functions | Single-method classes |
| `pathlib.Path` | `os.path.join` |
| `@dataclass(slots=True)` | Plain dicts for structured data |
| `StrEnum` | Magic strings |
| f-strings | `.format()` or `%` |
| `pyproject.toml` | `setup.py` + `setup.cfg` |

## Contributing

Found an AI pattern this skill doesn't catch? Open an issue or PR.

## Version History

- **1.0.0** - Initial release — 12 anti-patterns, modern Python 3.13+ typing, production patterns

## License

MIT
