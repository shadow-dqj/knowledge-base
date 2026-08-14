---
title: Docker 与容器
type: infrastructure
tags: [docker, compose, 基础设施]
updated: 2026-08-13
server: 192.168.1.117
status: running
---

# Docker 与容器

[[Ubuntu服务器/首页|← 运维中心]] · [[服务器与存储]] · [[../03-运维手册/启停与恢复]]

## 版本与策略

| 项目             | 当前值                           |
| -------------- | ----------------------------- |
| Docker Engine  | `29.7.2`                      |
| Docker Compose | `5.4.0`                       |
| Buildx         | `0.36.1`                      |
| Root Dir       | `/mnt/docker-data/docker`     |
| 镜像加速           | `https://docker.1panel.live/` |
| 普通用户           | `shadowdu` 可直接运行 Docker       |
| Restart Policy | 中间件统一 `unless-stopped`        |
|                |                               |
URL: http://192.168.1.117:31788/manage_d12fa1e2f3
Port: 31788
Security entrance: manage_d12fa1e2f3
Username: admin_d12fa1e2f3
Password: xIqdWWrVWYZ6EjT1eB5ayEMd

## Compose 项目

| 项目 | 目录 | 状态 |
| --- | --- | --- |
| `mall4cloud-middleware` | `/home/shadowdu/services/mall4cloud-middleware` | 13/13 running |
| `metamcp` | `/home/shadowdu/services/metamcp` | 2/2 healthy |

## 健康模型

| 类型 | 服务 |
| --- | --- |
| Docker healthcheck | MySQL、Redis、Nacos、MongoDB、Elasticsearch、MetaMCP、MetaMCP PostgreSQL |
| 仅进程状态 | MinIO、Seata、XXL-Job、Canal、RocketMQ 三组件、WuKongIM |

> [!warning] 状态含义
> `running` 不等于业务可用。无 healthcheck 服务必须执行 [[../03-运维手册/日常巡检#应用级检查|应用级检查]]。

## 常用命令

```bash
docker compose ls
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker system df
docker volume ls
```

## 安全操作

```bash
# 保留卷地停止并删除容器
docker compose down

# 单服务重启
docker compose restart mall4cloud-nacos
```

> [!danger] 禁止
> `docker compose down -v` 会删除 MySQL、Redis、MongoDB、MinIO、Elasticsearch、RocketMQ 和 WuKongIM 等持久数据。

## 配置权限

| 文件 | 权限 |
| --- | ---: |
| mall4cloud `compose.yaml` | `644` |
| mall4cloud `.env` | `600` |
| MetaMCP `compose.yaml` | `600` |
| MetaMCP `.env` | `600` |
| MetaMCP `update.sh` | `700` |

## 相关

[[服务器与存储]] · [[../03-运维手册/启停与恢复]] · [[../03-运维手册/备份与升级]]