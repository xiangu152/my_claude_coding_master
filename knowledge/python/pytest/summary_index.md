# pytest 知识库

> **版本**: stable | **来源**: docs.pytest.org | **文件数**: 50 | **总大小**: ~790KB

## 文档层级结构

```
~/.claude/knowledge/python/pytest/
├── summary_index.md              ← 本文件（分层抽象）
└── details/                      ← 完整原始文档（50 文件）
    ├── 01-getting-started.md     ← 入门指南
    ├── 02~24-how-to/*.md         ← 操作指南（23 文件）
    ├── 25-builtin-fixtures.md    ← 内置 fixtures
    ├── 26a~g-api-*.md            ← API 参考（7 文件，按主题拆分）
    ├── 27~29-reference/*.md      ← 参考文档
    ├── 30~36-explanation/*.md    ← 原理解释
    └── 37~42-examples/*.md       ← 示例与技巧
```

## 架构总览

```
pytest 测试框架
├── 核心概念
│   ├── Test Functions      → test_*.py 中的 test_*() 函数
│   ├── Assertions          → 原生 assert + pytest.raises/warns/approx
│   ├── Fixtures            → @pytest.fixture 依赖注入
│   ├── Marks               → @pytest.mark.* 标记元数据
│   └── Parametrize         → @pytest.mark.parametrize 参数化
│
├── 执行流程
│   ├── Collection          → 发现 test_*.py, Test* 类, test_* 函数
│   ├── Setup               → fixture setup, yield 前
│   ├── Call                → 执行测试函数
│   ├── Teardown            → yield 后, fixture cleanup
│   └── Reporting           → 终端输出, pytest-html, junitxml
│
├── 配置系统
│   ├── pyproject.toml      → [tool.pytest.ini_options]
│   ├── pytest.ini          → [pytest] 节
│   ├── conftest.py         → 本地 fixture/hooks, 目录级配置
│   └── CLI flags           → -v, -x, -k, -m, --tb, etc.
│
├── 插件生态
│   ├── pytest-xdist        → 并行执行
│   ├── pytest-cov          → 覆盖率
│   ├── pytest-mock         → mocker fixture
│   ├── pytest-asyncio      → 异步测试
│   └── 自定义插件          → conftest.py 或 pip 包
│
└── 高级特性
    ├── Hooks               → pytest_collection_modify_items 等
    ├── Monkeypatch         → 临时修改对象/环境变量
    ├── tmp_path            → 临时目录隔离
    ├── Capture             → stdout/stderr 捕获
    └── Cache               → --lf, --ff 失败重跑
```

## 文件索引

| # | 文件名 | 主题 | 大小 |
|---|--------|------|------|
| 01 | 01-getting-started.md | 入门：安装、首个测试、异常、类、approx、tmp_path | 8KB |
| **操作指南 (How-to)** | | | |
| 02 | 02-invocation.md | 调用方式：命令行、python -m、配置 | 6KB |
| 03 | 03-assertions.md | 断言：raises、warns、approx、自定义消息 | 18KB |
| 04 | 04-fixtures.md | Fixtures：scope、yield、autouse、params、共享 | 55KB |
| 05 | 05-marks.md | Markers：自定义标记、strict_markers、注册 | 3KB |
| 06 | 06-parametrize.md | 参数化：indirect、ids、pytest.param | 9KB |
| 07 | 07-subtests.md | Subtests：pytest-subtests 参数化子测试 | 4KB |
| 08 | 08-tmp-path.md | 临时目录：tmp_path、tmp_path_factory | 5KB |
| 09 | 09-monkeypatch.md | Monkeypatch：修改对象、环境变量、属性 | 14KB |
| 10 | 10-doctest.md | Doctest：运行 doctest、配置、自定义 | 9KB |
| 11 | 11-cache.md | 缓存：--lf、--ff、--cache-show、cache fixture | 10KB |
| 12 | 12-failures.md | 处理失败：--tb 样式、pdb 调试、断言重写 | 5KB |
| 13 | 13-output.md | 输出管理：-v、-q、--tb、--no-header、reporting | 28KB |
| 14 | 14-logging.md | 日志：caplog、log_cli、log_file、级别配置 | 9KB |
| 15 | 15-capture.md | 捕获 stdout/stderr：capsys、capfd、capture | 6KB |
| 16 | 16-warnings.md | 捕获警告：filterwarnings、recwarn、W 类 | 15KB |
| 17 | 17-skipping.md | Skip/xfail：skipif、xfail(strict)、reason | 11KB |
| 18 | 18-plugins.md | 使用插件：pip install、-p 加载、conftest | 5KB |
| 19 | 19-writing-plugins.md | 编写插件：hook、fixture、入口点、pytest11 | 14KB |
| 20 | 20-hooks.md | Hook 函数：collection、runtest、reporting | 13KB |
| 21 | 21-existing-suite.md | 集成现有测试套件 | 1KB |
| 22 | 22-unittest.md | unittest 兼容：TestCase、setUp/tearDown | 9KB |
| 23 | 23-xunit-setup.md | xunit 风格 setup：setup_method/teardown | 3KB |
| 24 | 24-bash-completion.md | Bash 自动补全：argcomplete | 1KB |
| **参考文档** | | | |
| 25 | 25-builtin-fixtures.md | 内置 fixtures 列表 | 10KB |
| 26a | 26a-api-functions.md | API：函数、常量、类（approx, raises, param 等） | 25KB |
| 26b | 26b-api-marks.md | API：Marks 参考（filterwarnings, parametrize 等） | 5KB |
| 26c | 26c-api-fixtures.md | API：Fixtures 参考（request, tmp_path, capsys 等） | 43KB |
| 26d | 26d-api-hooks.md | API：Hooks 参考（collection, runtest, reporting） | 45KB |
| 26e | 26e-api-objects.md | API：Objects（Config, Session, Item, Metafunc 等） | 47KB |
| 26f | 26f-api-exceptions.md | API：异常和警告类 | 26KB |
| 26g | 26g-api-cli.md | API：命令行标志完整参考 | 34KB |
| 27 | 27-fixtures-reference.md | Fixtures 详细参考 | 17KB |
| 28 | 28-configuration.md | 配置选项参考 | 8KB |
| 29 | 29-exit-codes.md | 退出码参考 | 1KB |
| **原理解释** | | | |
| 30 | 30-anatomy.md | 测试解剖：Arrange-Act-Assert-Cleanup | 2KB |
| 31 | 31-fixtures-explained.md | Fixtures 原理：scope、执行顺序、共享 | 6KB |
| 32 | 32-good-practices.md | 最佳实践：src layout、import mode、conftest | 11KB |
| 33 | 33-pythonpath.md | 导入机制：sys.path、PYTHONPATH、import mode | 7KB |
| 34 | 34-typing.md | 类型标注：pytest 类型支持 | 3KB |
| 35 | 35-ci-pipelines.md | CI 集成：GitHub Actions、GitLab CI | 2KB |
| 36 | 36-flaky-tests.md | Flaky 测试：根因分析、缓解策略 | 8KB |
| **示例与技巧** | | | |
| 37 | 37-basic-examples.md | 基础示例：fixtures、conftest、tmpdir | 30KB |
| 38 | 38-parametrize-examples.md | 参数化示例：间接参数化、自定义 ID | 22KB |
| 39 | 39-marker-examples.md | Marker 示例：自定义标记、注册、筛选 | 25KB |
| 40 | 40-session-fixtures.md | Session fixture 示例：查看所有测试 | 2KB |
| 41 | 41-collection-config.md | 收集配置：自定义发现规则 | 9KB |
| 42 | 42-reporting-demo.md | 报告演示：各种失败报告样式 | 26KB |
| 43 | 43-nonpython-tests.md | 非 Python 测试：conftest.py 收集机制 | 5KB |
| 44 | 44-custom-directory.md | 自定义目录收集器 | 4KB |

## 关键概念速查

| 概念 | 定义 | 详情文件 |
|------|------|----------|
| **Fixture** | 测试的依赖注入机制，通过函数参数请求，支持 scope/yield/params/autouse | 04, 25, 26c, 27, 31 |
| **Mark** | 测试元数据标记，如 skip/skipif/xfail/parametrize，可自定义 | 05, 17, 26b |
| **Parametrize** | 参数化测试，一个测试函数运行多组输入数据 | 06, 38 |
| **conftest.py** | 目录级配置文件，存放共享 fixtures 和 hooks，无需导入 | 04, 32, 37 |
| **Assertion rewrite** | pytest 重写 assert 语句，提供详细的失败信息 | 03, 12 |
| **Hook** | 插件扩展点，覆盖 pytest 的收集、运行、报告流程 | 20, 26d |
| **Monkeypatch** | 临时修改对象属性、字典项、环境变量，测试后自动恢复 | 09 |
| **Capture** | 捕获测试的 stdout/stderr 输出（capsys/capfd） | 15 |
| **tmp_path** | 每个测试唯一的临时目录，测试后自动清理 | 08 |
| **Scope** | Fixture 生命周期：function/class/module/session/package | 04, 26c, 31 |
| **xfail** | 标记预期失败的测试，失败不算测试失败 | 17 |
| **Plugin** | pytest 扩展，通过 conftest.py 或 pip 包提供 | 18, 19 |
| **Collection** | pytest 发现和收集测试的过程 | 26d, 41 |
| **Config** | pytest 配置系统：pyproject.toml/pytest.ini/setup.cfg/tox.ini | 28 |

## 速查表

### 常用命令
```bash
pytest                        # 运行所有测试
pytest test_file.py           # 运行指定文件
pytest test_file.py::test_fn  # 运行指定测试函数
pytest -v                     # 详细输出
pytest -x                     # 首次失败后停止
pytest -k "keyword"           # 按关键字筛选
pytest -m "mark"              # 按标记筛选
pytest --lf                   # 只运行上次失败的测试
pytest --ff                   # 先运行上次失败的测试
pytest -n auto                # 并行执行（需 pytest-xdist）
pytest --tb=short             # 简化回溯
pytest --co                   # 只收集不执行
pytest -s                     # 不捕获输出
```

### 常用 Fixture
```python
@pytest.fixture
def sample_data():              # 自定义 fixture
    return {"key": "value"}

@pytest.fixture(autouse=True)   # 自动使用
def setup_each(): ...

@pytest.fixture(scope="module") # 模块级共享
def db_connection(): ...

def test_example(sample_data):  # 通过参数请求 fixture
    assert sample_data["key"] == "value"
```

### 常用 Mark
```python
@pytest.mark.skip(reason="not implemented")
@pytest.mark.skipif(sys.platform == "win32", reason="Linux only")
@pytest.mark.xfail(reason="known bug")
@pytest.mark.parametrize("input,expected", [(1,2), (3,4)])
@pytest.mark.slow
def test_something(): ...
```

### 断言模式
```python
assert result == expected
assert "substring" in text
pytest.raises(ValueError, func, args)
pytest.warns(DeprecationWarning, func, args)
pytest.approx(3.14159, rel=1e-3)
```

### 配置文件 (pyproject.toml)
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-v --tb=short"
markers = ["slow: marks tests as slow"]
```
