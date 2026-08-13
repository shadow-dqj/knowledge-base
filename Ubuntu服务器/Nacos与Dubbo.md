---
tags:
  - nacos
  - dubbo
  - mall4cloud
updated: 2026-08-13
server: 192.168.1.117
---

# Nacos 与 Dubbo

[[首页|返回首页]] · 相关：[[mall4cloud中间件]] · [[故障记录]] · [[安全与备份]]

> 最后核验：2026-08-13

## Nacos 访问与客户端配置

| 项目 | 当前值 |
| --- | --- |
| Nacos 版本 | `3.1.1` |
| Console | `http://192.168.1.117:8080/index.html` |
| API | `192.168.1.117:8848` |
| gRPC | `192.168.1.117:9848/9849` |
| Windows Hosts 名称 | `nacos.mall4cloud.local` |
| 配置命名空间 | `public` |
| Dubbo 元数据命名空间 | `public-dubbo` |

Windows Hosts 应使用：

```text
192.168.1.117 nacos.mall4cloud.local
```

域名是 `nacos.mall4cloud.local`，不是 `nacos.mall4cloud-local`。Nacos 3 登录接口为 `POST /nacos/v3/auth/user/login`；旧版 v1 登录兼容接口不能用于判断密码是否正确。

## 旧 IP 修正

初始化配置中 26 条记录引用旧地址 `192.168.1.41`，已精确替换为 `192.168.1.117`：

- 空 `tenant_id`：13 条。
- `public` 命名空间：13 条。
- 旧 IP 剩余：0。
- 新 IP 引用：26。
- MD5 不匹配：0。
- 两套命名空间 14 组配置保持一致。

修改前备份：

```text
/home/shadowdu/services/mall4cloud-middleware/backups/nacos/mall4cloud_nacos-before-ip-fix-20260813-122146.sql
```

## Dubbo 配置基线

- Dubbo `3.3.6`。
- Nacos Client `3.1.1`。
- 注册模式：`instance`。
- 服务发现迁移：`FORCE_APPLICATION`。
- Dubbo registry 使用 Nacos，并将元数据写入 `public-dubbo`。

## Mapping CAS 告警

典型服务端日志：

```text
[cas-publish-config-fail] casMd5 = 0, server md5 may have changed
```

已核验的时间顺序表明：

1. 第一个线程成功创建 service mapping。
2. Provider 元数据成功发布。
3. 第二个线程仍携带 `casMd5=0`，按“记录不存在”重复创建 mapping。
4. Nacos 正确拒绝重复创建。
5. 客户端随后成功读取 mapping，Provider 和健康实例均存在。

因此启动阶段零星的 mapping CAS 冲突属于 Dubbo 3.3.6 并发重复发布产生的非致命幂等竞争，不代表 Nacos 地址、密码、权限或整体注册失败。

### 可以忽略的条件

- mapping 最终存在且内容正确。
- Provider 元数据存在。
- 对应 Dubbo 服务实例为 `healthy: true`。
- Provider 元数据没有持续失败。
- 消费者能够发现并调用 Provider。

### 必须处理的条件

- 同一 `dataId` 在启动完成后仍周期性失败。
- mapping 缺失或指向错误应用。
- Provider 元数据或健康实例缺失。
- 消费者出现 `No provider available`。

不要为了消除日志直接删除 `public-dubbo` 元数据、关闭 mapping 或修改 Nacos 数据库。若只需降低告警噪音，应先在测试环境验证更新的 Dubbo `3.3.x` 补丁版本。

## `invokers: 0` 与 CAS 的区别

`No provider available`、`invokers: 0[]` 是实际运行时依赖缺失，不能按 CAS 告警忽略。2026-08-13 的 `PurchaseOrderFeignClient` 故障是 `mall4cloud-order` 未启动，详见 [[故障记录#Order Provider 未启动]]。