---
title: "Python 注释与文档字符串详解"
source: "PEP 8 注释、PEP 257 文档字符串、Google Python Style Guide"
version: "latest"
---

# Python 注释与文档字符串详解

> 来源：PEP 8 注释、PEP 257 文档字符串、Google Python Style Guide

---

## 1. 注释基本原则

- 与代码相矛盾的注释比没有注释更糟糕
- 代码更改时，始终将注释保持最新
- 注释应为完整句子，首字母大写
- 多句子注释中，句末句点后使用一个或两个空格
- 确保注释清晰易懂，让其他 Python 使用者也能理解
- **非英语国家程序员：用英语编写注释**（除非代码永远不会被外国人阅读）

---

## 2. 块注释

```python
# 正确 - # 后跟一个空格
# This is a block comment.
# It spans multiple lines.

# 正确 - 缩进文本
    # Indented comment within a block.

# 正确 - 段落分隔
# First paragraph of the comment.
#
# Second paragraph of the comment.
```

- 通常适用于其后的代码
- 与代码缩进到同一级别
- 每行以 `#` 和一个空格开头
- 段落用包含单个 `#` 的行分隔

---

## 3. 行内注释

```python
# 正确 - 至少两个空格，# 后一个空格
x = x + 1                 # Compensate for border

# 错误 - 说明显而易见的内容
x = x + 1                 # Increment x

# 错误 - 格式不对
x = x + 1 # no space before
```

- 少量使用
- 与语句至少相隔两个空格
- 以 `#` 和一个空格开头
- 不要说明显而易见的内容

---

## 4. 文档字符串（Docstring）

### 4.1 基本规则（PEP 257）

- **所有公共模块、函数、类和方法必须有文档字符串**
- 使用三重双引号 `"""`
- 单行文档字符串：`"""Summary sentence."""`（结束 `"""` 在同一行）
- 多行文档字符串：概述 → 空行 → 详细描述（结束 `"""` 单独一行）

```python
# 单行
def func():
    """Do something."""

# 多行
def func():
    """Do something.

    Detailed description goes here.
    """
```

### 4.2 非公共方法

- 不需要文档字符串
- 但应有描述功能的注释
- 注释出现在 `def` 行之后

---

## 5. Google 风格文档字符串

### 5.1 模块文档字符串

```python
"""Module or program one-line summary, ending with a period.

Leave a blank line. The overall description of the module or program
goes here. Optionally, briefly describe exported classes and functions,
and/or usage examples.

Classic usage example:

foo = ClassFoo()
bar = foo.FunctionBar()
"""
```

### 5.2 测试模块

```python
# 不必包含模块级文档字符串
# 仅在文档字符串可以提供额外信息时才需要

"""This blaze test uses golden files.

To update, run `blaze run //foo/bar:foo_test -- --update_golden_files`
"""
```

### 5.3 函数文档字符串

必须有文档字符串的函数特征：
- 公开 API 的一部分
- 长度过长
- 逻辑不能一目了然

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Fetch rows from Smalltable.

    Retrieves rows from the Table instance represented by table_handle,
    for the given keys. String keys will be UTF-8 encoded.

    参数:
        table_handle: An open smalltable.Table instance.
        keys: A sequence of strings representing the key of each table
            row to fetch. Strings will be UTF-8 encoded.
        require_all_keys: If True, only rows with values set for all keys
            will be returned.

    返回:
        A dictionary mapping keys to the corresponding table row data
        encoded as strings. For example:

        {b'Serak': ('Rigel VII', 'Preparer'),
         b'Zim': ('Irk', 'Invader'),
         b'Lrrr': ('Omicron Persei 8', 'Emperor')}

        Returned keys are always bytes. If a key from the keys argument
        is not present in the dictionary, then that row was not found in
        the table (and require_all_keys must have been False).

    抛出:
        IOError: An error occurred accessing the smalltable.
    """
```

### 5.4 Args 部分

- 列出所有参数名
- 参数名后跟冒号、空格或换行符、描述
- 描述过长时，悬挂缩进 2 或 4 个空格
- 没有类型注解时，描述中说明类型
- `*foo` 和 `**bar` 写成 `*foo` 和 `**bar`

```python
def my_function():
    """Summary.

    参数:
        first_var: Description of first_var.
        second_var: Description that is too long to fit on
            one line.
        *args: Variable length argument list.
        **kwargs: Arbitrary keyword arguments.
    """
```

### 5.5 Returns 部分

```python
def my_function():
    """Summary.

    返回:
        A tuple (mat_a, mat_b), where mat_a is ..., and ...
    """
```

- 描述返回值的类型和意义
- 仅返回 `None` 时可省略
- 生成器使用 `Yields:` 替代 `Returns:`

### 5.6 Raises 部分

```python
def my_function():
    """Summary.

    抛出:
        ValueError: If key is not found.
        IOError: If file cannot be read.
    """
```

- 列出与接口相关的所有异常和描述
- 不要记录违反 API 使用条件时的异常

### 5.7 类文档字符串

```python
class SampleClass:
    """Summary of the class.

    More details...

    属性:
        likes_spam: Boolean indicating if we like spam.
        eggs: An integer count of the eggs we have laid.
    """

    def __init__(self, likes_spam=False):
        """Initialize SampleClass."""
        self.likes_spam = likes_spam
        self.eggs = 0

    def public_method(self):
        """Do something."""
```

- 概述描述类的实例所代表的事物
- 异常子类描述异常代表什么，而非抛出环境
- 不要有无意义的重复

---

## 6. 块注释和行注释（Google 风格）

```python
# 正确 - 复杂操作前写注释
# We use a weighted dictionary search to find the position of i in
# the array. We infer a position from the max value and array length,
# then use binary search to find the exact result.

# 正确 - 行尾注释
if i & (i-1) == 0:  # True if i is 0 or a power of 2.

# 错误 - 只描述代码
# Now iterate through the array b, ensuring each i encountered has
# the next element be i+1
```

- 注释的 `#` 和代码之间至少 2 个空格
- `#` 和注释之间至少 1 个空格
- 假设读代码的人比你更懂 Python，只是不知道代码要做什么

---

## 7. TODO 注释

```python
# TODO(crbug.com/192795): Investigate cpufreq optimization.
# TODO(your_username): File an issue, using '*' for repeat.

# 具体日期
# TODO(2009年11月前): 解决这个问题.

# 具体事件
# TODO(当所有客户端都能处理 XML 响应时): 删除这些代码.
```

- 以 `TODO` 全大写开头
- 括号内是上下文标识符（bug 链接或用户名）
- 后跟冒号和描述
- `TODO` 不代表被提到的人要做出修复保证

---

## 8. 标点符号、拼写和语法

- 注释应和记叙文一样可读
- 使用恰当的大小写和标点
- 完整的句子比残缺句更可读
- 保持风格一致
