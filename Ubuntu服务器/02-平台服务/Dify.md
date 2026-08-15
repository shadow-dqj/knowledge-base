---
title: Dify
type: service
tags: [dify, ai, workflow, rag, docker]
updated: 2026-08-15
server: 192.168.1.117
service: dify
status: healthy
---

# Dify

[[Ubuntu服务器/首页|← 运维中心]] · [[平台服务索引]] · [[Ubuntu服务器/01-基础设施/Docker与容器]] · [[Ubuntu服务器/03-运维手册/备份与升级]]

> [!check] 当前状态
> 官方 Community `1.16.1` 已部署。15 个常驻服务运行，API healthy；
> `init_permissions` 以状态码 `0` 正常退出。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| LAN 入口 | `http://192.168.1.117:12009` |
| Compose 项目 | `dify` |
| 部署目录 | `/home/shadowdu/services/dify` |
| 主配置 | `docker-compose.yaml` |
| 端口覆盖 | `docker-compose.override.yaml`，权限 `600` |
| 环境变量 | `.env`，权限 `600` |
| 数据目录 | `volumes/` |
| 安装版本 | `INSTALLED_RELEASE`：`1.16.1` |
| 镜像刷新 | `update-images.sh`，权限 `750` |

`1.16.1` 是 2026-08-15 安装时的官方最新稳定版。API、Web、Agent、
Plugin、Sandbox 和中间件镜像使用官方 Compose 兼容矩阵，不单独追踪
不匹配的 `latest`。

## 架构

```text
Browser :12009
  -> Dify Nginx
     -> Web
     -> API / WebSocket / Worker
        -> PostgreSQL
        -> Redis
        -> Weaviate
        -> Plugin Daemon
        -> Agent Backend -> Local Sandbox
        -> Sandbox / SSRF Proxy
```

主要镜像：

| 组件 | 镜像 |
| --- | --- |
| API、Worker、WebSocket | `langgenius/dify-api:1.16.1` |
| Web | `langgenius/dify-web:1.16.1` |
| Agent Backend | `langgenius/dify-agent-backend:1.16.1` |
| Agent Local Sandbox | `langgenius/dify-agent-local-sandbox:1.16.1` |
| Plugin Daemon | `langgenius/dify-plugin-daemon:0.6.3-local` |
| Sandbox | `langgenius/dify-sandbox:0.2.15` |
| PostgreSQL | `postgres:15-alpine` |
| Redis | `redis:6-alpine` |
| Weaviate | `semitechnologies/weaviate:1.27.0` |

## 网络与安全

| 发布端口 | 绑定 | 用途 |
| ---: | --- | --- |
| 12009 | `192.168.1.117` | HTTP/UI/API |
| 12010 | `127.0.0.1` | HTTPS 预留 |
| 5003 | `127.0.0.1` | Plugin Daemon 调试 |

PostgreSQL、Redis、Weaviate、API、Agent 和 Sandbox 不向 LAN 发布端口。
Compose 使用独立 SSRF、Agent Sandbox 和 Local Sandbox Proxy 隔离网络。

安装时已随机生成并持久化数据库密码、Dify Secret、Weaviate Key、
Sandbox Key、Plugin 内部 Key，以及 Agent API/Server/Shell Token。
实际值只保存在服务器 `.env` 中，不写入 Vault。

## 首次初始化

当前检查：

```json
{"step":"not_started","setup_at":null}
```

说明容器和数据库已就绪，但尚未通过 Web 页面创建首个管理员账号。打开：

```text
http://192.168.1.117:12009
```

## 日常管理

```bash
cd /home/shadowdu/services/dify

docker compose ps -a
docker compose logs --tail=100
docker compose logs -f api worker
docker compose up -d --pull never --remove-orphans
```

正常状态：

- API、PostgreSQL、Redis、Sandbox、Local Sandbox 为 `healthy`
- Agent Backend 为 `running`，RestartCount 不增长
- `init_permissions` 为 `Exited (0)`
- 其他 15 个服务运行

## 镜像与版本更新

服务器直连 Docker Hub 较慢。刷新当前兼容矩阵镜像：

```bash
cd /home/shadowdu/services/dify
./update-images.sh
```

脚本通过 `docker.m.daocloud.io` 拉取相同官方镜像，重新标记为官方镜像名，
再应用 Compose。它不执行 Dify 跨版本升级。

跨版本升级必须：

1. 阅读官方发布说明、环境变量变化和数据库迁移要求。
2. 保留 `.env`、`volumes/` 和 `docker-compose.override.yaml`。
3. 同步更新完整官方 `docker/` 目录。
4. 校验 Compose 后刷新镜像并执行完整巡检。

## 验证

```bash
cd /home/shadowdu/services/dify
docker compose config --quiet
docker compose ps -a

curl -LsS -o /dev/null -w '%{http_code}\n' \
  http://192.168.1.117:12009/
curl -fsS http://192.168.1.117:12009/console/api/setup
curl -fsS http://192.168.1.117:12009/console/api/system-features
```

如 API 容器重建后 Nginx 暂时缓存旧容器地址并返回 `502`：

```bash
docker compose restart nginx
```

首次冷启动需要从机械盘读取较大的 Python 镜像，I/O wait 可能短时升高，
缓存预热后会逐步下降。

## 相关

[[Ubuntu服务器/03-运维手册/日常巡检]] · [[Ubuntu服务器/03-运维手册/端口与依赖矩阵]] · [[Ubuntu服务器/03-运维手册/启停与恢复]]
