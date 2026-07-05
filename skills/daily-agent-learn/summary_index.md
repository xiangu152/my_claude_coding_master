# Summary Index

> 按技术点分类索引已学习的 Agent 项目。每次学习后更新。

## Agent Memory（记忆与状态管理）

- **[Mem0](repo/mem0ai-mem0/)** — 通用记忆中间件。核心技巧：ADD-only 事实提取（不覆盖）、多信号混合检索（语义+BM25+实体加权自适应归一化）、工厂模式可插拔架构。适合给已有 Agent 加记忆。
- **[Letta](repo/letta-ai-letta/)** — 有状态 Agent 平台（原 MemGPT）。核心技巧：Block-based 上下文管理（类比 OS 内存）、三层记忆体系（Core/Recall/Archival）、Agent 自主记忆管理工具、增量记忆重建。适合从零构建有记忆的 Agent。
