---
tags:
  - ssh
  - mcp
  - ubuntu
updated: 2026-08-13
server: 192.168.1.117
---

# SSH MCP

[[首页|返回首页]] · 相关：[[服务器与存储]] · [[安全与备份]]

> 最后核验：2026-08-13

## 连接目标

| 项目 | 当前值 |
| --- | --- |
| MCP 名称 | `ubuntu-ssh` |
| 主机 | `192.168.1.117` |
| 端口 | `22` |
| 用户 | `shadowdu` |
| 认证方式 | SSH Ed25519 私钥 |

Windows 侧私钥路径由 Factory MCP 配置引用。笔记只记录连接原则，不保存私钥内容、SSH 密码或授权文件内容。

## 验证方式

在 Windows PowerShell 中使用已配置的私钥执行 SSH 连接测试；成功后再通过 `ubuntu-ssh` MCP 执行只读检查。

服务器侧公钥应位于：

```text
/home/shadowdu/.ssh/authorized_keys
```

## 配置原则

- MCP 配置只能保存私钥路径，不能嵌入私钥内容。
- 不使用明文 SSH 密码。
- 私钥文件应限制为当前 Windows 用户可读。
- 服务器端 `authorized_keys` 和 `.ssh` 目录保持正确权限。
- 远程执行命令前先区分只读检查、可逆修改和高风险操作。
- 涉及 Docker 持久卷、数据库、UFW、systemd 或凭据时，先备份并验证范围。

## 常见排查

1. 检查目标 IP、端口和用户名。
2. 从 Windows 直接验证 SSH 私钥连接。
3. 检查服务器 `authorized_keys` 是否包含匹配公钥。
4. 检查 MCP 服务是否启用并已重新加载。
5. 检查命令超时是否适合 Docker 构建或长时间任务。

服务器状态、Docker 数据位置和安全限制分别见 [[服务器与存储]]、[[Docker与管理平台]] 和 [[安全与备份]]。