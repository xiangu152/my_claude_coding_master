# My Claude Coding Master

一个为 Claude Code 打造的智能知识库系统，包含 10 个离线可用的框架文档、3 个可复用技能、和完整的开发工作流配置。

## 🎯 这是什么？

这是一个 Claude Code 的配置仓库，让 AI agent 在离线环境下也能完美理解各种框架和工具。所有文档都是从官方文档完整爬取的原始内容，不是摘要。

## 📦 知识库一览

| 知识库 | 文件数 | 大小 | 覆盖内容 |
|--------|--------|------|----------|
| **Python 编码规范** | 13 | 124KB | PEP 8 + Google Style，命名/布局/类型/异常/测试 |
| **Pydantic** | 32 | 596KB | 数据验证框架：Model/Field/Validator/Settings |
| **pytest** | 50 | 824KB | 测试框架：Fixture/Parametrize/Mark/Hook/Plugin |
| **AgentScope** | 16 | 300KB | Agent 框架：Agent/Message/Tool/Plan/RAG |
| **LangChain + LangGraph** | 38 | 640KB | LLM 应用框架：Agent/Tool/Memory/Graph/Workflow |
| **Claude Agent SDK** | 28 | 592KB | Claude Agent 开发：Tool/MCP/Subagent/Hook |
| **FastMCP** | 115 | 1.2MB | MCP 服务端/客户端：Tool/Resource/Prompt/Auth/App |
| **Redis** | 40 | 524KB | 内存数据库：数据类型/持久化/集群/Sentinel |
| **Git (Pro Git)** | 13 | 812KB | 版本控制：分支/合并/变基/内部原理 |
| **Docker** | 58 | 2.5MB | 容器平台：CLI/Dockerfile/Compose/网络/存储/安全/AI |

**总计**: 403 个详情文件，~6.7MB 原始文档

## 🏗️ 目录结构

```
~/.claude/
├── CLAUDE.md                          ← 核心路由文件（自动加载）
├── knowledge/                         ← 知识库目录
│   ├── python/                        ← Python 生态
│   │   ├── summary_index.md           ← Python 编码规范索引
│   │   ├── details/                   ← 编码规范详情 (13 文件)
│   │   ├── pydantic/                  ← Pydantic 知识库
│   │   └── pytest/                    ← pytest 知识库
│   ├── agent/                         ← Agent 框架
│   │   ├── agentscope/                ← AgentScope 知识库
│   │   ├── langchain/                 ← LangChain 知识库
│   │   ├── claude-agent-sdk/          ← Claude Agent SDK 知识库
│   │   └── mcp/fastmcp/               ← FastMCP 知识库
│   ├── db/redis/                      ← Redis 知识库
│   ├── deployment/docker/             ← Docker 知识库
│   └── shared/git/                    ← Git 知识库
├── skills/                            ← 技能目录
│   └── omc-learned/
│       ├── build-coding-standards.md  ← 构建编码规范技能
│       ├── build-framework-docs.md    ← 构建框架文档技能
│       └── code-review-expert/        ← 代码审查专家技能
└── projects/-Users-xiangu/memory/     ← 长期记忆
    └── claude-directory-structure.md  ← .claude 目录参考
```

## 🚀 快速开始

### 1. 克隆到本地

```bash
git clone https://github.com/xiangu152/my_claude_coding_master.git ~/.claude
```

### 2. 验证安装

```bash
# 检查知识库文件
ls ~/.claude/knowledge/

# 检查 CLAUDE.md 路由
grep "## " ~/.claude/CLAUDE.md
```

### 3. 开始使用

启动 Claude Code 后，它会自动读取 `CLAUDE.md` 并根据你的工作内容路由到对应知识库：

```
你: "帮我写一个 Pydantic model"
Claude: [自动读取 pydantic 知识库] → 提供完整 API 用法

你: "写个 pytest fixture"
Claude: [自动读取 pytest 知识库] → 提供 fixture 最佳实践

你: "配置 Docker Compose"
Claude: [自动读取 Docker 知识库] → 提供完整配置示例
```

## 📚 知识库层级结构

每个知识库采用三层抽象：

```
CLAUDE.md                    ← 第一层：路由（哪个项目用哪个知识库）
  └── summary_index.md       ← 第二层：索引（架构图 + 文件索引 + 概念速查）
      └── details/*.md       ← 第三层：详情（完整原始文档）
```

### summary_index.md 包含

- **架构图**: ASCII 树形图展示框架核心组件关系
- **文件索引**: 表格列出每个详情文件的主题和大小
- **概念速查**: 关键术语定义和交叉引用
- **速查表**: 常用命令/配置/模式的代码示例

### details/*.md 包含

- **YAML frontmatter**: title/source/version 元数据
- **完整原始文档**: 从官方文档爬取的全部内容
- **代码示例**: 保留所有原始代码块
- **无导航残留**: 已清理网站 UI 元素

## 🔧 自定义技能

### build-coding-standards

构建任意语言/框架的编码规范知识库：

```
/build-coding-standards
# 然后指定语言和参考来源
```

### build-framework-docs

构建框架文档知识库：

```
/build-framework-docs
# 然后指定框架文档 URL
```

### code-review-expert

结构化代码审查：

```
/code-review-expert
# 自动审查当前 git 变更，按 P0-P3 分级
```

## 🔄 更新知识库

### 添加新知识库

```bash
# 1. 使用技能构建
/build-framework-docs

# 2. 提交变更
cd ~/.claude
git add knowledge/new-framework/
git commit -m "feat: add new-framework knowledge base"
git push
```

### 同步到其他机器

```bash
# 拉取最新
cd ~/.claude
git pull
```

## 📋 CLAUDE.md 路由配置

`CLAUDE.md` 是核心路由文件，定义了不同项目类型使用哪些知识库：

```markdown
## Python Projects
When working on **any Python project**, read these files:
1. rules/code-python-style.md
2. knowledge/python/summary_index.md
3. knowledge/python/details/*.md

## pytest (Testing)
When writing **tests using pytest**, read these files:
1. knowledge/python/pytest/summary_index.md
2. knowledge/python/pytest/details/*.md

## Docker (Container Platform)
When working with **Docker**, read these files:
1. knowledge/deployment/docker/summary_index.md
2. knowledge/deployment/docker/details/*.md
```

## 🛡️ 离线可用

所有知识库都是离线可用的：

- ✅ 完整原始文档（非摘要）
- ✅ 代码示例保留
- ✅ 无外部链接依赖
- ✅ YAML 元数据便于索引
- ✅ 分层摘要快速定位

## 📊 验证状态

所有知识库都通过了 Architect + Critic 双层验证：

| 知识库 | Architect | Critic | 状态 |
|--------|-----------|--------|------|
| Python 编码规范 | PASS 7/7 | PASS 5/5 | ✅ |
| Pydantic | PASS 7/7 | PASS 5/5 | ✅ |
| pytest | PASS 7/7 | PASS 5/5 | ✅ |
| AgentScope | PASS 7/7 | PASS 5/5 | ✅ |
| LangChain | PASS 7/7 | PASS 5/5 | ✅ |
| Claude Agent SDK | PASS 7/7 | PASS 5/5 | ✅ |
| FastMCP | PASS 7/7 | PASS 5/5 | ✅ |
| Redis | PASS 7/7 | PASS 5/5 | ✅ |
| Git | PASS 7/7 | PASS 5/5 | ✅ |
| Docker | PASS 7/7 | PASS 5/5 | ✅ |

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

### 添加新知识库

1. 使用 `/build-framework-docs` 技能
2. 确保通过 Architect + Critic 验证
3. 更新 CLAUDE.md 路由
4. 提交 PR

### 改进现有知识库

1. 编辑详情文件
2. 确保 YAML frontmatter 完整
3. 验证代码围栏完整
4. 提交 PR

## 📄 许可证

MIT License

## 🔗 相关链接

- [Claude Code 文档](https://code.claude.com/docs)
- [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview)
- [FastMCP 文档](https://fastmcp.wiki)
- [Pro Git 中文版](https://git-scm.com/book/zh/v2)
