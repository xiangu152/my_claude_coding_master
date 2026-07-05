---
title: "Python 空白符规范详解"
source: "PEP 8 表达式和语句中的空白符"
version: "latest"
---

# Python 空白符规范详解

> 来源：PEP 8 表达式和语句中的空白符

---

## 1. 禁止多余空白的场景

### 1.1 括号内侧

```python
# 正确
spam(ham[1], {eggs: 2})

# 错误
spam( ham[ 1 ], { eggs: 2 } )
```

### 1.2 尾随逗号和闭括号之间

```python
# 正确
foo = (0,)

# 错误
bar = (0, )
```

### 1.3 逗号/分号/冒号之前

```python
# 正确
if x == 4: print(x, y); x, y = y, x

# 错误
if x == 4 : print(x , y) ; x , y = y , x
```

### 1.4 函数调用括号之前

```python
# 正确
spam(1)

# 错误
spam (1)
```

### 1.5 索引/切片括号之前

```python
# 正确
dct['key'] = lst[index]

# 错误
dct ['key'] = lst [index]
```

---

## 2. 切片空白

切片冒号充当二元运算符，两侧应有等量空格：

```python
# 正确
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower:upper], ham[lower:upper:], ham[lower::step]
ham[lower+offset : upper+offset]
ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
ham[lower + offset : upper + offset]

# 错误
ham[lower + offset:upper + offset]  # 两侧空格不等
ham[1: 9], ham[1 :9], ham[1:9 :3]  # 空格不一致
ham[lower : : step]
ham[ : upper]
```

**例外**：切片参数省略时，空格也省略：`ham[1:9]` ✅

---

## 3. 赋值对齐

```python
# 正确
x = 1
y = 2
long_variable = 3

# 错误 - 多余空格对齐
x             = 1
y             = 2
long_variable = 3
```

**注意**：不要为了对齐使用多个空格。

---

## 4. 运算符空白

### 4.1 必须两侧各一个空格的运算符

- 赋值：`=`
- 增广赋值：`+=`, `-=`, `*=`, `/=`, etc.
- 比较：`==`, `<`, `>`, `!=`, `<=`, `>=`, `in`, `not in`, `is`, `is not`
- 布尔：`and`, `or`, `not`

```python
# 正确
i = i + 1
submitted += 1
x = x*2 - 1
hypot2 = x*x + y*y
c = (a+b) * (a-b)

# 错误
i=i+1
submitted +=1
x = x * 2 - 1
hypot2 = x * x + y * y
c = (a + b) * (a - b)
```

### 4.2 不同优先级运算符

```python
# 正确 - 低优先级运算符周围加空格
x = x*2 - 1
c = (a+b) * (a-b)

# 也可以 - 所有运算符两侧加空格（一致性更好）
x = x * 2 - 1
c = (a + b) * (a - b)
```

- 使用不同优先级时，在最低优先级运算符周围添加空格
- 不要使用超过一个空格
- 二元运算符两侧保持相同数量的空白符

---

## 5. 函数注解空白

```python
# 正确
def munge(input: AnyStr): ...
def munge() -> PosInt: ...

# 错误
def munge(input:AnyStr): ...
def munge()->PosInt: ...
```

---

## 6. 关键字参数和默认值

### 6.1 无类型注解

```python
# 正确
def complex(real, imag=0.0):
    return magic(r=real, i=imag)

# 错误
def complex(real, imag = 0.0):
    return magic(r = real, i = imag)
```

### 6.2 有类型注解

```python
# 正确 - 有类型注解时 = 两侧加空格
def munge(sep: AnyStr = None): ...
def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...

# 错误
def munge(input: AnyStr=None): ...
def munge(input: AnyStr, limit = 1000): ...
```

---

## 7. 复合语句

### 7.1 不推荐同行多语句

```python
# 正确
if foo == 'blah':
    do_blah_thing()
do_one()
do_two()
do_three()

# 错误
if foo == 'blah': do_blah_thing()
do_one(); do_two(); do_three()
```

### 7.2 小主体 if/for/while

```python
# 可以接受（单行）
if foo == 'blah': do_blah_thing()

# 不推荐
for x in lst: total += x
while t < 10: t = delay()

# 绝对不行
if foo == 'blah': do_blah_thing()
else: do_non_blah_thing()

try: something()
finally: cleanup()
```

---

## 8. 尾随逗号

### 8.1 单元素元组（强制）

```python
# 正确
FILES = ('setup.cfg',)

# 错误
FILES = 'setup.cfg',
```

### 8.2 多行列表（推荐）

```python
# 正确 - 尾随逗号便于 VCS diff
FILES = [
    'setup.cfg',
    'tox.ini',
    ]
initialize(FILES,
           error=True,
           )

# 错误 - 闭合符同行的尾随逗号
FILES = ['setup.cfg', 'tox.ini',]
initialize(FILES, error=True,)
```

---

## 9. 尾随空白

- **避免在任何地方留下尾随空白符**
- 反斜杠后跟空格和换行符不计为行延续标记
- 一些编辑器不保留尾随空白
- 许多项目有预提交钩子拒绝尾随空白

---

## 10. Google 风格补充

### 10.1 括号使用

```python
# 正确
if foo:
    bar()
while x:
    x = bar()
if x and y:
    bar()
if not x:
    bar()
onesie = (foo,)
return foo
return spam, beans
return (spam, beans)

# 错误
if (x):
    bar()
if not(x):
    bar()
return (foo)
```

### 10.2 最外层分行

```python
# 正确
bridgekeeper.answer(
    name="Arthur", quest=questlib.find(owner="Arthur", perilous=True))

# 错误
bridgekeeper.answer(name="Arthur", quest=questlib.find(
    owner="Arthur", perilous=True))
```

- 尽量在最外层的语法结构上分行
- 如果需要多次换行，在同一层语法结构上换行
