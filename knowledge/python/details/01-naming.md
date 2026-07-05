---
title: "Python 命名规范详解"
source: "PEP 8 命名约定、Google Python Style Guide 命名规范"
version: "latest"
---

# Python 命名规范详解

> 来源：PEP 8 命名约定、Google Python Style Guide 命名规范

---

## 1. 首要原则

作为 API 公共部分对用户可见的名称应遵循**反映用法而非实现**的约定。

---

## 2. 命名风格一览

| 风格 | 示例 | 用途 |
|------|------|------|
| `lowercase` | `x` | 单个小写字母 |
| `UPPERCASE` | `X` | 单个大写字母 |
| `lowercase` | `name` | 小写单词 |
| `lower_case_with_underscores` | `my_name` | 带下划线的小写（最常用） |
| `UPPERCASE` | `NAME` | 大写 |
| `UPPER_CASE_WITH_UNDERSCORES` | `MY_NAME` | 带下划线的大写（常量） |
| `CapitalizedWords` | `MyName` | 大驼峰（CapWords/CamelCase） |
| `mixedCase` | `myName` | 首字母小写的大驼峰（避免使用） |

**注意**：在 CapWords 中使用首字母缩写时，全大写：`HTTPServerError` ✅ / `HttpServerError` ❌

---

## 3. 前导/尾随下划线约定

| 形式 | 含义 | 示例 |
|------|------|------|
| `_single_leading_underscore` | 弱"内部使用"指示符，`from M import *` 不导入 | `_internal_var` |
| `single_trailing_underscore_` | 避免与 Python 关键字冲突 | `class_`, `type_` |
| `__double_leading_underscore` | 类属性名称重整（name mangling） | `__mangled` |
| `__double_leading_and_trailing_underscore__` | Python 魔术方法，**禁止自行创建** | `__init__`, `__len__` |

---

## 4. 具体命名规则

### 4.1 包和模块

```python
# 正确
import mymodule
from mypackage import mymodule

# 错误（避免下划线）
import my_module  # 包名不推荐下划线（模块名可以）
```

- 包名：简短、全小写，**不鼓励**使用下划线
- 模块名：简短、全小写，**可以**使用下划线提高可读性
- C/C++ 扩展模块：前导下划线 `_socket`

### 4.2 类名

```python
# 正确
class MyClass:
    pass

class HTTPServer:
    pass

# 错误
class myClass:  # 不符合 CapWords
    pass
```

- 使用 CapWords 约定
- 接口主要用作可调用对象时，可使用函数命名约定
- 内置名称例外：大多数是单字，仅异常和内置常量使用 CapWords

### 4.3 类型变量

```python
from typing import TypeVar

# 正确
T = TypeVar('T')
AnyStr = TypeVar('AnyStr')
VT_co = TypeVar('VT_co', covariant=True)

# 错误
t = TypeVar('t')  # 应该用大写
```

- 使用 CapWords 约定，首选短名称
- 协变变量添加 `_co` 后缀，逆变变量添加 `_contra` 后缀

### 4.4 异常名

```python
# 正确
class NotFoundError(Exception):
    pass

class ValidationError(Exception):
    pass

# 错误
class NotFound(Exception):  # 缺少 Error 后缀
    pass
```

- 因为异常是类，使用类命名约定
- 异常名后使用 `Error` 后缀（如果确实是错误）

### 4.5 全局变量

```python
# 正确 - 模块级常量
MAX_OVERFLOW = 100
DEFAULT_TIMEOUT = 30

# 正确 - 内部全局变量
_module_cache = {}
_connection_pool = None

# 错误
maxOverflow = 100  # 应该用全大写+下划线
```

- 仅在一个模块内使用的变量，遵循函数命名约定
- 通过 `from M import *` 使用的模块应使用 `__all__` 或 `_` 前缀
- 常量：全大写，下划线分隔

### 4.6 函数和变量

```python
# 正确
def my_function():
    pass

user_count = 0

# 错误
def MyFunction():  # 函数不该用大驼峰
    pass

def myFunction():  # 不该用 mixedCase（除非向后兼容）
    pass
```

- 小写，下划线分隔
- `mixedCase` 仅在已有风格中允许（如 `threading.py`）

### 4.7 函数和方法参数

```python
# 正确
class MyClass:
    def method(self, arg, keyword_arg=None):
        pass

    @classmethod
    def create(cls, **kwargs):
        pass

# 正确 - 参数名与关键字冲突
def connect(class_=None):
    pass
```

- 实例方法第一个参数 `self`
- 类方法第一个参数 `cls`
- 参数名与关键字冲突时，附加尾随下划线 `class_` 优于 `clss`

### 4.8 方法名和实例变量

```python
class MyClass:
    def public_method(self):
        pass

    def _internal_method(self):
        pass

    def __mangled_method(self):  # 仅在需要防子类冲突时
        pass
```

- 遵循函数命名规则
- 非公共方法/变量：单前导下划线
- 避免子类名称冲突：双前导下划线（名称重整）

### 4.9 常量

```python
# 正确
MAX_OVERFLOW = 100
TOTAL = 1024

# 错误
max_overflow = 100  # 应该全大写
```

- 模块级别定义
- 全大写字母，下划线分隔

---

## 5. 为继承而设计

### 5.1 公共属性
- 不应有前导下划线
- 名称与关键字冲突时，附加尾随下划线
- 简单数据属性直接暴露，不需要 getter/setter
- 需要计算逻辑时使用 `@property`

### 5.2 非公共属性
- 单前导下划线 `_internal`
- 不保证向后兼容

### 5.3 子类 API
- 设计为被继承的类，明确决定哪些是公共、哪些是子类 API
- 防止子类冲突使用 `__double_leading`（名称重整）

```python
class Base:
    def __init__(self):
        self.public_attr = 1      # 公共
        self._protected = 2       # 受保护（约定）
        self.__private = 3        # 私有（名称重整）
```

**注意**：名称重整不是真正的私有，可通过 `_Base__private` 访问。权衡避免冲突与可调试性。

---

## 6. 公共和内部接口

- 已文档化的接口 = 公共接口（除非明确声明为内部）
- 未文档化的接口 = 内部接口
- 模块应使用 `__all__` 声明公共 API
- 内部接口以单前导下划线前缀
- 导入的名称视为实现细节

```python
# 正确
__all__ = ['public_func', 'PublicClass']

def public_func():
    """公共接口."""
    pass

def _internal_func():
    """内部接口."""
    pass
```

---

## 7. Google 风格命名补充

### 7.1 避免的名称

- 单字符名称（除计数器 `i, j, k`、异常 `e`、文件句柄 `f`、类型变量 `_T`）
- 包含连字符的包名/模块名
- 首尾双下划线名称（Python 保留）
- 包含冒犯性词语的名称
- 不必要地包含变量类型的名称（如 `id_to_name_dict`）

### 7.2 文件名

- 必须以 `.py` 结尾
- 不能包含连字符 `-`
- 小写字母加下划线

### 7.3 测试方法名

```python
# 正确
class MyTest(unittest.TestCase):
    def test_public_method_returns_expected(self):
        pass

    def test_public_method_raises_on_invalid_input(self):
        pass
```

- 格式：`test_<被测方法名>_<状态>`
- 遵循 PEP 8 小写加下划线

### 7.4 数学符号例外

对于涉及大量数学内容的代码，如果相关论文或算法有对应符号，可以使用较短变量名。但必须在注释或文档字符串中注明命名来源。
