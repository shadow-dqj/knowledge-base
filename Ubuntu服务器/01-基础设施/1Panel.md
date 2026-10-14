---
title: 1Panel
type: management
tags: [1panel, 管理平台]
updated: 2026-10-14
server: 192.168.3.59
status: verification-pending
---

# 1Panel

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[Docker与容器]] · [[网络与防火墙]]

> [!info] 历史状态（2026-08 基线）
> 版本 `v2.2.5 stable`；Core 和 Agent 均 active/enabled。

## 接入

| 项目 | 当前值 |
| --- | --- |
| 目标监听 | `192.168.3.59:31788`（新地址端口未核验） |
| 授权网段 | 历史为 `192.168.1.0/24`；换网后实际规则待核验，按客户端来源最小化调整，不视为已放行 `192.168.3.0/24` |
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