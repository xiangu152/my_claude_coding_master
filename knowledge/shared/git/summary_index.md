# Git 知识库 (Pro Git 中文版)

> **版本**: v2 | **来源**: git-scm.com/book/zh/v2 | **文件数**: 13 | **总大小**: ~457KB

## 文档层级结构

```
~/.claude/knowledge/shared/git/
├── summary_index.md              ← 本文件（分层抽象）
└── details/                      ← 完整原始文档（13 文件）
    ├── 01: 起步                  ← 版本控制/Git简史/安装/配置
    ├── 02: Git 基础              ← 仓库/提交/历史/撤销/远程/标签/别名
    ├── 03: Git 分支              ← 分支简介/新建合并/管理/工作流/远程分支/变基
    ├── 04: 服务器上的 Git        ← 协议/搭建/SSH/守护进程/HTTP/GitWeb/GitLab
    ├── 05: 分布式 Git            ← 工作流程/贡献/维护项目
    ├── 06: GitHub                ← 账户/贡献/维护/组织/脚本
    ├── 07: Git 工具              ← 修订版本/暂存/贮藏/签名/搜索/重写/重置/合并/Rerere/调试/子模块/打包/替换/凭证
    ├── 08: 自定义 Git            ← 配置/属性/钩子/强制策略
    ├── 09: Git 与其他系统        ← SVN/Hg/Perforce 客户端/迁移
    ├── 10: Git 内部原理          ← 对象/引用/包文件/引用规范/传输协议/维护/环境变量
    ├── 11: 附录 A                ← GUI/VS/VSCode/IntelliJ/Bash/Zsh/PowerShell
    ├── 12: 附录 B                ← Libgit2/JGit/go-git/Dulwich
    └── 13: 附录 C                ← Git 命令参考
```

## 架构总览

```
Git 版本控制系统
├── 核心概念
│   ├── 仓库 (Repository)    → .git 目录，存储所有版本数据
│   ├── 提交 (Commit)        → 项目快照，包含作者/时间/父提交
│   ├── 分支 (Branch)        → 指向提交的可移动指针
│   ├── 标签 (Tag)           → 指向提交的固定指针
│   ├── 工作区 (Working Dir) → 项目文件的当前状态
│   ├── 暂存区 (Staging)     → git add 后的待提交区域
│   └── 远程 (Remote)        → 其他仓库的连接（origin 等）
│
├── 基本操作
│   ├── git init / clone     → 创建/克隆仓库
│   ├── git add              → 暂存文件变更
│   ├── git commit           → 提交暂存区内容
│   ├── git status / diff    → 查看状态/差异
│   ├── git log              → 查看提交历史
│   ├── git remote           → 管理远程仓库
│   ├── git push / pull      → 推送/拉取
│   └── git tag              → 创建标签
│
├── 分支操作
│   ├── git branch           → 创建/列出/删除分支
│   ├── git checkout / switch→ 切换分支
│   ├── git merge            → 合并分支
│   ├── git rebase           → 变基（重放提交）
│   └── git cherry-pick      → 摘取特定提交
│
├── 高级工具
│   ├── git stash            → 暂存当前工作
│   ├── git reset            → 重置 HEAD/暂存区/工作区
│   ├── git revert           → 创建反向提交
│   ├── git reflog           → 引用日志
│   ├── git bisect           → 二分查找 bug
│   ├── git blame            → 逐行追溯
│   ├── git submodule        → 子模块管理
│   ├── git replace          → 对象替换
│   └── git rerere           → 重放记录的冲突解决
│
├── 内部原理
│   ├── Blob / Tree / Commit → Git 对象模型
│   ├── Refs                 → 分支/标签的引用
│   ├── Packfile             → 压缩存储
│   ├── Refspec              → 引用规范
│   ├── Transfer Protocol    → 传输协议
│   └── Plumbing vs Porcelain→ 底层命令 vs 上层命令
│
├── 服务器
│   ├── 协议                 → 本地/SSH/HTTP/Git
│   ├── 搭建                 → bare repo / SSH key / git-daemon
│   ├── GitLab               → 自托管平台
│   └── 第三方托管           → GitHub/GitLab/Bitbucket
│
├── 自定义
│   ├── 配置                 → git config (system/global/local)
│   ├── 属性                 → .gitattributes
│   └── 钩子                 → pre-commit/post-commit/pre-push 等
│
└── 与其他系统
    ├── SVN 客户端           → git svn
    ├── Hg/Perforce          → 兼容层
    └── 迁移                 → git filter-branch / git filter-repo
```

## 文件索引

| # | 文件名 | 主题 | 大小 |
|---|--------|------|------|
| 01 | 01-getting-started.md | 起步：版本控制、Git 简史、安装、配置、帮助 | 13KB |
| 02 | 02-basics.md | Git 基础：仓库、提交、历史、撤销、远程、标签、别名 | 38KB |
| 03 | 03-branching.md | Git 分支：简介、新建合并、管理、工作流、远程分支、变基 | 26KB |
| 04 | 04-server.md | 服务器上的 Git：协议、搭建、SSH、守护进程、HTTP、GitLab | 23KB |
| 05 | 05-distributed.md | 分布式 Git：工作流程、贡献、维护项目 | 33KB |
| 06 | 06-github.md | GitHub：账户、贡献、维护、组织、脚本 | 32KB |
| 07 | 07-tools.md | Git 工具：修订版本、暂存、贮藏、签名、搜索、重写、重置、合并、Rerere、调试、子模块、打包、替换、凭证 | 110KB |
| 08 | 08-customizing.md | 自定义 Git：配置、属性、钩子、强制策略 | 33KB |
| 09 | 09-other-systems.md | Git 与其他系统：SVN/Hg/Perforce 客户端、迁移 | 60KB |
| 10 | 10-internals.md | Git 内部原理：对象、引用、包文件、引用规范、传输、维护、环境变量 | 48KB |
| 11 | 11-appendix-a.md | 附录 A：GUI/VS/VSCode/IntelliJ/Bash/Zsh/PowerShell | 11KB |
| 12 | 12-appendix-b.md | 附录 B：Libgit2/JGit/go-git/Dulwich | 14KB |
| 13 | 13-appendix-c.md | 附录 C：Git 命令参考 | 15KB |

## 关键概念速查

| 概念 | 定义 | 详情文件 |
|------|------|----------|
| **Repository** | .git 目录，存储所有版本数据和元信息 | 02 |
| **Commit** | 项目的一个快照，包含树对象、父提交、作者信息 | 02, 10 |
| **Branch** | 指向某个提交的可移动指针 | 03 |
| **Tag** | 指向某个提交的固定指针（轻量/附注） | 02 |
| **Staging Area** | git add 后的待提交区域（index 文件） | 02 |
| **Remote** | 远程仓库连接（origin/upstream 等） | 02, 04 |
| **Merge** | 将两个分支的提交历史合并 | 03 |
| **Rebase** | 将提交序列重放到另一个基准提交之上 | 03 |
| **Stash** | 临时保存当前工作区和暂存区的变更 | 07 |
| **Reset** | 重置 HEAD、暂存区、工作区到指定状态 | 07 |
| **Revert** | 创建一个新提交来撤销某个提交的变更 | 07 |
| **Reflog** | 引用日志，记录 HEAD 和分支引用的变更历史 | 07 |
| **Submodule** | 将一个 Git 仓库作为另一个仓库的子目录 | 07 |
| **Hook** | 在特定 Git 操作前后触发的脚本 | 08 |
| **Git Object** | Blob(文件)、Tree(目录)、Commit(提交)、Tag(标签) | 10 |
| **Packfile** | Git 的压缩存储格式 | 10 |
| **Refspec** | 引用规范，定义本地和远程引用的映射 | 10 |

## 速查表

### 基本操作
```bash
git init                          # 初始化仓库
git clone <url>                   # 克隆远程仓库
git add <file>                    # 暂存文件
git commit -m "message"           # 提交
git status                        # 查看状态
git diff                          # 查看差异
git log --oneline --graph         # 查看历史
git remote add origin <url>       # 添加远程仓库
git push origin main              # 推送
git pull origin main              # 拉取
```

### 分支操作
```bash
git branch <name>                 # 创建分支
git checkout <branch>             # 切换分支
git switch <branch>               # 切换分支（新语法）
git merge <branch>                # 合并分支
git rebase <branch>               # 变基
git branch -d <branch>            # 删除分支
git cherry-pick <commit>          # 摘取提交
```

### 高级操作
```bash
git stash                         # 暂存当前工作
git stash pop                     # 恢复暂存
git reset --soft HEAD~1           # 撤销提交（保留暂存）
git reset --hard HEAD~1           # 撤销提交（丢弃所有）
git revert <commit>               # 创建反向提交
git reflog                        # 查看引用日志
git bisect start                  # 开始二分查找
git blame <file>                  # 逐行追溯
```

### 配置
```bash
git config --global user.name "Name"
git config --global user.email "email"
git config --global core.editor vim
git config --list                 # 查看所有配置
```
