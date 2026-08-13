---
title: Canal
type: service
tags: [canal, binlog, rocketmq]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-canal
status: running
---

# Canal

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[RocketMQ]] · [[Elasticsearch]]

> [!warning] 状态
> Canal 1.1.8 容器 running、RestartCount 0；无 Docker healthcheck，端口仅容器网络。

## 数据链路

```text
MySQL ROW Binlog -> Canal -> RocketMQ -> 业务消费者 / Elasticsearch
```

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-canal` |
| 配置 | `config/canal/canal.properties`、`config/canal/example` |
| 日志卷 | `mall4cloud-canal-logs` |
| LAN 发布 | 无 |
| 依赖 | MySQL、RocketMQ Broker |
| MySQL 账号 | canal；见 [[Ubuntu服务器/_敏感/中间件凭据清单]] |

## 应用级验证

```bash
docker exec mall4cloud-canal \
  sh -c 'tail -20 /home/admin/canal-server/logs/example/meta.log'

docker exec mall4cloud-mysql sh -c \
  'mysql -h127.0.0.1 -uroot -p"$MYSQL_ROOT_PASSWORD" -e "SHOW PROCESSLIST;"'
```

> [!check] 成功判据
> MySQL 有 Binlog Dump 线程，Canal 位点持续推进，RocketMQ 投递没有持续异常。

> [!info] 重连判断
> MySQL 重启时历史日志会出现 connection refused；应以当前 Dump 线程和最新位点判断是否恢复。

## 恢复注意

重建 Canal 前记录 binlog 文件和位点，避免重复消费或数据缺口。