---
title: SSH MCP
type: integration
tags: [ssh, mcp, windows]
updated: 2026-08-13
server: 192.168.1.117
status: connected
---

# SSH MCP

[[Ubuntu服务器/首页|← 运维中心]] · [[服务器与存储]] · [[网络与防火墙]]

## 连接配置

| 项目 | 当前值 |
| --- | --- |
| MCP 名称 | `ubuntu-ssh` |
| 目标 | `shadowdu@192.168.1.117:22` |
| 认证 | Ed25519 私钥 |
| Server authorized keys | `/home/shadowdu/.ssh/authorized_keys` |

> [!warning] 凭据边界
> MCP 配置只引用私钥路径，不嵌入私钥正文或 SSH 密码。

## 排查顺序

1. Windows 直接测试 SSH 私钥连接。
2. 检查 IP、端口、用户名和公钥。
3. 检查 MCP 是否 enabled/connected。
4. 长任务确认 MCP timeout 足够。
5. 涉及数据库、卷、systemd 或 UFW 时先备份并缩小修改范围。

## 成功判据

- SSH 可无交互登录。
- MCP 可执行 `hostname` 等只读命令。
- 命令结果主机名为 `shadowdu-911MT`。

## 相关

[[服务器与存储]] · [[../03-运维手册/日常巡检]]