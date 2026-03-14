# rapidbuilder-server

低代码平台的后端服务，目标是提供 **数据库能力 + API 热加载能力**，并通过自托管方式获得可控、稳定、易扩展的基础设施。

### 架构目标

- **数据库**: 使用自托管 Supabase（Postgres + Auth + Storage 等能力）。
- **API 热加载**: 使用 Hono 构建 API 服务，支持快速开发与热更新（本地开发时使用热重载，服务器端通过无停机部署实现“热”效果）。
- **部署方式**: 全部服务通过 Docker 部署，由 Coolify 统一管理。
- **运维能力**:
  - 自动 HTTPS（证书申请与续期）
  - 容器级自动重启
  - 自动部署（Git 推送或手动触发）

### 云服务器整体结构

在一台云服务器上规划如下结构：

- 安装 Docker（所有服务以容器形式运行）
- 安装 Coolify（作为图形化部署中心）
  - 在 Coolify 中部署 **Supabase（自托管）**
  - 在 Coolify 中部署 **Hono API 服务（rapidbuilder-server）**
  - （可选）在 Coolify 中部署 **前端应用**
- 通过 Coolify 为所有项目启用：
  - 自动 HTTPS
  - 健康检查与自动重启
  - 与 Git 仓库的自动部署流水线

### 后端职责划分

- **Supabase**
  - 提供 Postgres 数据库
  - 管理用户认证 / 权限（如需）
  - 提供存储、Edge Functions（可选）
- **Hono API（本仓库服务）**
  - 封装业务 API，为低代码引擎提供统一接口
  - 根据数据库结构和配置，支持 API 的快速迭代与“热加载式”无停机发布

### 部署流程（概要）

1. 在云服务器上安装 Docker 与 Docker Compose。
2. 安装并配置 Coolify，绑定服务器域名与 DNS。
3. 在 Coolify 内创建 Supabase 项目（使用官方自托管模板或自定义 compose）。
4. 在 Coolify 内创建 Hono API 项目：
   - 连接到本仓库的 Git 仓库
   - 配置构建命令与启动命令
   - 配置环境变量（数据库连接、鉴权密钥等）
5. （可选）在 Coolify 内创建前端项目，连接到低代码前端仓库。
6. 为各项目开启：
   - 自动部署（Git push 触发）
   - 自动 HTTPS
   - 健康检查和自动重启策略

后续可以在此 `README` 中继续补充具体的环境变量说明、API 约定、以及本地开发指南。