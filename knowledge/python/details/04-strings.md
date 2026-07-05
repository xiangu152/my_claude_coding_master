---
title: "Python 字符串规范详解"
source: "PEP 8 字符串引号、Google Python Style Guide 字符串"
version: "latest"
---

# Python 字符串规范详解

> 来源：PEP 8 字符串引号、Google Python Style Guide 字符串

---

## 1. 引号风格

### 1.1 基本规则

- 单引号和双引号在 Python 中等价
- **选择一种并保持一致**
- 当字符串包含单引号时用双引号，反之亦然
- **三引号字符串始终使用 `"""`**（与 PEP 257 文档字符串约定一致）

```python
# 正确
a = 'hello'
b = "world"
c = "it's a test"  # 包含单引号，用双引号
d = 'he said "hello"'  # 包含双引号，用单引号

# 正确 - 三引号
"""这是一个三引号字符串"""

# 错误 - 三引号用单引号
'''这不是推荐的三引号字符串'''
```

### 1.2 Google 风格

```python
# 正确 - 引号一致性
Python('Why are you hiding your eyes?')
Gollum("I'm scared of lint errors.")
Narrator('"Good!" thought a happy Python reviewer.')

# 错误 - 引号不一致
Python("Why are you hiding your eyes?")
Gollum('The lint. It burns. It burns us.')
```

---

## 2. 字符串格式化

### 2.1 优先级

```
f-string > .format() > % 运算符 > + 拼接（禁止）
```

### 2.2 f-string（推荐，Python 3.6+）

```python
name = 'Alice'
score = 95
x = f'名称: {name}; 分数: {score}'
```

### 2.3 % 运算符

```python
x = '%s, %s!' % (imperative, expletive)
x = '名称: %s; 分数: %d' % (name, n)
x = '名称: %(name)s; 分数: %(score)d' % {'name': name, 'score': n}
```

### 2.4 .format() 方法

```python
x = '{}, {}'.format(first, second)
x = '名称: {}; 分数: {}'.format(name, n)
```

### 2.5 禁止 + 拼接

```python
# 错误
x = first + ', ' + second
x = '名称: ' + name + '; 分数: ' + str(n)
```

---

## 3. 循环中的字符串拼接

```python
# 正确 - 使用 join
items = ['<table>']
for last_name, first_name in employee_list:
    items.append('<tr><td>%s, %s</td></tr>' % (last_name, first_name))
items.append('</table>')
employee_table = ''.join(items)

# 错误 - 循环中用 + 拼接（可能产生平方时间复杂度）
employee_table = '<table>'
for last_name, first_name in employee_list:
    employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
employee_table += '</table>'
```

---

## 4. 多行字符串

```python
# 正确 - 多行字符串不跟进缩进
long_string = """If you accept extra spaces,
    you can do this."""

# 正确 - 拼接方式避免额外空格
long_string = ("If you can't accept extra spaces,\n"
               "you can do this.")

# 正确 - textwrap.dedent
import textwrap
long_string = textwrap.dedent("""\
    This works because textwrap.dedent()
    removes common leading whitespace.""")

# 错误 - 缩进导致额外空格
    long_string = """This looks ugly.
Don't do this.
"""
```

---

## 5. 日志字符串

```python
# 正确 - 使用字符串字面量作为第一个参数
import logging
logging.info('The current $PAGER is: %s', os.getenv('PAGER', default=''))

# 错误 - 使用 f-string
logging.info(f'The current $PAGER is: {os.getenv("PAGER")}')
```

- 日志函数的第一个参数使用字符串字面量（非 f-string）
- 有些日志实现会收集未展开的格式字符串作为可搜索项目
- 避免渲染被设置为不用输出的消息

---

## 6. 错误信息

错误信息（异常消息、用户提示）应遵守：

1. **精确匹配**真正的错误条件
2. 插入的片段能**清晰分辨**
3. 便于**自动化处理**（如正则搜索）

```python
# 正确
if not 0 <= p <= 1:
    raise ValueError(f'Not a probability: {p!r}')

try:
    os.rmdir(workdir)
except OSError as error:
    logging.warning('Could not remove directory (reason: %r): %r',
                    error, workdir)

# 错误 - 条件不精确（float('nan') 时也为假）
if p < 0 or p > 1:
    raise ValueError(f'Not a probability: {p!r}')

# 错误 - 信息不可搜索
logging.warning('Cannot delete %s directory.', workdir)
# 如果 workdir = 'deleted'，会变成 "Cannot delete deleted directory."
```

---

## 7. 字符串比较

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

---

## 8. 前缀/后缀检查

```python
# 正确 - 使用 startswith/endswith
if foo.startswith('bar'):
    pass

# 错误 - 使用切片
if foo[:3] == 'bar':
    pass
```

---

## 9. 尾随空白

- 不要编写依赖于重要尾随空白符的字符串字面量
- 尾随空白在视觉上无法区分
- 一些编辑器或 reindent.py 会将其修剪
- 避免在任何地方留下尾随空白符
