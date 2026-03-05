# Production Python Skill

一个 Claude Code / Codex 技能，让 AI 编程代理编写生产级 Python 代码——没有 AI 废话模式，没有过度工程，只有简洁易读的代码。

[English](README.md) | 中文

## 安装

### Claude Code 插件（推荐）

添加市场并安装：

```bash
/plugin marketplace add ankit-aglawe/python-coding-agent-skill
```
```bash
/plugin install prod-python@python-coding-agent-skill
```

### 克隆到技能目录

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/ankit-aglawe/python-coding-agent-skill.git ~/.claude/skills/prod-python
```

### 手动安装（仅技能文件）

```bash
mkdir -p ~/.claude/skills/prod-python
cp prod-python/skills/prod-python/SKILL.md ~/.claude/skills/prod-python/
```

### Codex

将 `prod-python/skills/prod-python/SKILL.md` 复制到您的 Codex 代理技能目录（`~/.agents/skills/prod-python/`）。

### 任何 AI 编程代理

该技能是一个 Markdown 文件（`SKILL.md`）。将其添加到您的代理的系统提示或指令集中。

## 使用方法

当 Claude Code 或 Codex 编写 Python 代码时，该技能会自动激活。无需斜杠命令——它在后台运行，强制执行简洁的代码模式。

您也可以直接引用它：

```
使用 prod-python 模式编写：[描述您的代码]
```

## 概述

AI 编程代理默认生成冗长、过度工程化的 Python 代码——遗留的 `typing` 导入、不必要的类、比函数还长的文档字符串、逐行解释的注释。这个技能修复了这些问题。

基于 PEP 标准、现代 Python 3.13+ 特性以及资深开发者使用的真实生产模式。

## 消除的 12 种 AI 废话模式（含前后对比）

### 代码结构

| # | 模式 | 修改前（AI 废话） | 修改后（简洁） |
|---|------|-------------------|----------------|
| 1 | **到处写模块文档字符串** | 每个文件都有 `"""用户认证模块。"""` | 跳过——文件名已经足够 |
| 2 | **过度文档化简单代码** | 3 行函数写 15 行文档字符串 | 一行文档字符串或直接省略 |
| 3 | **注释叙述** | `# 初始化列表` / `# 遍历项目` | 删除——代码本身已说明一切 |
| 4 | **遗留 typing 导入** | `from typing import List, Dict, Optional` | `list[str]`、`dict`、`X \| None` |

### 架构

| # | 模式 | 修改前（AI 废话） | 修改后（简洁） |
|---|------|-------------------|----------------|
| 5 | **过早抽象** | 只有一个方法且无状态的类 | 普通函数 |
| 6 | **过度工程化的返回值** | `ValidationResult(is_valid=True, error=None)` | `return error_msg or None` |
| 7 | **包装函数** | 只是调用另一个函数的函数 | 直接调用 |
| 8 | **防御不可能的检查** | 内部代码中的 `if order is None` | 信任自己的代码，仅在边界处验证 |

### 风格

| # | 模式 | 修改前（AI 废话） | 修改后（简洁） |
|---|------|-------------------|----------------|
| 9 | **噪声日志** | 每一步都有 `logger.info` | 仅记录错误和关键事件 |
| 10 | **`_helper`/`_util` 后缀** | `_process_helper`、`_validate_internal` | 按功能命名 |
| 11 | **参数已知时使用 `**kwargs`** | `def create(**kwargs)` | `def create(name: str, age: int)` |
| 12 | **`from __future__ import annotations`** | 在 3.13+ 上不必要 | 删除该导入 |

## 完整示例

**修改前（AI 废话——30 行）：**

```python
from typing import List, Dict, Any, Optional

class UserValidator:
    """用户数据验证器。"""

    def __init__(self) -> None:
        self.required_fields: List[str] = ['email', 'name']

    def validate(self, user: Dict[str, Any]) -> Dict[str, Any]:
        """
        验证用户数据。

        Args:
            user: 要验证的用户字典

        Returns:
            包含 is_valid 和 errors 的验证结果
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

**修改后（简洁——8 行）：**

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

减少 82% 的代码。更易读。相同功能。无需导入。

## 覆盖范围

| 领域 | 执行内容 |
|------|----------|
| **类型标注** | 现代内置类型（`list`、`dict`）、`\|` 联合类型、`type` 别名（3.12+）、泛型类（3.12+）、TypeVar 默认值（3.13+） |
| **AI 废话** | 不到处写模块文档字符串、不写注释叙述、不用遗留 `typing` 导入、不用包装函数 |
| **架构** | 函数优于类、不过早抽象、仅在边界处验证 |
| **模式** | `dataclass(slots=True)`、`pathlib`、`StrEnum`、上下文管理器、海象运算符、模式匹配 |
| **错误处理** | 带上下文的特定异常、不用裸 `except`、不静默吞错误 |
| **异步** | `asyncio.gather`、`httpx.AsyncClient`、正确的错误收集 |
| **导入** | PEP 8 排序（标准库 → 第三方 → 本地）、`isort` 兼容 |
| **项目布局** | `src/` 布局、`pyproject.toml` 统一配置、不用 `setup.py` |
| **日志** | 仅记录错误和关键事件、不逐步噪声记录 |

## 快速参考

| 应该 | 不应该 |
|------|--------|
| `list[str]`、`dict[str, int]` | `List[str]`、`Dict[str, int]` |
| `str \| None` | `Optional[str]` |
| `type JSON = dict[str, ...]` | `JSON = Dict[str, Any]` |
| 一行文档字符串（或省略） | 对显而易见的代码写多段落文档 |
| 注释解释为什么 | 注释叙述做了什么 |
| 函数 | 单方法类 |
| `pathlib.Path` | `os.path.join` |
| `@dataclass(slots=True)` | 对结构化数据用普通字典 |
| `StrEnum` | 魔法字符串 |
| f-strings | `.format()` 或 `%` |
| `pyproject.toml` | `setup.py` + `setup.cfg` |

## 贡献

发现了这个技能没有捕获的 AI 模式？欢迎提交 Issue 或 PR。

## 版本历史

- **1.0.0** - 初始版本——12 种反模式、现代 Python 3.13+ 类型标注、生产模式

## 许可证

MIT
