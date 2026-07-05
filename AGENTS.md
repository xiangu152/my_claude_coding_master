# Agent 配置指南

本仓库为 Claude Code 提供了完整的 agent 配置，包括知识库、技能和工作流。

## 🤖 Agent 工作原理

```
用户请求
    ↓
CLAUDE.md (路由层)
    ↓
判断项目类型 → 选择对应知识库
    ↓
summary_index.md (索引层)
    ↓
快速定位概念/文件/模式
    ↓
details/*.md (详情层)
    ↓
提供完整原始文档和代码示例
```

## 📂 Agent 配置文件

### CLAUDE.md (核心路由)

位置: `~/.claude/CLAUDE.md`

作用: 定义不同项目类型使用哪些知识库

示例:
```markdown
## Python Projects
When working on **any Python project**, read these files:
1. rules/code-python-style.md
2. knowledge/python/summary_index.md
3. knowledge/python/details/*.md
```

### summary_index.md (知识索引)

位置: 每个知识库根目录

作用: 提供分层抽象，快速定位信息

包含:
- 架构图 (ASCII 树形图)
- 文件索引 (表格)
- 概念速查 (术语定义)
- 速查表 (常用命令/配置)

### details/*.md (详情文件)

位置: 每个知识库的 details/ 目录

作用: 存储完整原始文档

格式:
```yaml
---
title: "文档标题"
source: "https://..."
version: "latest"
---

# 文档标题

> 原始文档来源：URL

---

[完整原始内容]
```

## 🛠️ 可用技能

### build-coding-standards

用途: 构建编码规范知识库

触发: `/build-coding-standards` 或说"构建编码规范"

工作流:
1. 分析语言/框架特点
2. 参考现有规范 (PEP 8, Google Style 等)
3. 创建 rules/ 文件
4. 创建 knowledge/ 目录
5. 更新 CLAUDE.md

### build-framework-docs

用途: 构建框架文档知识库

触发: `/build-framework-docs` 或说"构建框架文档"

工作流:
1. 爬取官方文档
2. 清理导航残留
3. 添加 YAML frontmatter
4. 创建 summary_index.md
5. 更新 CLAUDE.md
6. Architect + Critic 验证

### code-review-expert

用途: 结构化代码审查

触发: `/code-review-expert` 或说"代码审查"

工作流:
1. 获取 git diff
2. SOLID 原则检查
3. 安全扫描
4. 性能分析
5. 输出 P0-P3 分级报告
6. 询问是否修复

## 📋 知识库列表

### Python 生态

| 知识库 | 路径 | 用途 |
|--------|------|------|
| Python 编码规范 | `knowledge/python/` | PEP 8 + Google Style |
| Pydantic | `knowledge/python/pydantic/` | 数据验证框架 |
| pytest | `knowledge/python/pytest/` | 测试框架 |

### Agent 框架

| 知识库 | 路径 | 用途 |
|--------|------|------|
| AgentScope | `knowledge/agent/agentscope/` | Agent 开发框架 |
| LangChain | `knowledge/agent/langchain/` | LLM 应用框架 |
| Claude Agent SDK | `knowledge/agent/claude-agent-sdk/` | Claude Agent 开发 |
| FastMCP | `knowledge/agent/mcp/fastmcp/` | MCP 服务端/客户端 |

### 数据库

| 知识库 | 路径 | 用途 |
|--------|------|------|
| Redis | `knowledge/db/redis/` | 内存数据库 |

### 部署

| 知识库 | 路径 | 用途 |
|--------|------|------|
| Docker | `knowledge/deployment/docker/` | 容器平台 |

### 版本控制

| 知识库 | 路径 | 用途 |
|--------|------|------|
| Git | `knowledge/shared/git/` | 版本控制 |

## 🔄 Agent 工作流示例

### 示例 1: Python 开发

```
用户: "帮我写一个 FastAPI 接口"

Agent:
1. 读取 CLAUDE.md → 识别为 Python 项目
2. 读取 knowledge/python/summary_index.md → 获取 Python 规范
3. 读取 knowledge/python/pydantic/summary_index.md → 获取数据验证
4. 生成符合规范的 FastAPI 代码
```

### 示例 2: 测试编写

```
用户: "写个 pytest 测试"

Agent:
1. 读取 CLAUDE.md → 识别为 pytest 项目
2. 读取 knowledge/python/pytest/summary_index.md → 获取 pytest 概念
3. 读取 details/04-fixtures.md → 获取 fixture 用法
4. 生成符合最佳实践的测试代码
```

### 示例 3: Docker 部署

```
用户: "写个 Dockerfile"

Agent:
1. 读取 CLAUDE.md → 识别为 Docker 项目
2. 读取 knowledge/deployment/docker/summary_index.md → 获取 Docker 概念
3. 读取 details/16-dockerfile-reference.md → 获取指令参考
4. 读取 details/35-build-best-practices.md → 获取最佳实践
5. 生成优化的 Dockerfile
```

### 示例 4: 代码审查

```
用户: "/code-review-expert"

Agent:
1. 执行 git diff 获取变更
2. 加载 SOLID 检查清单
3. 加载安全检查清单
4. 分析代码质量
5. 输出 P0-P3 分级报告
6. 询问是否修复
```

## 🧩 自定义 Agent

### 添加新知识库

1. 创建目录结构:
```
knowledge/category/framework/
├── summary_index.md
└── details/
    ├── 01-topic.md
    └── 02-topic.md
```

2. 编写 summary_index.md:
- 架构图
- 文件索引
- 概念速查
- 速查表

3. 创建详情文件:
- 添加 YAML frontmatter
- 包含完整原始内容
- 保留代码示例

4. 更新 CLAUDE.md:
```markdown
## Framework Name
When working with **Framework**, read these files:
1. knowledge/category/framework/summary_index.md
2. knowledge/category/framework/details/*.md
```

### 创建新技能

1. 创建 SKILL.md:
```markdown
---
name: skill-name
description: "技能描述"
---

# 技能名称

## 工作流
1. 步骤 1
2. 步骤 2
3. 步骤 3
```

2. 放置到 skills/ 目录

3. 测试触发词

## 🔍 Agent 调试

### 检查知识库加载

```bash
# 查看 CLAUDE.md 路由
grep "## " ~/.claude/CLAUDE.md

# 检查知识库文件
ls ~/.claude/knowledge/*/*/summary_index.md

# 验证 YAML frontmatter
head -5 ~/.claude/knowledge/python/pytest/details/01-getting-started.md
```

### 验证技能

```bash
# 列出已安装技能
ls ~/.claude/skills/omc-learned/

# 检查技能文件
cat ~/.claude/skills/omc-learned/code-review-expert/SKILL.md
```

### 查看记忆

```bash
# 查看长期记忆
ls ~/.claude/projects/-Users-xiangu/memory/

# 读取记忆内容
cat ~/.claude/projects/-Users-xiangu/memory/claude-directory-structure.md
```

## 📚 参考资源

- [Claude Code 文档](https://code.claude.com/docs)
- [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview)
- [FastMCP 文档](https://fastmcp.wiki)
- [Pro Git 中文版](https://git-scm.com/book/zh/v2)
- [Docker 文档](https://docs.docker.com/)
- [pytest 文档](https://docs.pytest.org/)
- [Pydantic 文档](https://docs.pydantic.dev/)
- [Redis 文档](https://redis.com.cn/documentation.html)

## 🤝 贡献指南

### 添加新知识库

1. Fork 本仓库
2. 创建知识库目录
3. 编写 summary_index.md
4. 创建详情文件
5. 更新 CLAUDE.md
6. 提交 PR

### 改进现有知识库

1. 编辑详情文件
2. 确保 YAML frontmatter 完整
3. 验证代码围栏完整
4. 提交 PR

### 创建新技能

1. 创建 SKILL.md
2. 编写工作流
3. 测试触发词
4. 提交 PR

## 📄 许可证

MIT License
