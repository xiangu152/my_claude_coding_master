---
title: "Pythonic 惯用法详解"
source: "PEP 8 编程建议、Google Python Style Guide"
version: "latest"
---

# Pythonic 惯用法详解

> 来源：PEP 8 编程建议、Google Python Style Guide

---

## 1. 隐式假值

### 1.1 基本规则

```python
# 正确 - 使用隐式假值
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

### 1.2 None 检查

```python
# 正确
if x is None:
    pass
if x is not None:
    pass

# 错误
if x == None:
    pass
if x is None == False:
    pass
```

### 1.3 布尔值

```python
# 正确
if greeting:
    pass
if not greeting:
    pass

# 错误
if greeting == True:
    pass
if greeting is True:
    pass
```

### 1.4 整数注意事项

```python
# 注意 - 0 和 None 都是假值
if value:  # 可能错误地将 None 当做 0
    pass

# 正确 - 区分 0 和 None
if value is not None:
    pass
if value == 0:
    pass
```

---

## 2. 字符串检查

### 2.1 startswith/endswith

```python
# 正确
if foo.startswith('bar'):
    pass
if foo.endswith('.txt'):
    pass

# 错误
if foo[:3] == 'bar':
    pass
if foo[-4:] == '.txt':
    pass
```

### 2.2 字符串格式化

```python
# 正确 - f-string
name = 'Alice'
greeting = f'Hello, {name}!'

# 正确 - join 拼接
parts = ['Hello', 'World']
sentence = ' '.join(parts)

# 错误 - 循环中 + 拼接
result = ''
for item in items:
    result += item  # 可能产生平方时间复杂度
```

---

## 3. 容器操作

### 3.1 默认迭代器

```python
# 正确
for key in adict:
    pass
for item in alist:
    pass
for line in afile:
    pass
for k, v in adict.items():
    pass

# 错误
for key in adict.keys():
    pass
for line in afile.readlines():
    pass
```

### 3.2 成员检查

```python
# 正确
if item in alist:
    pass
if key in adict:
    pass

# 错误
if alist.count(item) > 0:
    pass
if key in adict.keys():
    pass
```

### 3.3 字典操作

```python
# 正确 - 使用 get 提供默认值
value = my_dict.get(key, default_value)

# 正确 - 使用 setdefault
my_dict.setdefault(key, []).append(item)

# 正确 - 使用 defaultdict
from collections import defaultdict
dd = defaultdict(list)
dd[key].append(item)

# 错误 - 手动检查
if key in my_dict:
    value = my_dict[key]
else:
    value = default_value
```

---

## 4. 推导式

### 4.1 列表推导式

```python
# 正确 - 简单情况
squares = [x**2 for x in range(10)]
evens = [x for x in numbers if x % 2 == 0]

# 正确 - 每部分可分行
result = [{'key': value} for value in iterable
          if a_long_filter_expression(value)]

# 错误 - 过于复杂
result = [complicated_transform(x, some_argument=x+1)
          for x in iterable if predicate(x)]
```

### 4.2 字典推导式

```python
# 正确
squares_dict = {x: x**2 for x in range(10)}
filtered = {k: v for k, v in data.items() if v > 0}
```

### 4.3 集合推导式

```python
# 正确
unique_lengths = {len(word) for word in words}
```

### 4.4 生成器表达式

```python
# 正确
squares_gen = (x**2 for x in range(10))
total = sum(x**2 for x in range(10))

# 正确 - 作为函数参数
eat(jelly_bean for jelly_bean in jelly_beans
    if jelly_bean.color == 'black')
```

### 4.5 复杂情况使用循环

```python
# 正确 - 复杂逻辑使用循环
result = []
for x in range(10):
    for y in range(5):
        if x * y > 10:
            result.append((x, y))
```

---

## 5. 条件表达式

```python
# 正确 - 简单情况
one_line = 'yes' if predicate(value) else 'no'

# 正确 - 稍微分行
slightly_split = ('yes' if predicate(value)
                  else 'no, nein, nyet')

# 错误 - 过长或换行位置错误
bad_line_breaking = ('yes' if predicate(value) else
                     'no')
```

- 适用于简单情况
- 每部分不超过一行
- 复杂情况使用完整 `if` 语句

---

## 6. with 语句

### 6.1 文件操作

```python
# 正确
with open('file.txt') as f:
    data = f.read()

# 正确 - 多个资源
with open('input.txt') as fin, open('output.txt', 'w') as fout:
    fout.write(fin.read())

# 错误 - 不使用 with
f = open('file.txt')
data = f.read()
f.close()
```

### 6.2 自定义上下文管理器

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(name):
    """A context manager for resource management."""
    print(f'Acquiring {name}')
    try:
        yield name
    finally:
        print(f'Releasing {name}')

with managed_resource('database') as db:
    use(db)
```

---

## 7. 枚举

```python
# 正确
for i, item in enumerate(items):
    print(f'{i}: {item}')

# 正确 - 自定义起始索引
for i, item in enumerate(items, start=1):
    print(f'{i}: {item}')

# 错误
i = 0
for item in items:
    print(f'{i}: {item}')
    i += 1
```

---

## 8. zip 和 itertools

```python
# 正确 - 并行迭代
for name, age in zip(names, ages):
    print(f'{name} is {age} years old')

# 正确 - 长度不同时使用 zip_longest
from itertools import zip_longest
for name, age in zip_longest(names, ages, fillvalue='unknown'):
    print(f'{name} is {age}')
```

---

## 9. any/all

```python
# 正确
if any(condition(x) for x in items):
    pass

if all(condition(x) for x in items):
    pass

# 错误
found = False
for x in items:
    if condition(x):
        found = True
        break
```

---

## 10. 字典合并

```python
# 正确 - Python 3.9+
merged = dict1 | dict2

# 正确 - 旧版本
merged = {**dict1, **dict2}

# 正确 - update 原地修改
dict1.update(dict2)
```

---

## 11. 类型检查

```python
# 正确
if isinstance(obj, int):
    pass
if isinstance(obj, (int, float)):
    pass

# 错误
if type(obj) is int:
    pass
```

---

## 12. 解包

```python
# 正确 - 多变量赋值
a, b, c = 1, 2, 3

# 正确 - 交换变量
a, b = b, a

# 正确 - 星号解包
first, *rest = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4, 5]

# 正确 - 忽略某些值
_, important_value, _ = get_triple()
```

---

## 13. 海象运算符（Python 3.8+）

```python
# 正确 - 赋值表达式
if (n := len(data)) > 10:
    print(f'Too long: {n} elements')

# 正确 - 循环中使用
while (line := f.readline()):
    process(line)

# 正确 - 推导式中使用
results = [y for x in data if (y := transform(x)) > 0]
```

---

## 14. 常见反模式

### 14.1 不要重复发明轮子

```python
# 错误 - 手动实现
def my_flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, list):
            result.extend(my_flatten(item))
        else:
            result.append(item)
    return result

# 正确 - 使用标准库
from itertools import chain
list(chain.from_iterable(nested_list))
```

### 14.2 不要过度使用魔法

```python
# 错误 - 过度使用 getattr/exec/eval
result = eval(user_input)  # 安全风险！

# 正确 - 使用明确的方法
result = int(user_input)
```

### 14.3 不要过度封装

```python
# 错误 - 过度封装
class UserManager:
    def get_user_name(self, user):
        return user.name

# 正确 - 直接访问
user.name
```
