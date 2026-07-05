# Docker 知识库

> **版本**: latest | **来源**: docs.docker.com | **文件数**: 58 | **总大小**: ~2.5MB

## 文档层级结构

```
~/.claude/knowledge/deployment/docker/
├── summary_index.md              ← 本文件（分层抽象）
└── details/                      ← 完整原始文档（58 文件）
    ├── 01~04: 入门              ← 概念/容器/Dockerfile/服务
    ├── 05~15: CLI 参考          ← docker run/build/exec/ps/logs/compose/network/volume
    ├── 16: Dockerfile 参考      ← 完整 Dockerfile 指令参考
    ├── 17~24: Compose 参考      ← 服务/构建/网络/卷/部署/高级/密钥
    ├── 25~26: Engine            ← 安装/Daemon 配置
    ├── 27~28: 存储              ← 数据卷/绑定挂载
    ├── 29~31: 网络              ← 网络概览/Bridge/Overlay
    ├── 32: 安全                 ← 安全最佳实践
    ├── 33~35: 构建              ← 构建上下文/多阶段/最佳实践
    ├── 36~37: Desktop           ← Docker Desktop 安装
    ├── 38~39: Hub/Registry      ← Docker Hub/私有 Registry
    ├── 40: Compose 入门         ← Compose 快速开始
    ├── 41: 账户管理             ← Docker 账户
    ├── 42: 管理控制台           ← 组织/团队/公司管理
    ├── 43~47: AI 功能           ← Gordon/Sandboxes/Model Runner/MCP
    ├── 48: 计费                 ← 支付/发票/周期
    ├── 49: Build Cloud          ← 云端构建
    ├── 50: Hardened Images      ← 安全加固镜像
    ├── 51: 企业部署             ← Desktop 企业部署
    ├── 52: 扩展                 ← Desktop 扩展 SDK
    ├── 53: 指南                 ← 语言/框架/CI 集成指南
    ├── 54: Offload              ← 云端运行容器
    ├── 55: Scout                ← 镜像分析/策略评估
    ├── 56: 订阅                 ← 许可证/计划
    ├── 57: 发布生命周期         ← 版本支持策略
    └── 58: Testcontainers       ← 编程式容器测试
```

## 架构总览

```
Docker 容器平台
├── 核心概念
│   ├── Container（容器）      → 镜像的运行实例，隔离的进程空间
│   ├── Image（镜像）          → 只读模板，包含应用代码+依赖+配置
│   ├── Dockerfile             → 构建镜像的指令文件
│   ├── Volume（数据卷）       → 持久化存储，独立于容器生命周期
│   └── Network（网络）        → 容器间通信的虚拟网络
│
├── 构建系统
│   ├── Dockerfile 指令        → FROM/RUN/COPY/ADD/CMD/ENTRYPOINT/ENV/EXPOSE/WORKDIR
│   ├── 多阶段构建             → 多个 FROM，只复制需要的产物
│   ├── 构建上下文             → docker build 时发送给 daemon 的文件集
│   ├── .dockerignore          → 排除不需要的文件
│   ├── BuildKit               → 现代构建引擎（并行/缓存/密钥）
│   └── Build Cloud            → 云端加速构建
│
├── 运行时
│   ├── docker run             → 创建并启动容器
│   ├── docker exec            → 在运行中的容器内执行命令
│   ├── docker ps              → 列出容器
│   ├── docker logs            → 查看容器日志
│   ├── docker stop/rm         → 停止/删除容器
│   └── docker inspect         → 查看容器详细信息
│
├── 镜像管理
│   ├── docker build           → 从 Dockerfile 构建镜像
│   ├── docker pull/push       → 从 Registry 拉取/推送镜像
│   ├── docker image ls        → 列出本地镜像
│   ├── docker tag             → 给镜像打标签
│   └── docker image rm        → 删除镜像
│
├── Docker Compose
│   ├── compose.yml            → 定义多容器应用的服务/网络/卷
│   ├── docker compose up      → 启动所有服务
│   ├── docker compose down    → 停止并删除所有资源
│   ├── services               → 服务定义（image/build/ports/volumes/environment）
│   ├── networks               → 自定义网络
│   ├── volumes                → 命名卷
│   ├── depends_on             → 服务依赖
│   ├── secrets/configs        → 敏感数据/配置管理
│   └── deploy                 → 部署配置（replicas/resources/constraints）
│
├── 存储
│   ├── Volumes（数据卷）      → Docker 管理的持久存储（推荐）
│   ├── Bind Mounts（绑定挂载）→ 宿主机目录映射到容器
│   ├── tmpfs Mounts           → 内存中的临时文件系统
│   └── Named vs Anonymous     → 命名卷 vs 匿名卷
│
├── 网络
│   ├── Bridge（桥接网络）     → 默认网络，单主机容器通信
│   ├── Overlay（覆盖网络）    → 跨主机容器通信（Swarm）
│   ├── Host（主机网络）       → 容器直接使用宿主机网络栈
│   ├── None（无网络）         → 完全隔离
│   └── Macvlan                → 容器获得物理网络 IP
│
├── 安全
│   ├── 最小权限原则           → 非 root 用户运行容器
│   ├── 只读文件系统           → --read-only
│   ├── 资源限制               → --memory/--cpus
│   ├── 安全扫描               → docker scout
│   ├── Content Trust          → 镜像签名验证
│   ├── Seccomp/AppArmor       → 系统调用过滤
│   └── Hardened Images        → 安全加固镜像
│
├── AI 功能
│   ├── Gordon                 → AI 助手，分析代码生成 Dockerfile
│   ├── Sandboxes              → AI 编码代理隔离环境
│   ├── Model Runner           → 本地模型运行器
│   └── MCP Catalog            → MCP 服务器目录
│
├── 供应链安全
│   ├── Docker Hub             → 官方镜像仓库
│   ├── Docker Scout           → 镜像分析和策略评估
│   └── Hardened Images        → 安全加固镜像
│
├── 平台管理
│   ├── Admin Console          → 组织/团队/公司管理
│   ├── Accounts               → 账户管理
│   ├── Billing                → 计费/支付
│   └── Subscription           → 许可证/计划
│
├── 开发工具
│   ├── Docker Desktop         → GUI + CLI + 内置 K8s
│   ├── Extensions             → 扩展插件
│   ├── Build Cloud            → 云端加速构建
│   ├── Offload                → 云端运行容器
│   └── Testcontainers         → 编程式容器测试
│
└── 企业功能
    ├── Desktop 企业部署       → MSI/PKG/Intune 部署
    ├── SSO/SAML               → 单点登录
    └── SCIM                   → 用户同步
```

## 文件索引

| # | 文件名 | 主题 | 大小 |
|---|--------|------|------|
| **入门** | | | |
| 01 | 01-getting-started.md | Docker 入门 | 2KB |
| 02 | 02-concepts.md | Docker 核心概念 | 11KB |
| 03 | 03-building-images.md | 构建镜像 | 14KB |
| 04 | 04-running-containers.md | 运行容器 | 1KB |
| **CLI 参考** | | | |
| 05 | 05-cli-overview.md | Docker CLI 概览 | 19KB |
| 06 | 06-docker-run.md | docker run 完整参考 | 59KB |
| 07 | 07-docker-build.md | docker build 完整参考 | 39KB |
| 08 | 08-docker-exec.md | docker exec | 4KB |
| 09 | 09-docker-ps.md | docker ps | 20KB |
| 10 | 10-docker-logs.md | docker logs | 3KB |
| 11 | 11-docker-compose.md | docker compose | 13KB |
| 12 | 12-docker-pull-push.md | docker pull/push | 13KB |
| 13 | 13-docker-network.md | docker network | 1KB |
| 14 | 14-docker-volume.md | docker volume | 1KB |
| 15 | 15-docker-container.md | docker container/image | 3KB |
| **Dockerfile 参考** | | | |
| 16 | 16-dockerfile-reference.md | Dockerfile 完整参考 | 100KB |
| **Compose 参考** | | | |
| 17 | 17-compose-file.md | Compose 文件参考 | 2KB |
| 18 | 18-compose-services.md | Compose Services | 72KB |
| 19 | 19-compose-build.md | Compose Build | 18KB |
| 20 | 20-compose-networks.md | Compose Networks | 7KB |
| 21 | 21-compose-volumes.md | Compose Volumes | 6KB |
| 22 | 22-compose-deploy.md | Compose Deploy | 10KB |
| 23 | 23-compose-advanced.md | Compose 高级特性 | 10KB |
| 24 | 24-compose-secrets-configs.md | Compose Secrets/Configs | 5KB |
| **Engine** | | | |
| 25 | 25-engine-install.md | Docker Engine 安装 | 4KB |
| 26 | 26-engine-daemon.md | Docker Daemon 配置 | 57KB |
| **存储** | | | |
| 27 | 27-volumes.md | 数据卷 | 25KB |
| 28 | 28-bind-mounts.md | 绑定挂载 | 15KB |
| **网络** | | | |
| 29 | 29-networking.md | Docker 网络概览 | 12KB |
| 30 | 30-bridge-network.md | Bridge 网络 | 23KB |
| 31 | 31-overlay-network.md | Overlay 网络 | 13KB |
| **安全** | | | |
| 32 | 32-security.md | Docker 安全 | 14KB |
| **构建** | | | |
| 33 | 33-build-context.md | 构建上下文 | 22KB |
| 34 | 34-multi-stage.md | 多阶段构建 | 7KB |
| 35 | 35-build-best-practices.md | 构建最佳实践 | 29KB |
| **Desktop** | | | |
| 36 | 36-desktop.md | Docker Desktop | 2KB |
| 37 | 37-desktop-install.md | Desktop 安装 | 28KB |
| **Hub/Registry** | | | |
| 38 | 38-hub.md | Docker Hub | 15KB |
| 39 | 39-registry.md | Docker Registry | 24KB |
| **Compose 入门** | | | |
| 40 | 40-compose-getting-started.md | Compose 入门 | 20KB |
| **账户与管理** | | | |
| 41 | 41-accounts.md | Docker 账户管理 | 12KB |
| 42 | 42-admin.md | Docker 管理控制台 | 87KB |
| **AI 功能** | | | |
| 43 | 43-ai-overview.md | Docker AI 概览 | 2KB |
| 44 | 44-ai-gordon.md | Gordon AI 助手 | 268KB |
| 45 | 45-ai-sandboxes.md | AI 沙箱环境 | 268KB |
| 46 | 46-ai-model-runner.md | 模型运行器 | 268KB |
| 47 | 47-ai-mcp.md | MCP 目录和工具包 | 268KB |
| **计费与订阅** | | | |
| 48 | 48-billing.md | Docker 计费 | 22KB |
| 49 | 49-build-cloud.md | Docker Build Cloud | 35KB |
| 50 | 50-dhi.md | Docker Hardened Images | 90KB |
| 51 | 51-enterprise.md | Docker 企业部署 | 106KB |
| 52 | 52-extensions.md | Docker Desktop 扩展 | 91KB |
| 53 | 53-guides.md | Docker 指南 | 246KB |
| 54 | 54-offload.md | Docker Offload | 18KB |
| 55 | 55-scout.md | Docker Scout | 109KB |
| 56 | 56-subscription.md | Docker 订阅 | 18KB |
| 57 | 57-release-lifecycle.md | 发布生命周期 | 8KB |
| 58 | 58-testcontainers.md | Testcontainers | 4KB |

## 关键概念速查

| 概念 | 定义 | 详情文件 |
|------|------|----------|
| **Container** | 镜像的运行实例，隔离的进程空间 | 02, 06 |
| **Image** | 只读模板，包含应用代码+依赖+配置 | 03, 16 |
| **Dockerfile** | 构建镜像的指令文件 | 16 |
| **Volume** | 持久化存储，独立于容器生命周期 | 27 |
| **Bind Mount** | 宿主机目录映射到容器 | 28 |
| **Network** | 容器间通信的虚拟网络 | 29, 30, 31 |
| **Compose** | 多容器应用定义和管理 | 17, 18, 40 |
| **Registry** | 镜像存储和分发服务 | 39 |
| **Docker Hub** | Docker 官方镜像仓库 | 38 |
| **BuildKit** | 现代构建引擎 | 07, 33 |
| **Multi-stage** | 多阶段构建，减小镜像体积 | 34 |
| **Gordon** | Docker AI 助手 | 44 |
| **Scout** | 镜像分析和策略评估 | 55 |
| **Hardened Images** | 安全加固镜像 | 50 |
| **Build Cloud** | 云端加速构建 | 49 |
| **Offload** | 云端运行容器 | 54 |

## 速查表

### 常用命令
```bash
docker run -d -p 8080:80 nginx        # 后台运行，映射端口
docker exec -it container bash        # 进入容器
docker ps                              # 列出运行中的容器
docker ps -a                           # 列出所有容器
docker logs container                  # 查看日志
docker stop container                  # 停止容器
docker rm container                    # 删除容器
docker image ls                        # 列出镜像
docker image rm image                  # 删除镜像
docker system prune                    # 清理未使用资源
```

### Dockerfile 常用指令
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["python", "app.py"]
```

### Compose 文件
```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/app
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
    depends_on:
      - db
  db:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

### 网络操作
```bash
docker network ls                      # 列出网络
docker network create mynet            # 创建网络
docker network connect mynet container # 连接容器到网络
docker run --network mynet ...         # 指定网络运行
```

### 数据卷
```bash
docker volume create myvol             # 创建卷
docker volume ls                       # 列出卷
docker run -v myvol:/data ...          # 使用卷
docker run -v /host/path:/container ...# 绑定挂载
```
