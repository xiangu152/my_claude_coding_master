---
title: "Python 类设计详解"
source: "PEP 8 命名约定/编程建议、Google Python Style Guide"
version: "latest"
---

# Python 类设计详解

> 来源：PEP 8 命名约定/编程建议、Google Python Style Guide

---

## 1. 类基本设计

### 1.1 类文档字符串

```python
class CheeseShopAddress:
    """The address of a cheese shop.

    属性:
        street: Street name.
        city: City name.
    """

    def __init__(self, street: str, city: str) -> None:
        """Initialize CheeseShopAddress."""
        self.street = street
        self.city = city
```

- 类定义下方必须有文档字符串
- 概述描述类的实例所代表的事物
- 异常子类描述异常代表什么，而非抛出环境
- 不要有无意义的重复

### 1.2 类文档字符串格式

```python
# 正确
class CheeseShopAddress:
    """The address of a cheese shop."""
    ...

class OutOfCheeseError(Exception):
    """No more cheese available."""
    ...

# 错误
class CheeseShopAddress:
    """A class representing a cheese shop address."""
    ...

class OutOfCheeseError(Exception):
    """Raised when no more cheese is available."""
    ...
```

---

## 2. 属性设计

### 2.1 公有属性

```python
class MyClass:
    """A simple class."""

    def __init__(self, name: str) -> None:
        self.name = name  # 公有属性直接暴露
        self._internal = {}  # 内部属性加前缀
```

- 简单数据属性直接暴露
- 不需要 getter/setter
- Python 提供 `@property` 作为未来增强路径

### 2.2 @property 使用

```python
class Circle:
    """A circle."""

    def __init__(self, radius: float) -> None:
        self._radius = radius

    @property
    def radius(self) -> float:
        """The radius of the circle."""
        return self._radius

    @radius.setter
    def radius(self, value: float) -> None:
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value

    @property
    def area(self) -> float:
        """The area of the circle (computed property)."""
        return 3.14159 * self._radius ** 2
```

- 用于需要计算逻辑的属性
- 属性应满足：轻量、直白、明确
- 避免用于计算开销大的操作
- 尽量保持无副作用（缓存除外）

### 2.3 属性 vs 方法

```python
# 正确 - 简单数据用属性
class Point:
    x: float
    y: float

# 正确 - 有副作用或开销大用方法
class Database:
    def fetch_users(self) -> list[User]:
        """Fetch all users from database."""
        ...

# 错误 - 简单属性不需要 property
class Config:
    @property
    def name(self):
        return self._name  # 没有计算逻辑，直接暴露即可
```

---

## 3. 访问器和设置器（Google 风格）

```python
# 允许使用 - 有有意义的作用时
class MyClass:
    def get_data(self):
        """Get data with caching."""
        if self._cache_expired:
            self._refresh_cache()
        return self._data

    def set_data(self, value):
        """Set data with validation."""
        self._validate(value)
        self._data = value
        self._invalidate_cache()
```

- 如果读写过程复杂或成本高昂，使用访问器/设置器
- 如果仅用于读写内部属性，直接用公有属性
- 使用 `@property` 替代简单的访问器/设置器
- 命名：`get_foo()`, `set_foo()`

---

## 4. 继承设计

### 4.1 公共/非公共/子类 API

```python
class Base:
    """Base class with clear API boundaries."""

    def public_method(self):
        """Public API - guaranteed backward compatible."""
        pass

    def _protected_method(self):
        """Protected - for subclass use, may change."""
        pass

    def __private_method(self):
        """Private - name mangled, only for this class."""
        pass
```

### 4.2 名称重整防冲突

```python
class Parent:
    def __init__(self):
        self.__internal = 'parent'  # 重整为 _Parent__internal

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.__internal = 'child'  # 重整为 _Child__internal，不冲突
```

- 双前导下划线触发名称重整
- 仅在设计用于子类化的类中使用
- 注意：重整名称中只使用简单类名

### 4.3 方法覆写

```python
class Child(Parent):
    def method(self):
        """参见基类."""
        # 简单覆写，引导读者看基类文档
        ...

    def method(self):
        """Extended method with additional behavior.

        This method extends Parent.method by adding...
        """
        # 有额外细节时，描述区别
        ...
```

---

## 5. 类方法和静态方法

### 5.1 classmethod

```python
class Date:
    """A date class."""

    def __init__(self, year: int, month: int, day: int) -> None:
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string: str) -> 'Date':
        """Create a Date from a string (named constructor)."""
        year, month, day = map(int, date_string.split('-'))
        return cls(year, month, day)
```

- 用于具名构造函数
- 用于修改必要的全局状态
- `cls` 参数代表类本身

### 5.2 staticmethod

```python
# 避免使用 - 除非为了兼容老代码 API
class MyClass:
    @staticmethod
    def utility_function(x, y):
        """A utility function (legacy API compatibility)."""
        return x + y

# 推荐 - 模块级函数
def utility_function(x, y):
    """A utility function."""
    return x + y
```

- **避免使用 `staticmethod`**
- 除非为了兼容老代码库的 API
- 应该改写为模块级函数

---

## 6. 特殊方法

### 6.1 常用魔术方法

```python
class MyClass:
    """A class demonstrating common magic methods."""

    def __init__(self, name: str) -> None:
        """Initialize."""
        self.name = name

    def __repr__(self) -> str:
        """Developer-friendly string representation."""
        return f"MyClass(name={self.name!r})"

    def __str__(self) -> str:
        """User-friendly string representation."""
        return self.name

    def __eq__(self, other: object) -> bool:
        """Check equality."""
        if not isinstance(other, MyClass):
            return NotImplemented
        return self.name == other.name

    def __hash__(self) -> int:
        """Hash for use in sets and dicts."""
        return hash(self.name)

    def __len__(self) -> int:
        """Return length."""
        return len(self.name)

    def __bool__(self) -> bool:
        """Boolean value."""
        return bool(self.name)
```

### 6.2 比较操作

```python
# 实现所有六个比较方法（如果需要排序）
from functools import total_ordering

@total_ordering
class Student:
    """A student with ordering support."""

    def __init__(self, grade: float) -> None:
        self.grade = grade

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Student):
            return NotImplemented
        return self.grade == other.grade

    def __lt__(self, other: 'Student') -> bool:
        if not isinstance(other, Student):
            return NotImplemented
        return self.grade < other.grade
```

- 使用 `functools.total_ordering()` 减少所需工作
- `sort()` 和 `min()` 使用 `<` 运算符
- `max()` 使用 `>` 运算符

---

## 7. 上下文管理器

```python
class ManagedResource:
    """A resource that needs cleanup."""

    def __init__(self, name: str) -> None:
        self.name = name

    def __enter__(self) -> 'ManagedResource':
        """Enter the context."""
        print(f"Acquiring {self.name}")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb) -> None:
        """Exit the context."""
        print(f"Releasing {self.name}")

# 使用
with ManagedResource("database") as resource:
    use_resource(resource)
```

---

## 8. 数据类

```python
from dataclasses import dataclass

@dataclass
class Point:
    """A point in 2D space."""
    x: float
    y: float

    def distance_to(self, other: 'Point') -> float:
        """Calculate distance to another point."""
        return ((self.x - other.x) ** 2 +
                (self.y - other.y) ** 2) ** 0.5

# 自动生成 __init__, __repr__, __eq__ 等
p = Point(1.0, 2.0)
```

- 使用 `@dataclass` 简化数据类定义
- 自动生成 `__init__`, `__repr__`, `__eq__` 等方法
- 可自定义行为

---

## 9. 抽象基类

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    """Abstract base class for shapes."""

    @abstractmethod
    def area(self) -> float:
        """Calculate the area."""
        ...

    @abstractmethod
    def perimeter(self) -> float:
        """Calculate the perimeter."""
        ...

class Circle(Shape):
    """A circle implementation."""

    def __init__(self, radius: float) -> None:
        self.radius = radius

    def area(self) -> float:
        return 3.14159 * self.radius ** 2

    def perimeter(self) -> float:
        return 2 * 3.14159 * self.radius
```

- 使用 `abc.ABC` 和 `@abstractmethod`
- 定义接口规范
- 强制子类实现抽象方法
