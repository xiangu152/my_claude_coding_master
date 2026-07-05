---
title: "Python 安全与最佳实践详解"
source: "PEP 8 编程建议、Google Python Style Guide、Python 安全最佳实践"
version: "latest"
---

# Python 安全与最佳实践详解

> 来源：PEP 8 编程建议、Google Python Style Guide、Python 安全最佳实践

---

## 1. 输入验证

### 1.1 永远不要信任用户输入

```python
# 正确 - 验证输入
def process_age(age_str: str) -> int:
    """Process age input with validation."""
    try:
        age = int(age_str)
    except ValueError:
        raise ValueError(f"Age must be a number, got: {age_str!r}")

    if not 0 <= age <= 150:
        raise ValueError(f"Age must be between 0 and 150, got: {age}")

    return age

# 错误 - 直接使用未验证输入
def process_age(age_str: str) -> int:
    return int(age_str)  # 可能抛出 ValueError
```

### 1.2 类型检查

```python
# 正确
def process(data: dict) -> None:
    if not isinstance(data, dict):
        raise TypeError(f"Expected dict, got {type(data).__name__}")
    ...

# 正确 - 使用类型注解 + 运行时检查
def process(items: list[int]) -> int:
    if not all(isinstance(x, int) for x in items):
        raise TypeError("All items must be integers")
    return sum(items)
```

---

## 2. SQL 注入防护

### 2.1 使用参数化查询

```python
# 正确 - 参数化查询
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

# 正确 - ORM 查询
User.objects.filter(id=user_id)

# 错误 - 字符串拼接
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
```

### 2.2 使用 ORM

```python
# SQLAlchemy 示例
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, Session

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String)

# 安全查询
with Session(engine) as session:
    user = session.query(User).filter(User.id == user_id).first()
```

---

## 3. 命令注入防护

### 3.1 使用 subprocess

```python
# 正确 - 使用列表参数
import subprocess
result = subprocess.run(["ls", "-la", directory], capture_output=True, text=True)

# 正确 - 使用 shlex.quote
import shlex
import subprocess
command = f"ls -la {shlex.quote(directory)}"
subprocess.run(command, shell=True, capture_output=True, text=True)

# 错误 - 直接拼接
import os
os.system(f"ls -la {directory}")  # 命令注入风险！
```

### 3.2 避免 shell=True

```python
# 正确
subprocess.run(["echo", user_input], capture_output=True)

# 错误 - shell=True 配合用户输入
subprocess.run(f"echo {user_input}", shell=True)  # 命令注入风险！
```

---

## 4. 文件操作安全

### 4.1 路径遍历防护

```python
# 正确 - 验证路径在允许目录内
import os

def safe_file_read(base_dir: str, filename: str) -> str:
    """Read file with path traversal protection."""
    base = os.path.abspath(base_dir)
    target = os.path.abspath(os.path.join(base, filename))

    if not target.startswith(base):
        raise ValueError("Path traversal detected")

    with open(target) as f:
        return f.read()

# 错误 - 直接使用用户输入的路径
def unsafe_file_read(filename: str) -> str:
    with open(filename) as f:  # 可能读取 /etc/passwd
        return f.read()
```

### 4.2 使用 pathlib

```python
from pathlib import Path

def safe_file_read(base_dir: Path, filename: str) -> str:
    """Read file with path traversal protection using pathlib."""
    base = Path(base_dir).resolve()
    target = (base / filename).resolve()

    if not str(target).startswith(str(base)):
        raise ValueError("Path traversal detected")

    return target.read_text()
```

---

## 5. 密码和密钥处理

### 5.1 永远不要硬编码

```python
# 错误 - 硬编码密码
DATABASE_PASSWORD = "my_secret_password"

# 正确 - 使用环境变量
import os
DATABASE_PASSWORD = os.environ["DATABASE_PASSWORD"]

# 正确 - 使用配置文件（不提交到版本控制）
# config.py（加入 .gitignore）
DATABASE_PASSWORD = "..."
```

### 5.2 密码哈希

```python
# 正确 - 使用 bcrypt
import bcrypt

def hash_password(password: str) -> str:
    """Hash a password using bcrypt."""
    salt = bcrypt.gensalt()
    return bcrypt.hashpw(password.encode(), salt).decode()

def verify_password(password: str, hashed: str) -> bool:
    """Verify a password against its hash."""
    return bcrypt.checkpw(password.encode(), hashed.encode())

# 错误 - 使用 MD5/SHA1
import hashlib
def hash_password(password: str) -> str:
    return hashlib.md5(password.encode()).hexdigest()  # 不安全！
```

---

## 6. 序列化安全

### 6.1 避免 pickle

```python
# 错误 - pickle 可以执行任意代码
import pickle
data = pickle.loads(user_provided_data)  # 安全风险！

# 正确 - 使用 JSON
import json
data = json.loads(user_provided_data)

# 正确 - 使用限制性反序列化
import json
def safe_loads(data: str) -> dict:
    """Safely load JSON data."""
    try:
        return json.loads(data)
    except json.JSONDecodeError as e:
        raise ValueError(f"Invalid JSON: {e}")
```

### 6.2 YAML 安全

```python
# 正确 - 使用 safe_load
import yaml
data = yaml.safe_load(yaml_string)

# 错误 - 使用 load（可以执行任意代码）
data = yaml.load(yaml_string)  # 安全风险！
```

---

## 7. 异常处理安全

### 7.1 不要泄露敏感信息

```python
# 错误 - 泄露敏感信息
def connect():
    try:
        db.connect(host="db.example.com", password="secret123")
    except Exception as e:
        raise ConnectionError(f"Failed to connect: {e}")  # 可能泄露密码

# 正确 - 记录详细错误，返回通用消息
import logging

def connect():
    try:
        db.connect(host="db.example.com", password="secret123")
    except Exception as e:
        logging.exception("Database connection failed")  # 记录详细信息
        raise ConnectionError("Failed to connect to database")  # 通用消息
```

### 7.2 不要静默忽略安全错误

```python
# 错误
try:
    verify_token(token)
except AuthenticationError:
    pass  # 静默忽略认证失败

# 正确
try:
    verify_token(token)
except AuthenticationError:
    logging.warning("Authentication failed")
    raise  # 重新抛出
```

---

## 8. 依赖安全

### 8.1 使用依赖锁定

```bash
# 使用 pip freeze 生成 requirements.txt
pip freeze > requirements.txt

# 或使用 pip-tools
pip-compile requirements.in

# 或使用 poetry
poetry lock
```

### 8.2 定期更新依赖

```bash
# 检查过时的包
pip list --outdated

# 更新特定包
pip install --upgrade package_name

# 使用 safety 检查已知漏洞
pip install safety
safety check
```

---

## 9. 日志安全

### 9.1 不要记录敏感信息

```python
import logging

# 错误 - 记录敏感信息
logging.info(f"User login: {username}, password: {password}")

# 正确 - 只记录必要信息
logging.info(f"User login: {username}")

# 正确 - 脱敏处理
logging.info(f"User login: {username}, token: {token[:4]}...")
```

### 9.2 日志级别

```python
import logging

# 不同级别
logging.debug("Detailed debug info")  # 开发时使用
logging.info("General info")  # 正常操作
logging.warning("Warning")  # 潜在问题
logging.error("Error")  # 错误但程序继续
logging.critical("Critical")  # 严重错误
```

---

## 10. 加密和随机数

### 10.1 使用 secrets 模块

```python
import secrets

# 生成安全的随机 token
token = secrets.token_hex(32)

# 生成安全的随机整数
random_int = secrets.randbelow(100)

# 从序列中随机选择
choice = secrets.choice(["a", "b", "c"])

# 比较密码学安全的字符串
if secrets.compare_digest(expected, actual):
    pass
```

### 10.2 不要使用 random 模块用于安全

```python
# 错误 - random 不是密码学安全的
import random
token = ''.join(random.choices('abcdefghijklmnopqrstuvwxyz', k=32))

# 正确 - 使用 secrets
import secrets
token = secrets.token_hex(32)
```

---

## 11. 线程安全

### 11.1 使用锁

```python
import threading

class ThreadSafeCounter:
    """A thread-safe counter."""

    def __init__(self):
        self._count = 0
        self._lock = threading.Lock()

    def increment(self):
        with self._lock:
            self._count += 1

    @property
    def count(self):
        with self._lock:
            return self._count
```

### 11.2 使用队列

```python
import queue
import threading

# 正确 - 使用 Queue 进行线程间通信
q = queue.Queue()

def producer():
    for i in range(10):
        q.put(i)

def consumer():
    while True:
        item = q.get()
        if item is None:
            break
        process(item)
        q.task_done()
```

---

## 12. 资源管理

### 12.1 使用 with 语句

```python
# 正确
with open("file.txt") as f:
    data = f.read()

# 正确 - 多个资源
with open("input.txt") as fin, open("output.txt", "w") as fout:
    fout.write(fin.read())

# 错误 - 不使用 with
f = open("file.txt")
data = f.read()
f.close()  # 可能不会执行（异常时）
```

### 12.2 临时文件

```python
import tempfile

# 正确 - 使用 tempfile
with tempfile.NamedTemporaryFile(mode='w', delete=False) as f:
    f.write(data)
    temp_path = f.name

try:
    process_file(temp_path)
finally:
    os.unlink(temp_path)
```

---

## 13. 网络安全

### 13.1 HTTPS

```python
import requests

# 正确 - 使用 HTTPS
response = requests.get("https://api.example.com/data")

# 错误 - 使用 HTTP（不安全）
response = requests.get("http://api.example.com/data")
```

### 13.2 SSL 验证

```python
import requests

# 正确 - 验证 SSL 证书
response = requests.get("https://api.example.com/data", verify=True)

# 错误 - 禁用 SSL 验证
response = requests.get("https://api.example.com/data", verify=False)  # 不安全！
```

---

## 14. 代码审查清单

- [ ] 输入验证：所有用户输入都经过验证
- [ ] SQL 注入：使用参数化查询或 ORM
- [ ] 命令注入：避免 shell=True，使用列表参数
- [ ] 路径遍历：验证文件路径在允许目录内
- [ ] 密码处理：使用 bcrypt/argon2，不硬编码
- [ ] 序列化：避免 pickle，使用 JSON
- [ ] 异常处理：不泄露敏感信息
- [ ] 依赖安全：定期更新，检查漏洞
- [ ] 日志安全：不记录敏感信息
- [ ] 加密：使用 secrets 模块
- [ ] 线程安全：使用锁和队列
- [ ] 资源管理：使用 with 语句
- [ ] 网络安全：使用 HTTPS，验证 SSL
