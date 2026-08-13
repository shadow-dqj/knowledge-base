---
tags:
  - docker
  - 1panel
  - metamcp
  - 运维
updated: 2026-08-13
server: 192.168.1.117
---

# Docker 与管理平台

[[首页|返回首页]] · 相关：[[服务器与存储]] · [[mall4cloud中间件]] · [[安全与备份]]

> 最后核验：2026-08-13

## Docker

| 组件 | 版本或配置 |
| --- | --- |
| Docker Engine | `29.7.2` |
| Docker Compose | `5.4.0` |
| Docker Buildx | `0.36.1` |
| Docker Root Dir | `/mnt/docker-data/docker` |
| containerd Root | `/mnt/docker-data/containerd` |
| 镜像加速 | `https://docker.1panel.live/` |
| 普通用户权限 | `shadowdu` 可直接执行 Docker |
| 服务状态 | Docker/containerd 均 `active/enabled` |

当前 Compose 项目：

| 项目 | 容器数 | 配置文件 |
| --- | ---: | --- |
| `mall4cloud-middleware` | 13 | `/home/shadowdu/services/mall4cloud-middleware/compose.yaml` |
| `metamcp` | 2 | `/home/shadowdu/services/metamcp/compose.yaml` |

## 1Panel

| 项目 | 当前值 |
| --- | --- |
| 版本 | `v2.2.5 stable` |
| Core/Agent | 运行中、开机自启 |
| 监听地址 | `192.168.1.117:31788` |
| 授权网段 | `192.168.1.0/24` |
| IPv6 面板监听 | 关闭 |
| 凭据文件 | `/home/shadowdu/.config/1panel/admin-credentials.txt`，权限 `600` |
| 安装前备份 | `/var/backups/1panel-preinstall-20260812-181256` |

1Panel 未覆盖 Docker/containerd 的机械盘配置。其“容器 -> 编排”中应显示 `mall4cloud-middleware` 和 `metamcp`。

```bash
sudo 1pctl status
sudo 1pctl restart
sudo 1pctl version
```

不要把 `1pctl user-info` 输出写入笔记或日志。

## MetaMCP

| 项目         | 当前值                                         |
| ---------- | ------------------------------------------- |
| 版本基线       | `2.4.22`                                    |
| 镜像策略       | `ghcr.io/metatool-ai/metamcp:latest`        |
| 地址         | `http://192.168.1.117:12008`                |
| Compose 目录 | `/home/shadowdu/services/metamcp`           |
| 容器         | `metamcp`、`metamcp-pg`，均健康                  |
| PostgreSQL | `postgres:16-alpine`，不向 LAN 发布端口            |
| 数据卷        | `metamcp_postgres_data`                     |
| 更新脚本       | `/home/shadowdu/services/metamcp/update.sh` |
| 备份目录       | `/home/shadowdu/services/metamcp/backups`   |

更新流程会先备份 PostgreSQL，再拉取镜像并重建服务：

```bash
cd ~/services/metamcp
./update.sh
docker compose ps
```

## 管理端口

| 端口 | 绑定 | 用途 |
| ---: | --- | --- |
| `22` | 所有接口、IPv6 | SSH |
| `3389` | 所有接口 | 远程桌面 |
| `31788` | `192.168.1.117` | 1Panel |
| `12008` | `192.168.1.117` | MetaMCP |

mall4cloud 端口见 [[mall4cloud中间件]]。

## UFW 状态

- `ufw.service` 为 `active (exited)` 且 `enabled`。
- `/etc/ufw/ufw.conf` 中 `ENABLED=no`。
- 因此 UFW 服务单元存在，但规则当前未实际启用。

启用 UFW 前必须评估 SSH、远程桌面、1Panel、MetaMCP 和全部中间件端口。