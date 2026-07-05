---
title: "Python 类型注解详解"
source: "PEP 484、PEP 526、Google Python Style Guide 类型注解"
version: "latest"
---

# Python 类型注解详解

> 来源：PEP 484、PEP 526、Google Python Style Guide 类型注解

---

## 1. 通用规则

- 遵循 PEP 484
- 仅在有额外类型信息时才注解 `self`/`cls`
- 不需要注解 `__init__` 的返回值（只能返回 None）
- 不需要限制类型时使用 `Any`
- 无需注解模块中的所有函数
- **至少注解公开 API**

```python
# self 不需要注解
def my_method(self, first_var: int) -> int:
    ...

# 有额外信息时注解 cls
@classmethod
def create(cls: Type[_T]) -> _T:
    return cls()

# __init__ 不注解返回值
def __init__(self, name: str) -> None:
    self.name = name
```

---

## 2. 函数签名

### 2.1 基本格式

```python
def func(a: int) -> List[int]:
    ...

def func(a: int, b: str = "default") -> bool:
    ...
```

### 2.2 换行规则

```python
# 每行一个参数，返回值单独一行
def my_method(
    self,
    first_var: int,
    second_var: Foo,
    third_var: Bar | None,
) -> int:
    ...

# 可以放在一行
def my_method(self, first_var: int) -> int:
    ...

# 最后一个参数+返回值可以同行
def my_method(
    self,
    first_var: int,
    second_var: int) -> dict[OtherLongType, MyLongType]:
    ...
```

### 2.3 右括号位置

```python
# 正确 - 与 def 对齐
def my_method(
    self,
    other_arg: MyLongType | None,
) -> tuple[MyLongType1, MyLongType1]:
    ...

# 错误 - 与参数对齐
def my_method(self,
              other_arg: MyLongType | None,
             ) -> dict[OtherLongType, MyLongType]:
    ...
```

---

## 3. 变量注解

### 3.1 PEP 526 语法

```python
# 模块级变量
code: int

# 类变量和实例变量
class Point:
    coords: Tuple[int, int]
    label: str = '<unknown>'

# 局部变量
def func():
    count: int = 0
```

### 3.2 空白规则

```python
# 正确
code: int  # 冒号后一个空格
class Test:
    result: int = 0  # = 两侧各一个空格

# 错误
code:int  # 冒号后无空格
code : int  # 冒号前有空格
class Test:
    result: int=0  # = 周围无空格
```

---

## 4. NoneType

```python
# 正确 - 现代并集写法 (Python 3.10+)
def modern_or_union(a: str | int | None, b: str | None = None) -> str:
    ...

# 正确 - Union/Optional 写法
def union_optional(a: Union[str, int, None], b: Optional[str] = None) -> str:
    ...

# 错误 - Union 代替 Optional
def nullable_union(a: Union[None, str]) -> str:
    ...

# 错误 - 隐式 Optional
def implicit_optional(a: str = None) -> str:
    ...
```

- 变量可能为 `None` 时必须声明
- 使用 `X | None` 替代隐式声明

---

## 5. 类型别名

```python
from typing import TypeAlias

_LossAndGradient: TypeAlias = tuple[tf.Tensor, tf.Tensor]
ComplexTFMap: TypeAlias = Mapping[str, _LossAndGradient]
```

- 别名命名使用大驼峰
- 仅模块内使用时加 `_` 前缀
- `TypeAlias` 注解仅 Python 3.10+ 可用

---

## 6. 前向声明

```python
# 方法一：使用 __future__ annotations（推荐）
from __future__ import annotations

class MyClass:
    def __init__(self, stack: Sequence[MyClass], item: OtherClass) -> None:
        ...

class OtherClass:
    ...

# 方法二：使用字符串
class MyClass:
    def __init__(self, stack: Sequence['MyClass'], item: 'OtherClass') -> None:
        ...
```

---

## 7. 忽略类型检查

```python
# 行级忽略
x = some_function()  # type: ignore

# pytype 特定错误禁用
x = some_function()  # pytype: disable=attribute-error

# 文件级忽略（放在文件顶部）
# type: ignore
```

---

## 8. 类型变量

```python
from collections.abc import Callable
from typing import ParamSpec, TypeVar

_P = ParamSpec("_P")
_T = TypeVar("_T")

# 约束条件
AddableType = TypeVar("AddableType", int, float, str)

# 边界约束
AnyFunction = TypeVar("AnyFunction", bound=Callable)

# 预定义
from typing import AnyStr
def check_length(x: AnyStr) -> AnyStr:
    if len(x) <= 42:
        return x
    raise ValueError()
```

### 命名规则

```python
# 正确 - 有约束条件时用描述性名称
_T = TypeVar("_T")  # 无约束，外部不可见
AddableType = TypeVar("AddableType", int, float, str)

# 错误
T = TypeVar("T")  # 应该用 _T
_T = TypeVar("_T", int, float, str)  # 有约束时应用描述性名称
```

---

## 9. 字符串类型

```python
# 正确 - 用 str 处理文本
def deals_with_text_data(x: str) -> str:
    ...

# 正确 - 用 bytes 处理二进制
def deals_with_binary_data(x: bytes) -> bytes:
    ...

# 错误 - 不要使用 typing.Text（仅用于 Python 2/3 兼容）
from typing import Text
def func(x: Text) -> Text:
    ...
```

---

## 10. 导入类型

```python
# 正确 - 直接导入符号
from collections.abc import Mapping, Sequence
from typing import Any, Generic

# 冲突时使用别名
from typing import Any as AnyType

# 尽量使用内置类型 (Python 3.9+)
def generate_foo_scores(foo: set[str]) -> list[float]:
    ...

# 兼容旧版本
from typing import Set, List
def generate_foo_scores(foo: Set[str]) -> List[float]:
    ...
```

---

## 11. 有条件导入

```python
import typing
if typing.TYPE_CHECKING:
    import sketch
def f(x: "sketch.Sketch"): ...
```

- 仅在运行时必须避免导入时使用
- 类型注解中用字符串表示
- 紧随常规导入之后，无空行

---

## 12. 泛型

```python
# 正确 - 填入类型参数
def get_names(employee_ids: Sequence[int]) -> Mapping[int, str]:
    ...

# 错误 - 缺少类型参数（默认为 Any）
def get_names(employee_ids: Sequence) -> Mapping:
    ...

# 正确 - 使用 TypeVar
_T = TypeVar('_T')
def get_names(employee_ids: Sequence[_T]) -> Mapping[_T, str]:
    ...
```

---

## 13. 元组 vs 列表

```python
# 列表：只能有一种类型
a: list[int] = [1, 2, 3]

# 元组：可以有多种类型
b: tuple[int, ...] = (1, 2, 3)  # 同类型可变长
c: tuple[int, str, float] = (1, "2", 3.5)  # 不同类型固定长
```

---

## 14. 循环依赖

```python
# 使用 Any 替换引起循环依赖的模块
from typing import Any

some_mod = Any  # 因为 some_mod.py 导入了我们的模块

def my_method(self, var: "some_mod.SomeType") -> None:
    ...
```

- 循环依赖说明代码可能需要重构
- 起有意义的别名
