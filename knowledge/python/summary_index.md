# Python 编码规范 - 总体索引与速查表

> 本文件是 Python 编码规范的总体索引。在开始编写 Python 代码前，请先阅读 `~/.claude/rules/code-python-style.md`（核心规则），然后阅读本文件了解全貌，最后按需查阅 `details/` 目录下的详细文档。

---

## 文档层级结构

```
~/.claude/rules/code-python-style.md          ← 核心规则（必须遵守）
~/.claude/knowledge/python/summary_index.md    ← 本文件（总体索引）
~/.claude/knowledge/python/details/            ← 详细参考文档
    ├── 01-naming.md                           ← 命名规范
    ├── 02-code-layout.md                      ← 代码布局
    ├── 03-imports.md                          ← 导入规范
    ├── 04-strings.md                          ← 字符串
    ├── 05-whitespace.md                       ← 空白符
    ├── 06-comments.md                         ← 注释与文档字符串
    ├── 07-type-hints.md                       ← 类型注解
    ├── 08-error-handling.md                   ← 异常处理
    ├── 09-functions.md                        ← 函数与方法
    ├── 10-classes.md                          ← 类设计
    ├── 11-pythonic.md                         ← Pythonic 惯用法
    ├── 12-testing.md                          ← 测试规范
    └── 13-security.md                         ← 安全与最佳实践
```

---

## 文件索引

详见 `~/.claude/rules/code-python-style.md` 获取核心规则，以下为详细参考文档：

| 文件 | 内容 |
|------|------|
| `details/01-naming.md` | 命名规范详解 |
| `details/02-code-layout.md` | 代码布局详解 |
| `details/03-imports.md` | 导入规范详解 |
| `details/04-strings.md` | 字符串规范详解 |
| `details/05-whitespace.md` | 空白符规范详解 |
| `details/06-comments.md` | 注释与文档字符串详解 |
| `details/07-type-hints.md` | 类型注解详解 |
| `details/08-error-handling.md` | 异常处理详解 |
| `details/09-functions.md` | 函数与方法详解 |
| `details/10-classes.md` | 类设计详解 |
| `details/11-pythonic.md` | Pythonic 惯用法详解 |
| `details/12-testing.md` | 测试规范详解 |
| `details/13-security.md` | 安全与最佳实践详解 |

---

## 关键概念速查

| 概念 | 说明 | 关联文件 |
|------|------|----------|
| CapWords | 大驼峰命名，用于类名 | 01-naming |
| lower_with_under | 小写+下划线，用于函数/变量 | 01-naming |
| UPPER_CASE | 全大写+下划线，用于常量 | 01-naming |
| f-string | 格式化字符串字面量 (Python 3.6+) | 04-strings |
| type hints | 类型注解，用于静态类型检查 | 07-type-hints |
| docstring | 文档字符串，三重双引号 | 06-comments |
| decorator | 装饰器，@ 语法 | 09-functions |
| context manager | 上下文管理器，with 语句 | 08-error-handling |
| dataclass | 数据类，@dataclass 装饰器 | 10-classes |
| ABC | 抽象基类，abc.ABC | 10-classes |
| pytest | 测试框架 | 12-testing |
| pylint | 代码检查工具 | 02-code-layout |

---

## 速查表

### 命名速查

| 元素 | 风格 | 示例 |
|------|------|------|
| 包 | `lowercase` | `mypackage` |
| 模块 | `lower_with_under` | `my_module.py` |
| 类 | `CapWords` | `MyClass` |
| 异常 | `CapWords` + `Error` | `NotFoundError` |
| 函数/方法 | `lower_with_under` | `my_function()` |
| 变量 | `lower_with_under` | `my_variable` |
| 常量 | `UPPER_CASE` | `MAX_SIZE` |
| 类型变量 | `CapWords` 或短名 | `T`, `AnyStr` |
| 私有 | `_前缀` | `_internal` |
| 模块私有 | `_前缀` | `_MODULE_VAR` |

### 行宽与缩进

| 项目 | 规则 |
|------|------|
| 缩进 | 4 空格，禁止 Tab |
| 代码行宽 | 79 字符（PEP 8）/ 80 字符（Google） |
| 文档/注释行宽 | 72 字符 |
| 续行 | 使用括号隐式续行，禁止反斜杠 |

### 空行规则

| 场景 | 空行数 |
|------|--------|
| 顶层函数/类之间 | 2 个空行 |
| 类内方法之间 | 1 个空行 |
| 函数内逻辑分段 | 少量空行 |

### 导入顺序

```
1. from __future__ import ...
2. 标准库 (import os, import sys)
3. 第三方库 (import numpy, import requests)
4. 本地库 (from myproject import mymodule)
```

每组之间空行分隔，每组内按字典序排列。

### 字符串格式化优先级

```
f-string > .format() > % 运算符 > + 拼接（禁止）
```

### 类型注解速查

```python
# 函数签名
def func(a: int, b: str = "default") -> bool: ...

# 变量注解
count: int = 0

# 可选类型
def func(x: str | None = None) -> str: ...  # Python 3.10+
def func(x: Optional[str] = None) -> str: ...  # 兼容写法

# 泛型
def first(items: list[T]) -> T: ...

# 类型别名
Vector: TypeAlias = list[float]
```

### 异常处理模式

```python
# 正确 - 具体异常 + 最小 try 块
try:
    value = collection[key]
except KeyError:
    return default
else:
    return process(value)

# 正确 - 资源管理
with open("file.txt") as f:
    data = f.read()

# 错误 - 裸 except
try:
    do_something()
except:  # 禁止！
    pass
```

---

## 参考来源

| 来源 | 说明 |
|------|------|
| [PEP 8](https://peps.pythonlang.cn/pep-0008/) | Python 官方代码风格指南 |
| [Google Python Style Guide](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/) | Google 开源项目 Python 风格指南 |
| [Aliyun Python 编码规范](https://developer.aliyun.com/article/892166) | 阿里云开发者 Python 编码规范 |
| [PEP 257](https://peps.pythonlang.cn/pep-0257/) | Docstring 约定 |
| [PEP 484](https://peps.pythonlang.cn/pep-0484/) | 类型注解 |
| [PEP 526](https://peps.pythonlang.cn/pep-0526/) | 变量注解语法 |
