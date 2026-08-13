---
title: Elasticsearch
type: service
tags: [elasticsearch, search, mall4cloud]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-elasticsearch
status: healthy
---

# Elasticsearch

[[Ubuntu服务器/首页|← 运维中心]] · [[Canal]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!check] 当前状态
> Elasticsearch 7.17.28，Docker healthcheck `healthy`，RestartCount 0。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-elasticsearch` |
| LAN API | `http://192.168.1.117:9200` |
| Transport 9300 | 仅容器网络 |
| 配置 | `config/elasticsearch/elasticsearch.yml` |
| 插件目录 | `config/elasticsearch/plugins` |
| 数据卷 | `mall4cloud-es-data` |
| 索引 | `product` |
| 插件 | `analysis-ik` |

## 验证

```bash
curl -u '<见凭据清单>' http://192.168.1.117:9200/_cluster/health
docker exec mall4cloud-elasticsearch \
  /usr/share/elasticsearch/bin/elasticsearch-plugin list
```

> [!check] 成功判据
> Cluster health API 成功、`product` 索引存在、插件列表包含 `analysis-ik`。

> [!danger] 版本约束
> Elasticsearch 与 IK 插件版本必须匹配；升级前先验证索引兼容和恢复方案。

## 数据恢复

明确索引是否可由 MySQL/Canal 重建；不可重建数据应使用 snapshot repository。