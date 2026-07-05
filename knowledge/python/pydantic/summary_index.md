# Pydantic 知识库索引

> 版本：latest | 文档：https://pydantic.dev/docs/validation/latest/
> Pydantic 是 Python 数据验证库，使用类型注解进行数据验证和设置管理。

---

## 核心架构

```
BaseModel (核心抽象)
├── Fields (字段定义)
│   ├── Field() — 默认值、约束、别名、验证器
│   ├── field_validator / model_validator — 自定义验证
│   └── computed_field — 计算属性
├── Types (类型系统)
│   ├── 标准类型 (str, int, float, bool, list, dict...)
│   ├── 特殊类型 (Any, Optional, Union, Literal, Annotated...)
│   ├── Pydantic 类型 (EmailStr, HttpUrl, UUID, Secret...)
│   └── 自定义类型 (__get_pydantic_core_schema__)
├── Validation (验证)
│   ├── Strict vs Lax 模式
│   ├── 自定义验证器 (@field_validator, @model_validator)
│   └── validate_call 装饰器
├── Serialization (序列化)
│   ├── model_dump() → dict
│   ├── model_dump_json() → JSON string
│   ├── model_validate() ← dict
│   └── model_validate_json() ← JSON string
├── Config (配置)
│   ├── model_config = ConfigDict(...)
│   └── 全局配置 vs 字段级配置
└── JSON Schema
    ├── model_json_schema() → JSON Schema dict
    └── 支持 OpenAPI / JSON Schema 标准
```

## 数据模型层次

```
数据模型
├── BaseModel — 最常用，完整的验证/序列化/JSON Schema 支持
├── dataclass — 轻量级，兼容标准库 dataclass
├── TypedDict — 类型安全的字典
├── NamedTuple — 带类型的元组
└── RootModel — 单值根模型 (如 list[Item])
```

## 验证流程

```
输入数据
  ↓
类型转换 (lax mode) 或 类型检查 (strict mode)
  ↓
字段验证 (Field constraints: min_length, gt, pattern...)
  ↓
自定义验证器 (@field_validator, @model_validator)
  ↓
模型实例 (或 ValidationError)
```

## Settings 管理

```
BaseSettings (pydantic-settings)
├── 环境变量读取
├── .env 文件支持
├── 嵌套配置
└── 类型验证 + 默认值
```

---

## 文件索引

### Get Started
| 文件 | 内容 |
|------|------|
| `details/01-why.md` | 为什么使用 Pydantic (类型提示、性能、JSON Schema、生态系统) |
| `details/02-migration.md` | V1 → V2 迁移指南 (破坏性变更、API 变化) |

### Concepts (核心概念)
| 文件 | 内容 |
|------|------|
| `details/03-models.md` | Model 定义、继承、嵌套、方法、属性 |
| `details/04-fields.md` | Field 定义、默认值、约束、别名、验证器 |
| `details/05-json-schema.md` | JSON Schema 生成、自定义、嵌套模型 |
| `details/06-json.md` | JSON 解析、验证、序列化 |
| `details/07-types.md` | 支持的类型、标准库类型、Pydantic 类型 |
| `details/08-unions.md` | Union 类型、判别联合、类型分发 |
| `details/09-alias.md` | 字段别名、生成别名、验证别名 |
| `details/10-config.md` | ConfigDict 配置选项 |
| `details/11-serialization.md` | 序列化、model_dump、自定义序列化器 |
| `details/12-validators.md` | 字段验证器、模型验证器、验证装饰器 |
| `details/13-dataclasses.md` | Pydantic dataclass、标准库 dataclass 兼容 |
| `details/14-forward-annotations.md` | 前向引用、延迟注解 |
| `details/15-strict-mode.md` | 严格模式 vs 宽松模式 |
| `details/16-type-adapter.md` | TypeAdapter (非 BaseModel 类型验证) |
| `details/17-validation-decorator.md` | @validate_call 装饰器 |
| `details/18-conversion-table.md` | 类型转换规则表 |
| `details/19-settings.md` | Pydantic Settings (环境变量、.env 文件) |
| `details/20-performance.md` | 性能优化、Pydantic 编译缓存 |
| `details/21-experimental.md` | 实验性功能 |

### Errors (错误处理)
| 文件 | 内容 |
|------|------|
| `details/22-errors.md` | 错误概述、自定义错误 |
| `details/23-validation-errors.md` | ValidationError 详解、错误类型 |
| `details/24-usage-errors.md` | 使用错误、常见陷阱 |

### Examples (示例)
| 文件 | 内容 |
|------|------|
| `details/25-examples-files.md` | 文件处理示例 |
| `details/26-examples-requests.md` | HTTP 请求示例 |
| `details/27-examples-queues.md` | 队列处理示例 |
| `details/28-examples-orms.md` | ORM 集成示例 |
| `details/29-examples-custom-validators.md` | 自定义验证器示例 |
| `details/30-examples-dynamic-models.md` | 动态模型创建示例 |

### Internals (内部机制)
| 文件 | 内容 |
|------|------|
| `details/31-architecture.md` | Pydantic 内部架构 |
| `details/32-resolving-annotations.md` | 注解解析机制 |

---

## 关键概念速查

| 概念 | 说明 | 关联文件 |
|------|------|----------|
| BaseModel | 核心基类，所有数据模型的基础 | 03-models |
| Field | 字段定义，支持默认值、约束、别名 | 04-fields |
| ConfigDict | 模型配置，控制验证/序列化行为 | 10-config |
| field_validator | 字段级自定义验证器 | 12-validators |
| model_validator | 模型级自定义验证器 | 12-validators |
| validate_call | 函数参数验证装饰器 | 17-validation-decorator |
| TypeAdapter | 非 BaseModel 类型的验证/序列化 | 16-type-adapter |
| RootModel | 单值根模型 (如 `RootModel[list[Item]]`) | 03-models |
| model_dump | 序列化为 dict | 11-serialization |
| model_dump_json | 序列化为 JSON string | 11-serialization |
| model_validate | 从 dict 反序列化 | 06-json |
| model_validate_json | 从 JSON string 反序列化 | 06-json |
| model_json_schema | 生成 JSON Schema | 05-json-schema |
| ValidationError | 验证失败时抛出的异常 | 23-validation-errors |
| Strict mode | 严格模式，不做类型转换 | 15-strict-mode |
| Lax mode | 宽松模式 (默认)，自动类型转换 | 15-strict-mode |
| Annotated | 类型注解 + 元数据 (如 `Annotated[int, Field(gt=0)]`) | 04-fields |
| computed_field | 计算属性，序列化时自动生成 | 03-models |
| BaseSettings | 环境变量配置管理 (pydantic-settings) | 19-settings |
| Discriminated Union | 判别联合，通过字段值区分类型 | 08-unions |

---

## 常用模式速查

### 基本模型定义
```python
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str
    age: int = Field(gt=0, lt=150)
    email: str = Field(pattern=r'^[\w\.-]+@[\w\.-]+\.\w+$')
```

### 验证
```python
user = User(name='Alice', age=30, email='alice@example.com')
user_dict = user.model_dump()
user_json = user.model_dump_json()
```

### 自定义验证器
```python
from pydantic import field_validator, model_validator

class Order(BaseModel):
    items: list[str]
    total: float

    @field_validator('total')
    @classmethod
    def total_must_be_positive(cls, v):
        if v <= 0:
            raise ValueError('total must be positive')
        return v
```

### Settings
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str = 'sqlite:///db.sqlite3'
    debug: bool = False

    class Config:
        env_file = '.env'
```

### TypeAdapter (非 BaseModel)
```python
from pydantic import TypeAdapter

ta = TypeAdapter(list[int])
result = ta.validate_python(['1', '2', '3'])  # [1, 2, 3]
```
