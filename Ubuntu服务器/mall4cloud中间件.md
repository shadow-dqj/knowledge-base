---
tags:
  - mall4cloud
  - docker
  - 中间件
updated: 2026-08-13
server: 192.168.1.117
---

# mall4cloud 中间件

[[首页|返回首页]] · 相关：[[Docker与管理平台]] · [[Nacos与Dubbo]] · [[故障记录]] · [[安全与备份]]

> 最后核验：2026-08-13  
> Compose 项目：`mall4cloud-middleware`  
> 部署目录：`/home/shadowdu/services/mall4cloud-middleware`  
> 状态：运行中 `13/13`

## 部署信息

| 项目 | 当前值 |
| --- | --- |
| Compose 文件 | `/home/shadowdu/services/mall4cloud-middleware/compose.yaml` |
| 环境变量 | `/home/shadowdu/services/mall4cloud-middleware/.env`，权限 `600` |
| 配置目录 | `/home/shadowdu/services/mall4cloud-middleware/config` |
| Compose 网络 | `mall4cloud-network` |
| Docker 数据目录 | `/mnt/docker-data/docker` |

中间件通过独立 Compose 网络互联。LAN 端口只绑定 `192.168.1.117`，没有绑定 `0.0.0.0`。项目 Nginx、Kibana 和 ShardingSphere 当前未部署；`80/443` 留给 1Panel OpenResty。

## 服务与端口

| 服务                  | 版本                             | LAN 端口                                | 状态      |
| ------------------- | ------------------------------ | ------------------------------------- | ------- |
| MySQL               | `8.0.44`                       | `3306`                                | healthy |
| Redis               | `8.4.0`                        | `6379`                                | healthy |
| Nacos               | `3.1.1`                        | UI `8080`、API `8848`、gRPC `9848/9849` | healthy |
| XXL-Job             | `3.3.2`                        | `8090`                                | running |
| Seata               | `2.6.0`                        | `8091`                                | running |
| RocketMQ Dashboard  | `2.1.0`                        | `8180`                                | running |
| MinIO               | `RELEASE.2025-09-07T16-13-09Z` | API `9000`、Console `9001`             | running |
| Elasticsearch       | `7.17.28`                      | `9200`                                | healthy |
| RocketMQ Nameserver | `5.4.0`                        | `9876`                                | running |
| RocketMQ Broker     | `5.4.0`                        | `10909/10911`                         | running |
| MongoDB             | `7.0.28-jammy`                 | `27017`                               | healthy |
| WuKongIM            | `v2.2.2-20251229`              | API `5001`、WebSocket `5200`           | running |
| Canal               | `1.1.8`                        | 仅 Compose 网络                          | running |

## 初始化结果

### MySQL

- 14 个 `mall4cloud_*` 数据库，共 300 张表。
- 20 份初始化 SQL 已全部执行，不应重复批量导入。
- Binlog 开启，格式 `ROW`，`server_id=1`。

### Redis

- 使用持久卷并开启 AOF。
- 策略：`appendonly yes`、`appendfsync everysec`。
- 不要清空商城库存和缓存数据。

### MinIO 与 Elasticsearch

- MinIO bucket：`mall4cloud`。
- Elasticsearch 索引：`product`。
- Elasticsearch 插件：`analysis-ik`，升级时必须保持版本匹配。

### Canal 与 RocketMQ

```text
MySQL ROW binlog -> Canal -> RocketMQ
```

Canal 已连接 MySQL，Binlog Dump 位点可推进，并向 RocketMQ 投递。MySQL 短暂重启时历史日志可能出现连接失败，应以当前线程和位点判断恢复状态。

### MongoDB 与 WuKongIM

- MongoDB root 和 `mall4cloud` 应用账号已初始化，认证开启。
- WuKongIM 认证开启，单节点 slot/channel 副本数已调整为 1。
- MongoDB 凭据修复记录见 [[故障记录#MongoDB 凭据不一致]]。

## 持久卷

持久数据位于 `/mnt/docker-data/docker/volumes`。主要命名卷包括：

```text
mall4cloud-mysql-data
mall4cloud-redis-data
mall4cloud-minio-data
mall4cloud-es-data
mall4cloud-mongo-configdb
mall4cloud-mongo-data
mall4cloud-mongo-log
mall4cloud-canal-logs
mall4cloud-rocketmq-namesrv-logs
mall4cloud-rocketmq-namesrv-store
mall4cloud-rocketmq-broker-logs
mall4cloud-rocketmq-broker-store
mall4cloud-wukongim-data
mall4cloud-wukongim-logs
```

## 日常运维

```bash
cd ~/services/mall4cloud-middleware
docker compose ps
docker compose logs --tail=100
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
```

可使用 `docker compose stop`、`restart` 或 `down`。**禁止执行：**

```bash
docker compose down -v
```

`-v` 会删除 MySQL、Redis、MinIO、Elasticsearch、MongoDB、RocketMQ 和 WuKongIM 的持久卷。