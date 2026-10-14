---
title: Redis
type: service
tags: [redis, cache, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: mall4cloud-redis
status: verification-pending
---

# Redis

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> Redis 8.4.0，Docker healthcheck `healthy`，AOF 已启用。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-redis` |
| 镜像 | `redis:8.4.0` |
| LAN | `192.168.3.59:6379` |
| 数据卷 | `mall4cloud-redis-data` → `/data` |
| 认证 | `requirepass`；见 [[Ubuntu服务器/_敏感/中间件凭据清单]] |
| 持久化 | `appendonly yes`、`appendfsync everysec` |

## 用途

商城缓存、库存状态、任务游标和分布式协调。`SKU_CHANGE_STATISTICS_TIME` 等运行状态可能影响补偿任务。

## 验证

```bash
docker exec mall4cloud-redis sh -c \
  'redis-cli --no-auth-warning -a "$REDIS_PASSWORD" PING'

docker exec mall4cloud-redis sh -c \
  'redis-cli --no-auth-warning -a "$REDIS_PASSWORD" CONFIG GET appendonly'
```

> [!danger] 数据操作
> 不要执行无范围的 `FLUSHALL`、`FLUSHDB` 或删除未知业务 Key。修改任务游标前先记录原值和补偿窗口。

## 日志与恢复

```bash
docker logs --tail=100 mall4cloud-redis
docker compose restart mall4cloud-redis
```

重启后确认 PONG、AOF 配置和 Java 服务重连。