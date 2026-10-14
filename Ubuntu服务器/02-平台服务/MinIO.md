---
title: MinIO
type: service
tags: [minio, object-storage, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: mall4cloud-minio
status: verification-pending
---

# MinIO

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[Ubuntu服务器/03-运维手册/备份与升级]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> 容器 running、RestartCount 0；无 Docker healthcheck，需验证 API/Console。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-minio` |
| 版本 | `RELEASE.2025-09-07T16-13-09Z` |
| API | `http://192.168.3.59:9000` |
| Console | `http://192.168.3.59:9001` |
| 数据卷 | `mall4cloud-minio-data` → `/data` |
| Bucket | `mall4cloud` |

## 应用级验证

```bash
curl -I http://192.168.3.59:9001
docker logs --tail=100 mall4cloud-minio
```

可在容器内使用 `mc` 验证服务和 bucket。

> [!check] 成功判据
> Console 可达、API 响应、`mall4cloud` bucket 存在且应用可上传/读取对象。

> [!warning] Bucket Policy
> 不默认开放匿名读；按业务对象访问需求最小化配置。

## 备份

使用 MinIO 客户端镜像/复制或服务级备份，不直接把运行中的卷目录复制作为唯一备份。