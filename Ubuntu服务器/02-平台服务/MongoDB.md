---
title: MongoDB
type: service
tags: [mongodb, database, mall4cloud]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-mongo
status: healthy
---

# MongoDB

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[Ubuntu服务器/04-故障记录/MongoDB凭据不一致|凭据故障]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!check] 当前状态
> MongoDB 7.0.28-jammy，认证开启，Docker healthcheck `healthy`。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-mongo` |
| LAN | `192.168.1.117:27017` |
| 配置 | `config/mongo/mongodb.conf` |
| 初始化 | `config/mongo/init` |
| 数据卷 | `mall4cloud-mongo-data` |
| ConfigDB | `mall4cloud-mongo-configdb` |
| 日志卷 | `mall4cloud-mongo-log` |

## 账号模型

- 管理账号位于 `admin`，角色 root。
- 应用账号位于 `mall4cloud`，角色 `dbOwner`。
- 登录必须使用正确 `authenticationDatabase`；明文见 [[Ubuntu服务器/_敏感/中间件凭据清单]]。

## 验证

```bash
docker exec mall4cloud-mongo sh -c \
  'mongosh --quiet --username "$MONGO_INITDB_ROOT_USERNAME" \
  --password "$MONGO_INITDB_ROOT_PASSWORD" --authenticationDatabase admin \
  --eval "db.adminCommand({ping:1})"'
```

> [!warning] 客户端配置
> 修改应用密码后，必须同步 Nacos 空 tenant 与 `public` 两套命名空间，并验证 MD5 和应用启动。

## 日志与备份

```bash
docker logs --tail=100 mall4cloud-mongo
```

使用 `mongodump`/`mongorestore` 做一致性备份，不以运行中卷目录复制替代。