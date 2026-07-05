---
title: "Python 函数与方法详解"
source: "PEP 8 编程建议、Google Python Style Guide"
version: "latest"
---

# Python 函数与方法详解

> 来源：PEP 8 编程建议、Google Python Style Guide

---

## 1. 函数设计原则

### 1.1 函数长度

- 函数应小巧专一
- 超过 40 行考虑拆分
- 不硬性限制长度，但保持简练

```python
# 好 - 短小精悍
def calculate_tax(amount, rate):
    """Calculate tax amount."""
    return amount * rate

# 需要拆分 - 过长
def process_order(order):
    # ... 100 行代码 ...
```

### 1.2 单一职责

```python
# 正确 - 每个函数做一件事
def validate_email(email):
    """Validate email format."""
    ...

def send_email(to, subject, body):
    """Send an email."""
    ...

# 错误 - 函数做太多事
def validate_and_send_email(email, subject, body):
    """Validate and send email."""
    ...
```

---

## 2. 参数设计

### 2.1 默认参数

```python
# 正确 - 不可变默认值
def func(a, b=None):
    if b is None:
        b = []

# 正确 - 空元组（不可变）
def func(a, b=()):
    ...

# 错误 - 可变默认值
def func(a, b=[]):  # 列表是可变的！
    ...

# 错误 - 表达式默认值
def func(a, b=time.time()):  # 模块导入时求值
    ...
```

**关键规则：函数和方法的默认值不能是可变对象**

### 2.2 关键字参数

```python
# 正确
def complex(real, imag=0.0):
    return magic(r=real, i=imag)

# 调用
complex(3.0, imag=5.0)
complex(3.0, 5.0)
```

### 2.3 参数命名冲突

```python
# 正确 - 尾随下划线
def connect(class_=None, type_='default'):
    pass

# 错误 - 缩写或拼写错误
def connect(cls=None, typ='default'):
    pass
```

---

## 3. 返回值

### 3.1 一致性

```python
# 正确 - 所有 return 都返回表达式
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    else:
        return None

# 正确 - 明确 return None
def bar(x):
    if x < 0:
        return None
    return math.sqrt(x)

# 错误 - 混合风格
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    # 隐式返回 None
```

### 3.2 return 语句

```python
# 正确
return result
return a, b  # 返回元组
return (a, b)  # 也可以

# 错误
return (result)  # 不是元组，只是括号
```

---

## 4. Lambda 函数

```python
# 正确 - 用于简单情况
square = lambda x: x ** 2

# 正确 - 用 operator 替代常见操作
import operator
multiply = operator.mul  # 代替 lambda x, y: x * y

# 错误 - 复杂 lambda
process = lambda x: complicated_transform(x, some_argument=x+1)
```

- 适用于单行函数
- 超过 60-80 字符时使用常规函数
- 用 `operator` 模块替代常见操作

---

## 5. def vs lambda

```python
# 正确
def f(x): return 2*x

# 错误
f = lambda x: 2*x
```

- 使用 `def` 语句，而非将 `lambda` 绑定到标识符
- `def` 生成的函数对象名称特定，对回溯更有用

---

## 6. is not vs not ... is

```python
# 正确
if foo is not None:
    pass

# 错误 - 可读性差
if not foo is None:
    pass
```

---

## 7. 比较操作

### 7.1 None 比较

```python
# 正确
if x is None:
    pass
if x is not None:
    pass

# 错误
if x == None:
    pass
```

### 7.2 布尔比较

```python
# 正确
if greeting:
    pass

# 错误
if greeting == True:
    pass

# 更糟
if greeting is True:
    pass
```

### 7.3 空序列

```python
# 正确
if not seq:
    pass
if seq:
    pass

# 错误
if len(seq) == 0:
    pass
if not len(seq):
    pass
```

---

## 8. 类型比较

```python
# 正确
if isinstance(obj, int):
    pass

# 错误
if type(obj) is type(1):
    pass
```

---

## 9. 字符串方法

```python
# 正确
if foo.startswith('bar'):
    pass

# 错误
if foo[:3] == 'bar':
    pass
```

---

## 10. 装饰器（Google 风格）

```python
# 正确 - 审慎使用
@my_decorator
def my_function():
    ...

# 避免 - 除非为了兼容老代码
@staticmethod
def my_function():
    ...
```

- 仅在有显著优势时使用装饰器
- 装饰器的导入和命名规则与函数相同
- 编写单元测试
- 避免装饰器对外部环境的依赖

---

## 11. 生成器

```python
# 生成器文档使用 Yields 而非 Returns
def my_generator():
    """Generate items.

    Yields:
        The next item in the sequence.
    """
    for item in items:
        yield item
```

- 按需使用生成器
- 占用大量资源时强制清理资源
- 使用上下文管理器包裹生成器

---

## 12. 条件表达式（Google 风格）

```python
# 正确 - 简单情况
one_line = 'yes' if predicate(value) else 'no'

# 正确 - 稍微分行
slightly_split = ('yes' if predicate(value)
                  else 'no, nein, nyet')

# 错误 - 过长
portion_too_long = ('yes'
                    if some_long_module.some_long_predicate_function(
                        really_long_variable_name)
                    else 'no, false, negative, nay')
```

- 适用于简单情况
- 每部分不超过一行
- 复杂情况使用完整 `if` 语句

---

## 13. 推导式（Google 风格）

```python
# 正确 - 简单推导式
result = [mapping_expr for value in iterable if filter_expr]

# 正确 - 每部分可分行
result = [{'key': value} for value in iterable
          if a_long_filter_expression(value)]

# 正确 - 复杂情况使用循环
result = []
for x in range(10):
    for y in range(5):
        if x * y > 10:
            result.append((x, y))

# 错误 - 多重 for 和过滤
result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]
```

- 适用于简单情况
- 每个部分不应超过一行
- 禁止多重 for 和多层过滤
- 复杂情况使用循环
