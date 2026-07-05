---
title: "Python 异常处理详解"
source: "PEP 8 编程建议、Google Python Style Guide 异常"
version: "latest"
---

# Python 异常处理详解

> 来源：PEP 8 编程建议、Google Python Style Guide 异常

---

## 1. 异常设计原则

### 1.1 继承层次

```python
# 正确 - 从 Exception 派生
class NotFoundError(Exception):
    pass

class ValidationError(Exception):
    pass

# 错误 - 直接从 BaseException 继承
class MyError(BaseException):  # 仅用于特殊场景
    pass
```

- 从 `Exception` 而非 `BaseException` 派生
- `BaseException` 保留给那些几乎总是错误处理方式的异常

### 1.2 异常命名

```python
# 正确 - 以 Error 结尾
class CheeseShopError(Exception):
    pass

# 错误 - 缺少 Error 后缀
class CheeseShop(Exception):
    pass
```

### 1.3 设计原则

- 基于代码**捕获**异常可能需要的区别来设计层次
- 不是基于引发异常的位置
- 目标：程序化地回答"哪里出错了？"

---

## 2. 抛出异常

### 2.1 使用 raise

```python
# 正确
if minimum < 1024:
    raise ValueError(f'Minimum port must be at least 1024, not {minimum}.')

# 错误 - 不用 assert 验证公开 API 参数
assert minimum >= 1024, 'Minimum port must be at least 1024.'
```

- **禁止使用 `assert` 验证公开 API 参数**
- `assert` 仅用于保证内部正确性
- `assert` 可被 `-O` 标志禁用

### 2.2 异常链

```python
# 正确 - 显式替换
raise ValueError("new error") from original_error

# 正确 - 故意替换（保留原始信息）
raise ValueError(f"Conversion failed: {key_error}") from None
```

- 使用 `raise X from Y` 表示显式替换
- 故意替换时，确保相关细节已传输到新异常

---

## 3. 捕获异常

### 3.1 具体异常

```python
# 正确
try:
    import platform_specific_module
except ImportError:
    platform_specific_module = None

# 错误 - 裸 except
try:
    do_something()
except:  # 捕获 SystemExit, KeyboardInterrupt 等
    pass
```

### 3.2 最小化 try 块

```python
# 正确
try:
    value = collection[key]
except KeyError:
    return key_not_found(key)
else:
    return handle_value(value)

# 错误 - try 范围过大
try:
    return handle_value(collection[key])
except KeyError:
    return key_not_found(key)
```

### 3.3 except 子句顺序

```python
try:
    do_something()
except FileNotFoundError:
    handle_not_found()
except PermissionError:
    handle_permission()
except OSError:
    handle_os_error()
```

- 从具体到一般排列
- 先捕获子类异常，再捕获父类异常

---

## 4. finally 和 else

```python
# finally - 无论异常与否都执行
try:
    resource = acquire_resource()
    use_resource(resource)
finally:
    release_resource(resource)

# else - 没有异常时执行
try:
    value = int(user_input)
except ValueError:
    print("Invalid input")
else:
    print(f"You entered: {value}")
```

### 4.1 finally 中的流控制

```python
# 错误 - finally 中 return 会取消异常
def foo():
    try:
        1 / 0
    finally:
        return 42  # 隐式取消 ZeroDivisionError
```

---

## 5. 资源管理

### 5.1 with 语句

```python
# 正确
with open("hello.txt") as hello_file:
    for line in hello_file:
        print(line)

# 正确 - contextlib.closing
import contextlib
with contextlib.closing(urllib.urlopen("https://www.python.org/")) as page:
    for line in page:
        print(line)
```

### 5.2 上下文管理器明确性

```python
# 正确 - 明确使用方法
with conn.begin_transaction():
    do_stuff_in_transaction(conn)

# 错误 - 不够明确
with conn:
    do_stuff_in_transaction(conn)
```

---

## 6. Google 风格异常

### 6.1 使用内置异常

```python
# 正确 - 使用合适的内置异常
def connect_to_next_port(self, minimum: int) -> int:
    if minimum < 1024:
        raise ValueError(f'Minimum port must be at least 1024, not {minimum}.')
    port = self._find_next_open_port(minimum)
    if port is None:
        raise ConnectionError(
            f'Could not connect to service on port {minimum} or higher.')
    return port
```

### 6.2 自定义异常

```python
# 模块或包可以定义自己的异常类型
# 必须继承已有的异常类
# 异常类型名以 Error 为后缀
class FooError(Exception):
    pass
```

### 6.3 禁止裸 except

```python
# 错误
try:
    do_something()
except:
    pass

# 正确
try:
    do_something()
except Exception:
    pass  # 或者重新抛出
```

- `except:` 会捕获 `SystemExit`、`KeyboardInterrupt` 等
- 使用 `except Exception:` 捕获所有程序错误

### 6.4 两种允许裸 except 的情况

1. 异常处理程序将打印或记录回溯
2. 代码需要做清理工作，然后让异常通过 `raise` 向上传播

---

## 7. 异常处理最佳实践

### 7.1 单一职责

```python
# 每个 try 块只处理一种可能的异常场景
try:
    config = load_config(path)
except FileNotFoundError:
    config = default_config()
```

### 7.2 有意义的错误消息

```python
# 正确
raise ValueError(f'Expected a positive number, got {value}')

# 错误
raise ValueError('Invalid input')
```

### 7.3 异常 vs 返回值

```python
# 异常用于异常情况
def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("Cannot divide by zero")
    return a / b

# 返回值用于正常情况
def find_user(user_id):
    user = db.get(user_id)
    if user is None:
        return None
    return user
```

### 7.4 不要忽略异常

```python
# 错误 - 静默忽略
try:
    do_something()
except Exception:
    pass

# 正确 - 至少记录
try:
    do_something()
except Exception:
    logging.exception("Something went wrong")
    raise  # 或者处理后继续
```
