---
title: MetaMCP
type: service
tags: [metamcp, mcp, postgres]
updated: 2026-08-13
server: 192.168.1.117
service: metamcp
status: healthy
---

# MetaMCP

[[Ubuntu服务器/首页|← 运维中心]] · [[Ubuntu服务器/01-基础设施/Docker与容器]] · [[Ubuntu服务器/03-运维手册/日常巡检]]

> [!check] 当前状态
> `metamcp` 与 `metamcp-pg` 均 healthy，RestartCount 为 0。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| Compose 目录 | `/home/shadowdu/services/metamcp` |
| App 镜像 | `ghcr.io/metatool-ai/metamcp:latest` |
| PostgreSQL | `postgres:16-alpine` |
| LAN 入口 | `http://192.168.1.117:12008` |
| PostgreSQL 发布 | 仅容器网络，不向 LAN 发布 |
| 数据卷 | `metamcp_postgres_data` |
| 更新脚本 | `./update.sh`，权限 `700` |
| 备份目录 | `/home/shadowdu/services/metamcp/backups` |

## 依赖与数据流

```text
Browser / MCP Client -> metamcp:12008 -> metamcp-pg:5432
```

## 验证

```bash
cd ~/services/metamcp
docker compose ps
docker inspect -f '{{.State.Health.Status}}' metamcp
docker inspect -f '{{.State.Health.Status}}' metamcp-pg
```

## 更新

```bash
cd ~/services/metamcp
./update.sh
```

> [!warning] 更新策略
> 该脚本先备份 PostgreSQL，再拉取 `latest` 并重建。更新后同时验证 App、PostgreSQL 和 12008 入口。

## 维护记录

- 当前配置文件和 `.env` 权限均为 `600`。
- 管理凭据不在 mall4cloud [[Ubuntu服务器/_敏感/中间件凭据清单|凭据清单]]范围内。