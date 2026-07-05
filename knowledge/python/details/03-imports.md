---
title: "Python 导入规范详解"
source: "PEP 8 导入、Google Python Style Guide 导入/包"
version: "latest"
---

# Python 导入规范详解

> 来源：PEP 8 导入、Google Python Style Guide 导入/包

---

## 1. 基本规则

### 1.1 每行一个导入

```python
# 正确
import os
import sys
from subprocess import Popen, PIPE

# 错误
import sys, os
```

### 1.2 导入位置

- 导入放在文件顶部
- 在模块注释和文档字符串之后
- 在模块全局变量和常量之前
- `from __future__` 导入例外，必须在最前

---

## 2. 导入顺序

导入应按以下顺序分组，**组间用空行分隔**：

```python
# 1. from __future__ 导入
from __future__ import annotations

# 2. 标准库导入
import os
import sys
import collections

# 3. 第三方库导入
import numpy as np
import requests
from flask import Flask

# 4. 本地应用/库导入
from myproject import config
from myproject.utils import helper
```

### 2.1 组内排序

- 按模块完整包路径的字典序排列（忽略大小写）
- `from` 导入在 `import` 导入之后（Google 风格可选）

```python
import collections
import queue
import sys

from absl import app
from absl import flags
import bs4
import tensorflow as tf
```

---

## 3. 绝对导入 vs 相对导入

### 3.1 绝对导入（推荐）

```python
# 正确
import mypkg.sibling
from mypkg import sibling
from mypkg.sibling import example
```

- 通常更具可读性
- 在导入系统配置不正确时行为更好
- 标准库代码应始终使用绝对导入

### 3.2 显式相对导入

```python
# 正确（复杂包布局时可接受）
from . import sibling
from .sibling import example
```

- 绝对导入的可接受替代方案
- 适用于复杂包布局，避免不必要的冗长

### 3.3 Google 风格：禁止相对导入

```python
# 正确 - 使用完整包名
from absl import flags
from doctor.who import jodie

# 错误 - 相对导入（Google 风格）
import jodie  # 不确定导入的是哪个模块
```

---

## 4. 导入类

```python
# 正确 - 直接导入类
from myclass import MyClass
from foo.bar.yourclass import YourClass

# 正确 - 名称冲突时导入模块
import myclass
import foo.bar.yourclass
# 使用 myclass.MyClass, foo.bar.yourclass.YourClass
```

---

## 5. 通配符导入

```python
# 错误 - 禁止
from module import *

# 唯一合理用例：重新发布内部接口
# 例如用可选加速器模块覆盖纯 Python 实现
```

- **禁止使用 `from <module> import *`**
- 使命名空间不明确，混淆读者和自动化工具
- 重新发布名称时仍需遵循公共/内部接口指南

---

## 6. 模块级双下划线

```python
"""模块文档字符串."""

from __future__ import annotations

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

- 放在模块文档字符串之后
- 除 `from __future__` 外，放在所有导入之前

---

## 7. from x import y as z

```python
# 正确 - 两个模块都叫 y 时
from mypackage import y as my_y
from otherpackage import y as other_y

# 正确 - 标准缩写
import numpy as np
import tensorflow as tf

# 错误 - 非标准缩写
import pandas as pd  # pd 是标准缩写，可以
import my_long_module as mlm  # 非标准，不推荐
```

- 当有两个同名模块时使用 `as`
- 当名称与当前模块全局名称冲突时使用 `as`
- 当名称过长时使用 `as`
- 仅当缩写 `z` 是标准缩写时才使用 `import y as z`

---

## 8. typing 和 collections.abc 导入

```python
# 正确 - 直接导入符号
from collections.abc import Mapping, Sequence
from typing import Any, NewType

# 可以在一行内导入多个
from typing import Any, Generic, TypeVar
```

- 为了类型检查导入时，**直接导入符号本身**
- 常用类型注解更简洁
- 导入的类进入本地命名空间，与关键字同等对待

---

## 9. 有条件导入

```python
# 正确 - 仅在 TYPE_CHECKING 时导入
import typing
if typing.TYPE_CHECKING:
    import sketch
def f(x: "sketch.Sketch"): ...

# 用于避免循环依赖
from typing import Any
some_mod = Any  # 因为 some_mod.py 导入了我们的模块
```

- 仅在运行时必须避免导入类型检查模块时使用
- 不推荐，优先重构代码使模块可在顶层导入
- 类型注解中必须用字符串表示

---

## 10. Google 风格导入补充

### 10.1 导入包和模块

```python
# 正确 - 导入包和模块
from sound.effects import echo
echo.EchoFilter(input, output, delay=0.7)

# 错误 - 单独导入函数/类（Google 风格不推荐）
from sound.effects.echo import EchoFilter
```

- Google 风格推荐只导入包和模块，不单独导入函数或类
- 命名空间管理更规范：`x.Obj` 表示 Obj 定义在模块 x 中

### 10.2 导入分组顺序（Google 风格）

```python
# 1. __future__ 导入
from __future__ import annotations

# 2. 标准库
import sys

# 3. 第三方库
import tensorflow as tf

# 4. 仓库中的其他子包
from otherproject.ai import mind

# 5. (已废弃) 同一子包的模块
from myproject.backend.hgwells import time_machine
```

### 10.3 完整包名

```python
# 正确
import absl.flags
from doctor.who import jodie

# 错误 - 不明确
import jodie
```

- 所有新代码使用完整包名导入
- 不能臆测 `sys.path` 包含主程序目录
