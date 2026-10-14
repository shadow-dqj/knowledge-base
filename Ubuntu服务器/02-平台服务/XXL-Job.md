---
title: XXL-Job
type: service
tags: [xxl-job, scheduler, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: mall4cloud-job
status: verification-pending
---

# XXL-Job

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[Ubuntu服务器/04-故障记录/Order Provider缺失|任务故障]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> 容器 running、RestartCount 0；未配置 Docker healthcheck，必须验证 HTTP 和调度功能。

## 服务卡

| 项目             | 当前值                                        |
| -------------- | ------------------------------------------ |
| 容器             | `mall4cloud-job`                           |
| 镜像             | `xuxueli/xxl-job-admin:3.3.2`              |
| Console        | `http://192.168.3.59:8090/xxl-job-admin/` |
| 容器端口           | 8080                                       |
| 依赖             | MySQL `mall4cloud_job`                     |
| Executor Token | 见 [[Ubuntu服务器/_敏感/中间件凭据清单]]                |

## 应用级验证

```bash
curl -I http://192.168.3.59:8090/xxl-job-admin/
docker logs --tail=100 mall4cloud-job
```

> [!check] 成功判据
> HTTP 可达、管理页面可登录、Executor 在线、调度日志有成功回调。

## 任务注意

`handleSkuStockChange` 依赖 Order Provider。Provider 未启动时任务失败，但旧实现仍可能推进 Redis 时间游标，详见 [[Ubuntu服务器/04-故障记录/Order Provider缺失]]。

## 恢复

```bash
docker compose restart mall4cloud-job
```

重启后检查管理页面、执行器注册和失败任务重跑策略。