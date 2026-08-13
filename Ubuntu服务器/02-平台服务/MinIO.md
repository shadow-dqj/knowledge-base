---
title: MinIO
type: service
tags: [minio, object-storage, mall4cloud]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-minio
status: running
---

# MinIO

[[Ubuntu服务器/首页|← 运维中心]] · [[Ubuntu服务器/03-运维手册/备份与升级]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 状态
> 容器 running、RestartCount 0；无 Docker healthcheck，需验证 API/Console。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-minio` |
| 版本 | `RELEASE.2025-09-07T16-13-09Z` |
| API | `http://192.168.1.117:9000` |
| Console | `http://192.168.1.117:9001` |
| 数据卷 | `mall4cloud-minio-data` → `/data` |
| Bucket | `mall4cloud` |

## 应用级验证

```bash
curl -I http://192.168.1.117:9001
docker logs --tail=100 mall4cloud-minio
```

可在容器内使用 `mc` 验证服务和 bucket。

> [!check] 成功判据
> Console 可达、API 响应、`mall4cloud` bucket 存在且应用可上传/读取对象。

> [!warning] Bucket Policy
> 不默认开放匿名读；按业务对象访问需求最小化配置。

## 备份

使用 MinIO 客户端镜像/复制或服务级备份，不直接把运行中的卷目录复制作为唯一备份。