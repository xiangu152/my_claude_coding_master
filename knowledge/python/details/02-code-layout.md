---
title: "Python 代码布局详解"
source: "PEP 8 代码布局、Google Python Style Guide 缩进/行宽"
version: "latest"
---

# Python 代码布局详解

> 来源：PEP 8 代码布局、Google Python Style Guide 缩进/行宽

---

## 1. 缩进

### 1.1 基本规则

- **每个缩进级别使用 4 个空格**
- **禁止使用 Tab**
- **禁止混用 Tab 和空格**

### 1.2 续行缩进

使用 Python 括号内的隐式行连接，垂直对齐或使用悬挂缩进：

```python
# 正确 - 与左括号对齐
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# 正确 - 悬挂缩进 4 空格，首行无参数
foo = long_function_name(
    var_one, var_two, var_three,
    var_four)

# 正确 - 悬挂缩进，右括号另起一行
foo = long_function_name(
    var_one, var_two, var_three,
    var_four
)

# 错误 - 首行有参数但未对齐
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# 错误 - 悬挂缩进不是 4 空格
foo = long_function_name(
  var_one, var_two,
  var_three, var_four)
```

### 1.3 多行 if 语句

```python
# 无额外缩进
if (this_is_one_thing and
    that_is_another_thing):
    do_something()

# 添加注释提供区分
if (this_is_one_thing and
    that_is_another_thing):
    # Since both conditions are true, we can frobnicate.
    do_something()

# 额外缩进区分条件和代码块
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

### 1.4 闭合括号位置

```python
# 与最后一行第一个非空白字符对齐
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]

# 与开始行的第一个字符对齐（推荐）
my_list = [
    1, 2, 3,
    4, 5, 6,
]
```

---

## 2. 最大行长

### 2.1 标准

| 类型 | PEP 8 | Google |
|------|-------|--------|
| 代码行 | 79 字符 | 80 字符 |
| 文档字符串/注释 | 72 字符 | 80 字符 |
| 团队可选 | 99 字符 | - |

### 2.2 例外情况

- 长导入语句
- 注释中的 URL、路径名、长标志
- 不含空格的模块级长字符串常量（URL、路径名）
- Pylint 禁用注释

### 2.3 续行方式

```python
# 正确 - 使用括号隐式续行
x = ('这是一个很长很长很长很长很长很长'
     '很长很长很长很长很长的字符串')

# 正确 - 最外层语法结构分行
answer = (a_long_line().of_chained_methods()
          .that_eventually_provides().an_answer())

# 正确 - 多行 with 语句（Python 3.10+）
with (
    very_long_first_expression_function() as spam,
    very_long_second_expression_function() as beans,
):
    place_order(eggs, beans, spam)

# 错误 - 使用反斜杠
if width == 0 and height == 0 and \
    color == 'red' and emphasis == 'bold':
    pass
```

### 2.4 长 URL 注释

```python
# 正确 - URL 独立成行
# 详情参见
# https://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html

# 错误 - URL 被反斜杠截断
# 详情参见
# https://www.example.com/us/developer/documentation/api/content/\
# v2.0/csv_file_name_extension_full_specification.html
```

---

## 3. Tab 还是空格？

- **空格是首选的缩进方法**
- Tab 仅用于与已用 Tab 缩进的代码保持一致
- Python 禁止混用 Tab 和空格

---

## 4. 空行

### 4.1 规则

| 场景 | 空行数 |
|------|--------|
| 顶级函数和类定义前后 | 2 个空行 |
| 类内方法定义前后 | 1 个空行 |
| 函数内逻辑段落之间 | 少量空行 |
| 相关单行代码组之间 | 可省略空行 |

### 4.2 示例

```python
import os


class MyClass:
    """类文档字符串."""

    def method_one(self):
        pass

    def method_two(self):
        pass


def top_level_function():
    pass


def another_top_level_function():
    pass
```

### 4.3 换页符

Python 接受 `Control-L` (`^L`) 换页符作为空白符，可用于分隔文件相关部分。

---

## 5. 源文件编码

- 核心 Python 发行版代码使用 **UTF-8**
- **不添加编码声明**（如 `# -*- coding: utf-8 -*-`）
- 标准库中非 UTF-8 编码仅用于测试
- 所有标识符使用纯 ASCII
- 鼓励面向全球的开源项目采用类似政策

---

## 6. 模块级双下划线

```python
"""This is the example module.

This module does stuff.
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

- 双下划线名称放在模块文档字符串之后
- 除 `from __future__` 外，放在所有导入之前

---

## 7. Google 风格补充

### 7.1 分号

- 不要在行尾加分号
- 不要用分号将两条语句合并到一行

```python
# 正确
do_one()
do_two()

# 错误
do_one(); do_two()
```

### 7.2 括号

- 元组可以括起来，但不强制
- 不要在返回语句或条件语句中使用括号（除非用于隐式续行或元组）

```python
# 正确
if foo:
    bar()
return foo
return spam, beans
onesie = (foo,)  # 单元素元组用括号更直观

# 错误
if (x):
    bar()
return (foo)
```

### 7.3 尾随逗号

- 仅当 `]`, `)`, `}` 和最后一个元素不在同一行时，推荐尾随逗号
- 多行列表/参数中使用尾随逗号便于 VCS diff

```python
# 正确 - 尾随逗号
FILES = [
    'setup.cfg',
    'tox.ini',
]

# 正确 - 单行不需要
FILES = ['setup.cfg', 'tox.ini']

# 错误 - 闭合符同行的尾随逗号
FILES = ['setup.cfg', 'tox.ini',]
```

### 7.4 Shebang 行

- 大部分 `.py` 文件不必以 `#!` 开始
- 程序主文件可添加 `#!/usr/bin/env python3`
- 仅对直接运行的文件有效

### 7.5 语句

- 通常每个语句独占一行
- 无 `else` 的 `if` 可放在一行：`if foo: bar(foo)` ✅
- `try`/`except` 不能放在同一行

```python
# 正确
if foo: bar(foo)

# 错误
if foo: bar(foo)
else: baz(foo)

try: bar(foo)
except ValueError: baz(foo)
```
