---
name: daily-agent-learn
description: "Daily GitHub project learning: search high-star repos by topic, clone, and generate learning materials"
tags: [learning, github, agent, daily]
triggers: ["daily-agent-learn", "daily learn", "agent learn"]
---

# Daily Agent Learn

每日从 GitHub 搜索高星项目并生成学习教材。

## Skill Root

```
$SKILL_ROOT = ~/.claude/skills/daily-agent-learn/
├── SKILL.md          # 本文件
├── repos.json        # 已学习项目记录
├── summary_index.md  # 技术点 → 项目索引
└── repo/             # clone 的项目目录
    └── owner-repo/
```

## Workflow

### Step 1: 询问学习主题

向用户提问："今天想学习哪个方面的 AI Agent 技术？"

选项参考（但不限于）：
- Agent Framework（LangChain, CrewAI, AutoGen, etc.）
- MCP (Model Context Protocol)
- RAG (Retrieval Augmented Generation)
- Multi-Agent Orchestration
- Tool Use / Function Calling
- Memory & State Management
- Evaluation & Testing
- Prompt Engineering

### Step 2: 搜索 GitHub 高星项目

使用 GitHub MCP 工具搜索：
- `search_repositories` 搜索目标 topic
- 筛选条件：stars:>500, 按 stars 降序
- 选择 2 个优质项目（优先选官方/核心项目，避免 tutorial 类仓库）

排除已在 `repos.json` 中记录过的项目。

### Step 3: Clone 项目

```bash
cd $SKILL_ROOT/repo
git clone --depth 1 https://github.com/{owner}/{repo}.git {owner}-{repo}
```

### Step 4: 深度阅读与分析

对每个项目：
1. 读 README.md 理解项目定位
2. 分析目录结构（tree）
3. 读核心入口文件和关键模块
4. 理解架构设计和核心抽象
5. 识别关键设计模式和技巧

### Step 5: 生成学习教材

在每个项目目录下创建 `LEARNING.md`，格式：

```markdown
# {项目名} 学习笔记

## 一句话定位
## 核心架构
## 目录结构与模块职责
## 关键设计模式
## 核心代码解读（附代码片段）
## 值得学习的技巧
## 与其他项目的对比
## 实战练习建议
```

### Step 6: 更新记录

**更新 `repos.json`**：
```json
{
  "repos": [
    {
      "owner": "langchain-ai",
      "repo": "langchain",
      "url": "https://github.com/langchain-ai/langchain",
      "stars": 100000,
      "topic": "Agent Framework",
      "learned_at": "2025-07-04",
      "path": "repo/langchain-ai-langchain"
    }
  ]
}
```

**更新 `summary_index.md`**：按技术点分类，列出对应已学习的项目和关键收获。

### Step 7: 汇报

向用户总结：
- 今天学了哪两个项目
- 每个项目的核心收获（3-5 点）
- 教材文件位置
- 下次可以继续深入的方向
