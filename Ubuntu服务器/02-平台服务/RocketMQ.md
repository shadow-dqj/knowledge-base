---
title: RocketMQ 服务族
type: service-group
tags: [rocketmq, mq, mall4cloud]
updated: 2026-08-13
server: 192.168.1.117
service: rocketmq
status: running
---

# RocketMQ 服务族

[[Ubuntu服务器/首页|← 运维中心]] · [[Canal]] · [[Ubuntu服务器/03-运维手册/端口与依赖矩阵]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 状态
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

Broker 对外广播地址为 `192.168.1.117:10911`。当前 Broker 未发现 ACL AccessKey/SecretKey；Dashboard 登录见 [[Ubuntu服务器/_敏感/中间件凭据清单]]。

## 应用级验证

```bash
docker logs --tail=100 mall4cloud-rocketmq-namesrv
docker logs --tail=100 mall4cloud-rocketmq-broker
curl -I http://192.168.1.117:8180/
```

> [!check] 成功判据
> Nameserver/Broker 日志出现 boot success，Dashboard 可登录，Canal 能投递且 Broker 有对应 Topic/消费活动。

## 启动顺序

1. Nameserver
2. Broker
3. Dashboard、Canal、业务客户端

> [!danger] Broker Store
> 升级或重建 Broker 前备份 Store，确认 Topic、消费位点和消息保留要求。