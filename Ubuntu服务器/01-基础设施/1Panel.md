---
title: 1Panel
type: management
tags: [1panel, 管理平台]
updated: 2026-08-15
server: 192.168.1.117
status: running
---

# 1Panel

[[Ubuntu服务器/首页|← 运维中心]] · [[Docker与容器]] · [[网络与防火墙]]

> [!info] 状态
> 版本 `v2.2.5 stable`；Core 和 Agent 均 active/enabled。

## 接入

| 项目 | 当前值 |
| --- | --- |
| 监听 | `192.168.1.117:31788` |
| 授权网段 | `192.168.1.0/24` |
| IPv6 面板监听 | 关闭 |
| 凭据文件 | `/home/shadowdu/.config/1panel/admin-credentials.txt` |
| 凭据权限 | `600` |
| 安装前备份 | `/var/backups/1panel-preinstall-20260812-181256` |

> [!warning] 安全入口
> 本文不复制完整 URL、用户名或密码；以服务器凭据文件为准。

## 管理

```bash
sudo 1pctl status
sudo 1pctl restart
sudo 1pctl version
```

## Docker 关系

1Panel 可识别 Compose 项目，但未接管或覆盖 `/mnt/docker-data`、Docker daemon 或 containerd 的机械盘配置。

> [!check] 面板预期
> “容器 → 编排”显示 `mall4cloud-middleware` 13/13、`metamcp` 2/2、
> `new-api` 1/1，以及 `dify` 15 个常驻容器。Dify 的
> `init_permissions` 为一次性初始化容器，`Exited (0)` 属正常状态。

## 相关

[[Docker与容器]] · [[../03-运维手册/日常巡检]]