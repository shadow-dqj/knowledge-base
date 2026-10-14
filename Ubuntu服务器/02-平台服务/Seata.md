---
title: Seata
type: service
tags: [seata, transaction, mall4cloud]
updated: 2026-10-14
server: 192.168.3.59
service: mall4cloud-seata
status: verification-pending
---

# Seata

> [!warning] 2026-10-14 地址同步
> 机器已迁至 `192.168.3.59`，下列入口与检查命令已按新 IP 更新；文档更新不代表运行配置已迁移或服务已恢复。正文版本、资源及 `healthy/running` 描述保留为历史基线，本次状态和待同步项见 [[Ubuntu服务器/首页#2026-10-14 IP 迁移核验|IP 迁移核验]]。

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[Nacos]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!warning] 历史状态（2026-08 基线）
> Seata 2.6.0 容器 running、RestartCount 0；没有 Docker healthcheck。

## 服务卡

| 项目 | 当前值 |
| --- | --- |
| 容器 | `mall4cloud-seata` |
| LAN | `192.168.3.59:8091` |
| Console 9091 | 仅容器网络，未发布到 LAN |
| 配置 | `config/seata/application.yml` |
| JDBC Driver | `config/seata/mysql-connector-j-8.0.33.jar` |
| 数据库 | MySQL `mall4cloud_seata` |
| 依赖 | MySQL、Nacos 配置/注册 |

## 应用级验证

```bash
docker logs --tail=100 mall4cloud-seata
ss -lnt | grep ':8091'
```

> [!check] 成功判据
> 日志无持续数据库/注册中心错误，8091 监听，Java 客户端可注册并完成全局事务。

## 维护注意

- 数据源凭据见 [[Ubuntu服务器/_敏感/中间件凭据清单]]。
- 修改 MySQL、Nacos 或 namespace 时同步检查 Seata。
- 升级前核对数据库表结构和客户端兼容性。