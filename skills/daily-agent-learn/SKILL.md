---
name: daily-agent-learn
description: "Daily GitHub project learning: search high-star repos by topic, clone, deep-init AGENTS.md, and teach via teacher mode"
tags: [learning, github, agent, daily, teacher]
triggers: ["daily-agent-learn", "daily learn", "agent learn", "teach me"]
---

# Daily Agent Learn

每日从 GitHub 搜索高星项目，通过 deep-init 建立项目理解，生成学习教材，并以教师模式讲解。

## Skill Root

```
$SKILL_ROOT = ~/.claude/skills/daily-agent-learn/
├── SKILL.md          # 本文件
├── repos.json        # 项目记录（含学习状态）
├── summary_index.md  # 技术点 → 项目索引
└── repo/             # clone 的项目目录
    └── owner-repo/
        ├── AGENTS.md      # deep-init 生成的项目全景图
        ├── LEARNING.md    # 学习教材
        └── ... (源码)
```

## Modes

### 学习模式（默认）
完整执行 Step 1-8：搜索 → clone → deep-init → 分析 → 教材 → 记录 → 汇报。

### 教师模式（`teach me`）
进入教师模式，基于已有的 AGENTS.md 和 LEARNING.md 进行互动教学。

---

## 学习模式 Workflow

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

排除已在 `repos.json` 中且 `studied: true` 的项目。`studied: false` 的项目可以重新学习。

### Step 3: Clone 项目

```bash
cd $SKILL_ROOT/repo
git clone --depth 1 https://github.com/{owner}/{repo}.git {owner}-{repo}
```

### Step 4: Deep-Init（分层生成 AGENTS.md）

对每个项目执行 deep-init 流程，**从根目录到每个子目录都生成 AGENTS.md**，形成自顶向下的知识索引。

#### 4.1 根目录 AGENTS.md

读取 README、pyproject.toml/package.json、入口文件，生成**项目级全景图**：

```markdown
# AGENTS.md — {项目名} 项目全景图

## 一句话定位
## 技术栈
## 目录结构（tree 输出，附每层说明）
## 核心模块表（模块 | 文件 | 职责）
## 架构设计（ASCII 数据流图）
## 关键设计模式
## 入口与启动
## 扩展点
## 注意事项
```

#### 4.2 子目录 AGENTS.md（逐层）

对每个有实质内容的子目录，读取其中的文件，生成**模块级文档**：

```markdown
# AGENTS.md — {目录名} 模块

## 职责
{这个目录/模块负责什么，一句话概括}

## 文件清单
| 文件 | 职责 | 关键类/函数 |
|------|------|------------|
| main.py | 核心逻辑 | Memory, AsyncMemory |
| base.py | 抽象基类 | MemoryBase |

## 核心抽象
{本模块的关键类/接口，附代码片段}

## 数据流
{输入什么 → 做什么 → 输出什么}

## 与其他模块的关系
{依赖谁，被谁依赖}

## 扩展指南
{如何扩展本模块，新增适配器/实现}
```

#### 4.3 执行顺序

1. 扫描完整目录树，确定需要生成 AGENTS.md 的目录列表
2. **先读叶子目录**（最底层模块），理解每个模块的职责
3. **逐层向上**，父目录的 AGENTS.md 引用子目录的理解
4. **最后生成根目录**，汇总所有子模块的理解

#### 4.4 目录选择标准

需要生成 AGENTS.md 的目录：
- 包含 `.py` / `.ts` / `.rs` 等源码文件的目录
- 包含 2 个以上源码文件的目录
- 跳过：tests/, docs/, scripts/, assets/, __pycache__/

### Step 5: 深度阅读与分析

基于 AGENTS.md 进一步深入：
1. 对照 AGENTS.md 验证理解是否准确
2. 读 AGENTS.md 中标记的核心模块的实现细节
3. 识别 AGENTS.md 中未覆盖但值得学习的模式
4. 补充 AGENTS.md 中缺失的关键信息

### Step 6: 生成学习教材

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

### Step 7: 更新记录

**更新 `repos.json`**：
```json
{
  "repos": [
    {
      "owner": "mem0ai",
      "repo": "mem0",
      "url": "https://github.com/mem0ai/mem0",
      "stars": 60096,
      "topic": "Agent Memory",
      "learned_at": "2025-07-05",
      "path": "repo/mem0ai-mem0",
      "studied": true,
      "has_agents_md": true,
      "has_learning_md": true
    }
  ]
}
```

字段说明：
- `studied`: 是否已学过（用户已通过教材或教师模式学习）
- `has_agents_md`: 是否已生成 AGENTS.md
- `has_learning_md`: 是否已生成 LEARNING.md

**更新 `summary_index.md`**：按技术点分类，列出对应已学习的项目和关键收获。

### Step 8: 汇报

向用户总结：
- 今天学了哪两个项目
- 每个项目的核心收获（3-5 点）
- AGENTS.md 和 LEARNING.md 文件位置
- 下次可以继续深入的方向
- 提示：可以用 `/daily-agent-learn teach me` 进入教师模式深入学习

---

## 教师模式 Workflow

当用户触发教师模式（说 "teach me" 或选择教师模式）时：

### Step 1: 选择要学习的项目

读取 `repos.json`，列出所有项目，按 `studied` 状态分组：

```
已学过（可复习）：
  ✅ mem0ai/mem0 (60k ⭐) — Agent Memory
  ✅ letta-ai/letta (23.6k ⭐) — Agent Memory

未学过：
  ⬜ owner/repo (Xk ⭐) — Topic
```

让用户选择要学习/复习的项目。

### Step 2: 加载项目知识

读取选中项目的：
1. `AGENTS.md` — 项目全景图
2. `LEARNING.md` — 学习教材
3. 必要时读取源码关键片段补充讲解

### Step 3: 教学计划

基于 AGENTS.md 自动生成教学大纲：

```
📚 {项目名} 教学大纲

1. 项目是什么？解决什么问题？（5 min）
2. 核心架构长什么样？（10 min）
3. 关键设计模式详解（15 min）
4. 核心代码走读（15 min）
5. 动手实践（10 min）

预计总时长：~55 min
```

### Step 4: 互动教学

**逐章讲解**，每章结束后：
1. 总结本章要点（3-5 条 bullet）
2. 提出 1-2 个思考问题检验理解
3. 等待用户确认后进入下一章

讲解风格：
- **由浅入深**：先给直觉，再给细节
- **类比优先**：用已知概念类比未知概念（如 "Block 就像操作系统的内存页"）
- **代码驱动**：每个概念都附代码片段，边读边讲
- **对比学习**：与同类项目对比，突出差异
- **问题引导**：通过提问引导思考，而非直接灌输

### Step 5: 学完确认

每章结束后询问：
- "这部分清楚了吗？"
- "有什么疑问？"
- "想深入某个点还是继续？"

### Step 6: 学完标记

当用户完成教学（或主动结束）：
1. 将 `repos.json` 中该项目的 `studied` 设为 `true`
2. 更新 `learned_at` 为当前日期
3. 在 `summary_index.md` 中补充教学中的新发现

### Step 7: 教学总结

```
🎓 学习完成！

项目：{项目名}
本次收获：
- {收获1}
- {收获2}
- {收获3}

建议下一步：
- {动手练习建议}
- {可深入的源码模块}
- {相关项目推荐}
```
