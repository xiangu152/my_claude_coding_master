---
title: "Python 测试规范详解"
source: "Google Python Style Guide、Python 测试最佳实践"
version: "latest"
---

# Python 测试规范详解

> 来源：Google Python Style Guide、Python 测试最佳实践

---

## 1. 测试文件组织

### 1.1 文件命名

```python
# 测试文件与被测文件对应
my_module.py          → test_my_module.py
my_package/module.py  → test_module.py
```

### 1.2 目录结构

```
project/
├── src/
│   └── mypackage/
│       ├── __init__.py
│       └── core.py
└── tests/
    ├── __init__.py
    ├── test_core.py
    └── conftest.py  # pytest fixtures
```

---

## 2. 测试方法命名

### 2.1 Google 风格

```python
class MyTest(unittest.TestCase):
    def test_public_method_returns_expected(self):
        pass

    def test_public_method_raises_on_invalid_input(self):
        pass

    def test_public_method_handles_edge_case(self):
        pass
```

- 格式：`test_<被测方法名>_<状态>`
- 遵循 PEP 8 小写加下划线

### 2.2 pytest 风格

```python
def test_calculate_tax_with_valid_amount():
    pass

def test_calculate_tax_raises_on_negative_amount():
    pass

def test_calculate_tax_handles_zero_amount():
    pass
```

---

## 3. 测试结构

### 3.1 AAA 模式

```python
def test_calculate_tax():
    # Arrange - 准备测试数据
    amount = 100
    rate = 0.1

    # Act - 执行被测操作
    result = calculate_tax(amount, rate)

    # Assert - 验证结果
    assert result == 10.0
```

### 3.2 Given-When-Then

```python
def test_calculate_tax():
    # Given
    amount = 100
    rate = 0.1

    # When
    result = calculate_tax(amount, rate)

    # Then
    assert result == 10.0
```

---

## 4. 断言

### 4.1 pytest 断言

```python
# 相等
assert result == expected

# 不等
assert result != unexpected

# 布尔值
assert is_valid
assert not is_invalid

# 包含
assert item in collection
assert item not in collection

# 类型
assert isinstance(result, int)

# 近似（浮点数）
assert result == pytest.approx(expected, rel=1e-6)

# 异常
with pytest.raises(ValueError, match="invalid"):
    int("not a number")
```

### 4.2 unittest 断言

```python
class MyTest(unittest.TestCase):
    def test_example(self):
        self.assertEqual(result, expected)
        self.assertNotEqual(result, unexpected)
        self.assertTrue(condition)
        self.assertFalse(condition)
        self.assertIn(item, collection)
        self.assertIsInstance(result, int)
        self.assertAlmostEqual(result, expected, places=6)
        self.assertRaises(ValueError, int, "not a number")
```

---

## 5. Fixtures

### 5.1 pytest fixtures

```python
import pytest

@pytest.fixture
def sample_user():
    """Create a sample user for testing."""
    return User(name="Alice", age=30)

@pytest.fixture
def database():
    """Create a test database connection."""
    db = create_test_database()
    yield db
    db.cleanup()

# 使用 fixture
def test_user_name(sample_user):
    assert sample_user.name == "Alice"

def test_database_query(database):
    result = database.query("SELECT 1")
    assert result is not None
```

### 5.2 Fixture 作用域

```python
@pytest.fixture(scope="session")
def database():
    """整个测试会话只创建一次"""
    db = create_database()
    yield db
    db.cleanup()

@pytest.fixture(scope="module")
def api_client():
    """每个模块创建一次"""
    return ApiClient()

@pytest.fixture(scope="function")
def temp_file():
    """每个函数创建一次（默认）"""
    f = create_temp_file()
    yield f
    f.delete()
```

### 5.3 conftest.py

```python
# tests/conftest.py
import pytest

@pytest.fixture
def common_data():
    """Shared fixture across all test files."""
    return {"key": "value"}

# 自动对同目录及子目录的测试可用
```

---

## 6. 参数化测试

### 6.1 pytest 参数化

```python
@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
    (0, 0),
    (-1, -2),
])
def test_double(input, expected):
    assert double(input) == expected

@pytest.mark.parametrize("invalid_input", [
    None,
    "",
    "not_a_number",
])
def test_parse_int_raises(invalid_input):
    with pytest.raises(ValueError):
        int(invalid_input)
```

### 6.2 多参数组合

```python
@pytest.mark.parametrize("x", [1, 2, 3])
@pytest.mark.parametrize("y", [10, 20])
def test_multiply(x, y):
    assert multiply(x, y) == x * y
```

---

## 7. Mock 和 Patch

### 7.1 unittest.mock

```python
from unittest.mock import Mock, patch, MagicMock

# Mock 对象
def test_with_mock():
    mock_db = Mock()
    mock_db.query.return_value = [{"id": 1}]
    result = get_user(mock_db, user_id=1)
    assert result == {"id": 1}
    mock_db.query.assert_called_once_with("SELECT * FROM users WHERE id=1")

# Patch 装饰器
@patch('mymodule.external_api')
def test_with_patch(mock_api):
    mock_api.get.return_value = {"status": "ok"}
    result = fetch_data()
    assert result["status"] == "ok"

# 上下文管理器
def test_with_context_manager():
    with patch('mymodule.os.path.exists') as mock_exists:
        mock_exists.return_value = True
        assert file_exists("test.txt") is True
```

### 7.2 pytest monkeypatch

```python
def test_with_monkeypatch(monkeypatch):
    mock_get = Mock(return_value=Mock(status_code=200, json=lambda: {"data": 1}))
    monkeypatch.setattr(requests, "get", mock_get)
    result = fetch_data()
    assert result == {"data": 1}
```

---

## 8. 测试分类

### 8.1 单元测试

```python
# 测试单个函数/方法
def test_calculate_tax():
    assert calculate_tax(100, 0.1) == 10.0

# 测试单个类
class TestUser:
    def test_full_name(self):
        user = User(first="John", last="Doe")
        assert user.full_name == "John Doe"
```

### 8.2 集成测试

```python
# 测试多个组件协作
def test_user_registration_flow():
    user = register_user("alice", "alice@example.com")
    assert user.is_active
    email = get_last_email()
    assert "Welcome" in email.subject
```

### 8.3 端到端测试

```python
# 测试完整流程
def test_api_create_user():
    response = client.post("/users", json={"name": "Alice"})
    assert response.status_code == 201
    data = response.json()
    assert data["name"] == "Alice"
```

---

## 9. 测试最佳实践

### 9.1 测试命名

```python
# 好的命名 - 描述行为
def test_calculate_tax_returns_zero_for_zero_amount():
    pass

def test_register_user_raises_on_duplicate_email():
    pass

# 差的命名 - 不够具体
def test_tax():
    pass

def test_user():
    pass
```

### 9.2 测试隔离

```python
# 每个测试应该独立运行
def test_a():
    # 不依赖 test_b 的结果
    pass

def test_b():
    # 不依赖 test_a 的结果
    pass
```

### 9.3 测试数据

```python
# 使用 fixtures 提供测试数据
@pytest.fixture
def sample_orders():
    return [
        Order(id=1, amount=100),
        Order(id=2, amount=200),
    ]

# 使用工厂模式
@pytest.fixture
def user_factory():
    def create_user(name="Alice", age=30):
        return User(name=name, age=age)
    return create_user

def test_user(user_factory):
    user = user_factory(name="Bob")
    assert user.name == "Bob"
```

### 9.4 边界条件

```python
def test_calculate_tax():
    # 正常情况
    assert calculate_tax(100, 0.1) == 10.0

    # 边界条件
    assert calculate_tax(0, 0.1) == 0.0
    assert calculate_tax(100, 0.0) == 0.0

    # 异常情况
    with pytest.raises(ValueError):
        calculate_tax(-100, 0.1)
```

---

## 10. 测试配置

### 10.1 pytest.ini

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = -v --tb=short
```

### 10.2 pyproject.toml

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = "-v --tb=short"
```

### 10.3 conftest.py 全局配置

```python
# tests/conftest.py
import pytest

def pytest_configure(config):
    """自定义 pytest 配置"""
    config.addinivalue_line("markers", "slow: mark test as slow")

@pytest.fixture(autouse=True)
def setup_test_environment():
    """自动运行的 fixture"""
    setup()
    yield
    teardown()
```

---

## 11. 覆盖率

### 11.1 pytest-cov

```bash
# 运行测试并生成覆盖率报告
pytest --cov=mypackage --cov-report=html

# 查看未覆盖的行
pytest --cov=mypackage --cov-report=term-missing
```

### 11.2 覆盖率配置

```ini
# .coveragerc
[run]
source = mypackage
omit = tests/*

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise NotImplementedError
```

---

## 12. 测试文档字符串

```python
def test_calculate_tax_with_valid_amount():
    """Test that tax calculation works correctly.

    Given a valid amount and tax rate,
    When calculate_tax is called,
    Then it should return the correct tax amount.
    """
    # 测试代码...
```

- 描述测试的 Given-When-Then
- 帮助理解测试意图
- 在测试失败时提供上下文
