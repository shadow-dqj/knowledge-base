---
title: RocketMQ 服务族
type: service-group
tags: [rocketmq, mq, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: rocketmq
status: verification-pending
---

# RocketMQ 服务族

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[Canal]] · [[Ubuntu服务器/03-运维手册/端口与依赖矩阵]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> Nameserver、Broker、Dashboard 均 running、RestartCount 0；三者均无 Docker healthcheck。

## 组件

| 组件 | 容器 | LAN 端口 | 存储 |
| --- | --- | --- | --- |
| Nameserver | `mall4cloud-rocketmq-namesrv` | 9876 | logs、store 命名卷 |
| Broker | `mall4cloud-rocketmq-broker` | 10909、10911 | logs、store 命名卷 |
| Dashboard | `mall4cloud-rocketmq-dashboard` | 8180 | 用户配置 bind mount |

## 依赖

```text
Canal -> Nameserver -> Broker
Dashboard -> Nameserver
mall4cloud Java clients -> Nameserver/Broker
```

Broker 对外广播地址目标为 `192.168.3.59:10911`，实际配置待同步复核。历史核验未发现 ACL AccessKey/SecretKey；Dashboard 登录见 [[Ubuntu服务器/_敏感/中间件凭据清单]]。

## 应用级验证

```bash
docker logs --tail=100 mall4cloud-rocketmq-namesrv
docker logs --tail=100 mall4cloud-rocketmq-broker
curl -I http://192.168.3.59:8180/
```

> [!check] 成功判据
> Nameserver/Broker 日志出现 boot success，Dashboard 可登录，Canal 能投递且 Broker 有对应 Topic/消费活动。

## 启动顺序

1. Nameserver
2. Broker
3. Dashboard、Canal、业务客户端

> [!danger] Broker Store
> 升级或重建 Broker 前备份 Store，确认 Topic、消费位点和消息保留要求。