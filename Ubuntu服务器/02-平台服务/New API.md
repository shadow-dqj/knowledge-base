---
title: New API
type: service
tags: [new-api, ai, gateway, 1panel]
updated: 2026-08-15
server: 192.168.1.117
service: new-api
status: healthy
---

# New API

[[Ubuntu服务器/首页|← 运维中心]] · [[平台服务索引]] · [[Ubuntu服务器/01-基础设施/1Panel]] · [[Ubuntu服务器/03-运维手册/备份与升级]]

> [!check] 当前状态
> 2026-08-15 已从 `v0.13.2` 在线更新到 `v1.0.0-rc.24`，容器 healthy，
> `/api/status` 返回 `success=true`。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| LAN 入口 | `http://192.168.1.117:3000` |
| Compose 项目 | `new-api` |
| Compose 目录 | `/opt/1panel/apps/new-api/new-api` |
| 容器 | `1Panel-new-api-Mokn` |
| 镜像 | `calciumion/new-api:latest` |
| 拉取策略 | `pull_policy: always` |
| 数据 | `./data:/data`，当前使用 SQLite |
| 日志 | `./logs:/app/logs` |
| 更新脚本 | `./update.sh`，权限 `755` |

## 在线更新

```bash
cd /opt/1panel/apps/new-api/new-api
./update.sh
```

更新脚本执行：

1. `docker compose config --quiet`
2. 在线拉取 `latest`
3. 重建 New API 服务并保留绑定目录

> [!info] 本次备份
> 服务尚未投入使用，按维护要求，本次从 `v0.13.2` 更新前未创建数据备份。
> 投入使用后，任何更新前都应备份 `data/`、Compose 和 `.env`。

## 验证

```bash
docker inspect 1Panel-new-api-Mokn \
  --format 'image={{.Config.Image}} health={{.State.Health.Status}}'

curl -fsS http://192.168.1.117:3000/api/status
```

预期：

- 镜像名为 `calciumion/new-api:latest`
- Docker health 为 `healthy`
- API 返回 `success=true`
- API 版本与镜像标签一致

## 网络提示

端口 3000 当前绑定所有 IPv4/IPv6 接口。服务器目前位于局域网且 UFW
规则未启用；如后续增加公网路由、端口映射或云防火墙规则，应先将绑定
收紧到 `192.168.1.117` 或限制来源地址。

## 相关

[[Ubuntu服务器/03-运维手册/日常巡检]] · [[Ubuntu服务器/03-运维手册/端口与依赖矩阵]] · [[Ubuntu服务器/03-运维手册/启停与恢复]]
