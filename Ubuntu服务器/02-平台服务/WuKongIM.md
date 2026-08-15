---
title: WuKongIM
type: service
tags: [wukongim, websocket, im]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-wukongim
status: running
---

# WuKongIM

[[Ubuntu服务器/首页|← 运维中心]] · [[Ubuntu服务器/03-运维手册/端口与依赖矩阵]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 状态
> WuKongIM 容器 running、RestartCount 0；无 Docker healthcheck。

## 服务卡

| 项目        | 当前值                         |     |
| --------- | --------------------------- | --- |
| 容器        | `mall4cloud-wukongim`       |     |
| 版本        | `v2.2.2-20251229`           |     |
| HTTP API  | `http://192.168.1.117:5001` |     |
| WebSocket | `ws://192.168.1.117:5200`   |     |
| 配置        | `config/wukongim/wk.yaml`   |     |
| 数据卷       | `mall4cloud-wukongim-data`  |     |
| 日志卷       | `mall4cloud-wukongim-logs`  |     |

## 单节点配置

- Token 认证已开启。
- slot/channel 副本数适配为 1。
- Manager 用户、Token 和 JWT Secret 见 [[Ubuntu服务器/_敏感/中间件凭据清单]]。
- Webhook 指向 mall4cloud Biz 的 8000 端口。

## 应用级验证

```bash
curl -I http://192.168.1.117:5001
docker logs --tail=100 mall4cloud-wukongim
```

> [!check] 成功判据
> API 有响应、WebSocket 可建立、单节点无副本不足错误、认证按预期拒绝无 Token 请求。

> [!warning] Webhook
> Biz Java 服务未启动时 Webhook 失败不代表 WuKongIM 主进程离线；需区分依赖不可用与 IM 服务故障。