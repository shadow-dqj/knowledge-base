---
title: Elasticsearch
type: service
tags: [elasticsearch, search, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: mall4cloud-elasticsearch
status: verification-pending
---

# Elasticsearch

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[Canal]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> Elasticsearch 7.17.28，Docker healthcheck `healthy`，RestartCount 0。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-elasticsearch` |
| LAN API | `http://192.168.3.59:9200` |
| Transport 9300 | 仅容器网络 |
| 配置 | `config/elasticsearch/elasticsearch.yml` |
| 插件目录 | `config/elasticsearch/plugins` |
| 数据卷 | `mall4cloud-es-data` |
| 索引 | `product` |
| 插件 | `analysis-ik` |

## 验证

```bash
curl -u '<见凭据清单>' http://192.168.3.59:9200/_cluster/health
docker exec mall4cloud-elasticsearch \
  /usr/share/elasticsearch/bin/elasticsearch-plugin list
```

> [!check] 成功判据
> Cluster health API 成功、`product` 索引存在、插件列表包含 `analysis-ik`。

> [!danger] 版本约束
> Elasticsearch 与 IK 插件版本必须匹配；升级前先验证索引兼容和恢复方案。

## 数据恢复

明确索引是否可由 MySQL/Canal 重建；不可重建数据应使用 snapshot repository。