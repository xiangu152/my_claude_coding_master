---
name: daily-arxiv-paper
description: "Daily arXiv paper reading: search latest AI papers, download, analyze, and generate reading notes"
tags: [arxiv, paper, research, daily, reading]
triggers: ["daily-arxiv-paper", "daily paper", "arxiv paper", "read paper"]
---

# Daily ArXiv Paper

每日从 arXiv 搜索最新 AI 论文，下载分析，生成阅读笔记。

## Skill Root

```
$SKILL_ROOT = ~/.claude/skills/daily-arxiv-paper/
├── SKILL.md          # 本文件
├── papers.json       # 论文阅读记录
├── summary_index.md  # 技术点 → 论文索引
└── papers/           # 下载的论文存储（由 arxiv-mcp-server 管理）
```

## Modes

### 学习模式（默认）
搜索 → 下载 → 分析 → 生成笔记 → 更新记录。

### 教师模式（`teach me`）
基于已下载的论文进行互动教学。

---

## 学习模式 Workflow

### Step 1: 询问研究方向

向用户提问："今天想看哪个方向的论文？"

选项参考（但不限于）：
- LLM Agent / Tool Use / Function Calling
- RAG (Retrieval Augmented Generation)
- Multi-Agent Systems
- Reasoning / Chain-of-Thought
- Fine-tuning / RLHF / DPO
- Vision-Language Models
- Code Generation
- Memory & Long-context

### Step 2: 搜索 arXiv 论文

使用 arxiv MCP 工具搜索：
- `search_papers` 搜索目标方向
- 筛选条件：最近 30 天，按日期排序
- 选择 2 篇高质量论文（优先选引用量高、机构知名的）

排除已在 `papers.json` 中且 `studied: true` 的论文。

### Step 3: 下载论文

使用 arxiv MCP 工具：
- `download_paper` 下载选定的论文
- `read_paper` 读取论文内容

### Step 4: 深度分析

对每篇论文：
1. 读摘要和结论，理解核心贡献
2. 分析方法论和实验设计
3. 识别关键创新点和技术细节
4. 评估实验结果的说服力
5. 思考与已有工作的区别

### Step 5: 生成阅读笔记

在 `$SKILL_ROOT/` 下创建 `{paper_id}.md`，格式：

```markdown
# {论文标题}

> arXiv: {paper_id} | {date}

## 一句话总结
{一句话概括论文核心贡献}

## 研究动机
{为什么做这个研究，解决什么问题}

## 核心方法
{技术方案，关键创新点}

## 实验设计
{数据集、基线、评估指标}

## 关键结果
{主要实验结果，附数据}

## 优势与局限
{论文的优点和不足}

## 与相关工作的对比
{与同类方法的区别}

## 启发与思考
{对自己工作的启发}

## 代码与资源
{代码链接、模型权重等}
```

### Step 6: 更新记录

**更新 `papers.json`**：
```json
{
  "papers": [
    {
      "paper_id": "2401.12345",
      "title": "Paper Title",
      "authors": ["Author 1", "Author 2"],
      "date": "2025-07-01",
      "categories": ["cs.AI", "cs.LG"],
      "topic": "LLM Agent",
      "studied_at": "2025-07-05",
      "studied": true,
      "path": "2401.12345.md"
    }
  ]
}
```

**更新 `summary_index.md`**：按技术点分类，列出对应论文和关键发现。

### Step 7: 汇报

向用户总结：
- 今天读了哪两篇论文
- 每篇论文的核心贡献（3-5 点）
- 笔记文件位置
- 可以继续深入的方向

---

## 教师模式 Workflow

当用户触发教师模式时：

### Step 1: 选择论文

读取 `papers.json`，列出所有论文，按 `studied` 状态分组：
```
已读过（可复习）：
  ✅ 2401.12345 — Paper Title (2025-07-01)

未读过：
  ⬜ 2402.67890 — Another Title (2025-07-03)
```

### Step 2: 加载论文内容

使用 `read_paper` 读取论文全文，加载对应的阅读笔记。

### Step 3: 生成教学大纲

```
📄 {论文标题} 教学大纲

1. 这篇论文在做什么？（5 min）
2. 为什么要做这个？研究动机（5 min）
3. 怎么做的？核心方法详解（15 min）
4. 做得怎么样？实验结果分析（10 min）
5. 启发与讨论（10 min）

预计总时长：~45 min
```

### Step 4: 互动教学

逐章讲解，每章结束后：
- 总结要点（3-5 条 bullet）
- 提出思考问题
- 等待用户确认后继续

讲解风格：
- **图解优先**：用 ASCII 图解释模型架构
- **公式直觉化**：先给直觉，再看公式
- **对比学习**：与已有方法对比
- **代码映射**：如果有代码，指出关键实现

### Step 5: 学完标记

将 `papers.json` 中该论文的 `studied` 设为 `true`，更新 `learned_at`。

### Step 6: 教学总结

```
🎓 论文学习完成！

论文：{title}
核心收获：
- {收获1}
- {收获2}
- {收获3}

建议下一步：
- 实现核心算法
- 阅读引用的关键文献
- 思考如何应用到自己的工作
```
