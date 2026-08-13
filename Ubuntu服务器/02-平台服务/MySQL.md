---
title: MySQL
type: service
tags: [mysql, database, mall4cloud]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-mysql
status: healthy
---

# MySQL

[[Ubuntu服务器/首页|← 运维中心]] · [[Redis]] · [[Nacos]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!check] 当前状态
> MySQL 8.0.44，Docker healthcheck `healthy`，RestartCount 0。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-mysql` |
| 镜像 | `mysql:8.0.44` |
| LAN | `192.168.1.117:3306` |
| 数据卷 | `mall4cloud-mysql-data` → `/var/lib/mysql` |
| 初始化目录 | `config/mysql-init` → `/docker-entrypoint-initdb.d` |
| 账号 | root、canal；见 [[Ubuntu服务器/_敏感/中间件凭据清单]] |

## 数据基线

- 14 个 `mall4cloud_*` 数据库，共 300 张表。
- 20 份初始化 SQL 已执行完成。
- Binlog 开启，`binlog_format=ROW`，`server_id=1`。
- Canal 使用专用复制账号。

## 依赖方

[[Nacos]]、[[Seata]]、[[XXL-Job]]、[[Canal]] 以及 mall4cloud Java 服务。

## 验证

```bash
docker exec mall4cloud-mysql sh -c \
  'mysqladmin ping -h127.0.0.1 -uroot -p"$MYSQL_ROOT_PASSWORD" --silent'

docker exec mall4cloud-mysql sh -c \
  'mysql -h127.0.0.1 -uroot -p"$MYSQL_ROOT_PASSWORD" \
  -e "SHOW MASTER STATUS; SELECT @@log_bin,@@binlog_format,@@server_id;"'
```

> [!danger] 初始化 SQL
> 不要重复批量导入 20 份初始化 SQL；可能导致重复主键和初始数据冲突。

## 日志与备份

```bash
docker logs --tail=100 mall4cloud-mysql
```

升级、改表或重建前执行全库逻辑备份；不要以卷目录热复制作为唯一备份。