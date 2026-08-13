---
title: Nacos
type: service
tags: [nacos, registry, config]
updated: 2026-08-13
server: 192.168.1.117
service: mall4cloud-nacos
status: healthy
---

# Nacos

[[Ubuntu服务器/首页|← 运维中心]] · [[MySQL]] · [[Ubuntu服务器/04-故障记录/Dubbo Mapping CAS|Dubbo CAS]] · [[Ubuntu服务器/_敏感/中间件凭据清单|凭据]]

> [!check] 当前状态
> Nacos 3.1.1，Docker healthcheck `healthy`，依赖 MySQL。

## 接口

| 用途 | 地址 |
| --- | --- |
| Console | `http://192.168.1.117:8080/index.html` |
| API | `192.168.1.117:8848` |
| gRPC | `192.168.1.117:9848/9849` |
| Windows Hosts | `192.168.1.117 nacos.mall4cloud.local` |

## 数据与命名空间

| 项目 | 当前值 |
| --- | --- |
| 配置存储 | MySQL `mall4cloud_nacos` |
| 日志卷 | `mall4cloud-nacos-logs` |
| 应用配置 namespace | `public` |
| Dubbo metadata namespace | `public-dubbo` |
| 登录接口 | `POST /nacos/v3/auth/user/login` |

## 已完成修正

- 26 条旧 IP `192.168.1.41` 已替换为 `192.168.1.117`。
- 空 tenant 和 `public` 的对应配置保持一致。
- MongoDB 密码已在两套配置中同步。
- 修改前备份见 [[Ubuntu服务器/03-运维手册/备份与升级#已有备份]]。

## 验证

```bash
docker inspect -f '{{.State.Health.Status}}' mall4cloud-nacos
curl -I http://192.168.1.117:8080/index.html
```

> [!warning] Nacos 3 API
> 旧 `/nacos/v1/auth/users/login` 返回异常不能用于判断密码；使用 v3 登录接口。

## Dubbo 元数据

Mapping CAS 的判断、`invokers: 0` 区别和处理条件见 [[Ubuntu服务器/04-故障记录/Dubbo Mapping CAS]]。不要直接删除 `public-dubbo` 数据来消除告警。