---
title: SSH MCP
type: integration
tags: [ssh, mcp, windows]
updated: 2026-10-14
server: 192.168.3.59
status: connected
---

# SSH MCP

[[Ubuntu服务器/首页|← 运维中心]] · [[服务器与存储]] · [[网络与防火墙]]

## 连接配置

| 项目 | 当前值 |
| --- | --- |
| 统一 MCP 入口 | `server-ssh`，调用时显式指定 `profile=ubuntu` |
| 目标 | `shadowdu@192.168.3.59:22` |
| 公共连接文件 | `C:\Users\shado\AppData\Roaming\ssh-mcp\config.toml`，已追加 `ubuntu`，共有 11 个 profile |
| MCP 启动定义 | `C:\Users\shado\.factory\mcp.json`；`server-ssh` 沿用显式公共配置与 `--strictConfigAcl` |
| 认证 | Ed25519 私钥；`C:\Users\shado\.ssh\shadow_server_mcp_ed25519`，仅引用路径 |
| 主机校验 | 固定已验证的 `trustedHostKey`；新 IP 公钥与旧机器已信任公钥一致，未关闭校验 |
| 权限与会话 | `group=dev`、`role=admin`、`approvalPolicy=ask-destructive`、会话上限 3 |
| 独立 MCP 入口 | `ubuntu-ssh`，显式配置 `C:\Users\shado\AppData\Roaming\ssh-mcp\ubuntu.toml`，默认 profile `ubuntu`，启用 `--hostKeyMode=strict` |
| 原生 SSH MCP 入口 | `ssh` MCP，`hostAlias=ub-server`；别名在 `C:\Users\shado\.ssh\config` |
| Server authorized keys | `/home/shadowdu/.ssh/authorized_keys` |

> [!warning] 默认连接与生效方式
> 公共默认 profile 仍为 `server-125`；使用 `server-ssh` 时不指定 profile 会连接原默认服务器，而不是 Ubuntu。原 10 个 profile、默认值及全局策略均未改。Ubuntu 保留原审批策略，不继承公共自动审批。已运行的 MCP 不会自动重载，需重连/重启 `server-ssh` 和 `ubuntu-ssh`，或重启客户端。

## 调用示例

重连后，在 `server-ssh` 的 `run-command` 工具中使用以下参数；不要省略 `profile`：

```json
{
  "profile": "ubuntu",
  "command": "hostname; id -un; ip -4 -brief address show scope global"
}
```

原生 `ssh` MCP 的 `runRemoteCommand` 使用别名参数：

```json
{
  "hostAlias": "ub-server",
  "command": "hostname"
}
```

## 2026-10-14 连接测试

- `ssh.checkConnectivity(hostAlias=ub-server)` 成功，远端只读命令确认主机身份。
- 分别按 `ubuntu-ssh` 和 `server-ssh` 的实际启动定义创建测试 MCP 客户端，经真实 stdio 协议调用 `list-connections`、`run-command(profile=ubuntu)` 成功；公共配置加载 11 个 profile，测试时 `ubuntu` 状态为 `connected`。测试客户端已关闭，不代表当前客户端旧进程已重载。
- 返回主机 `shadowdu-911MT`、用户 `shadowdu`、网卡 `wlo1` 地址 `192.168.3.59/24`。
- 本次仅修改 Windows 侧连接配置，未改服务器授权公钥、服务、数据库或防火墙；SSH 可用不代表中间件可用，详见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|当前核验]]。
- 文档复核已修正公共配置、MCP 启动定义、私钥引用和独立配置的 4 处损坏路径，并补充显式 profile 调用示例；只记录文件位置，不读取或复制私钥内容。

> [!info] 旧参数覆盖问题
> 原独立 `ubuntu-ssh` 参数还保留更早的主机地址，但当前 ssh-mcp 会优先加载公共默认配置并忽略 `--host/--user`。已改为显式指定独立 `ubuntu.toml`，避免误用公共默认服务器。

> [!warning] 凭据边界
> MCP 配置只引用私钥路径，不嵌入私钥正文或 SSH 密码。

## 排查顺序

1. Windows 直接测试别名连接：`ssh -o BatchMode=yes ub-server hostname`。
2. 检查 IP、端口、用户名和公钥。
3. 检查 MCP 是否 enabled/connected，确认 `server-ssh` 显式使用 `profile=ubuntu`；变更后重连/重启再测试。
4. 长任务确认 MCP timeout 足够。
5. 涉及数据库、卷、systemd 或 UFW 时先备份并缩小修改范围。

## 成功判据

- SSH 可无交互登录。
- MCP 可执行 `hostname` 等只读命令。
- 命令结果主机名为 `shadowdu-911MT`。

## 相关

[[服务器与存储]] · [[../03-运维手册/日常巡检]]