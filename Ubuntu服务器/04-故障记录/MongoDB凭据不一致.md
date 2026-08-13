---
title: MongoDB 凭据不一致
type: incident
tags: [mongodb, 凭据, product]
updated: 2026-08-13
server: 192.168.1.117
status: resolved
---

# MongoDB 凭据不一致

[[故障记录索引|← 故障索引]] · [[Ubuntu服务器/02-平台服务/MongoDB]] · [[Ubuntu服务器/02-平台服务/Nacos]]

> [!check] 状态
> 已解决并验证；发生于 2026-08-13。

## 现象

`mall4cloud-product` 创建 `MongoTemplate` 失败：

```text
Exception authenticating MongoCredential
mechanism=SCRAM-SHA-256
userName=mall4cloud
source=mall4cloud
```

Tomcat 同时提示 DNS AddressChangeListener 线程未停止。

## 影响

Product ApplicationContext 启动失败；相关 Dubbo Provider 和业务任务不可用。

## 根因

- MongoDB 服务、用户、认证库和 SCRAM 机制正常。
- 初始化脚本中的应用密码认证成功。
- Nacos Product 配置中的密码与数据库实际密码不一致。
- 本机没有环境变量覆盖。

DNS 线程警告是启动失败后的清理副作用，不是根因。

## 修复

通过 Nacos API 同步 8 个服务、两套命名空间共 16 条 MongoDB 密码配置，未重置数据库用户。

## 验证

- 16/16 配置密码一致。
- 8/8 空 tenant 与 `public` 配置对一致。
- MD5 mismatch 为 0。
- MongoDB 应用认证 PASS。
- Nacos/MongoDB healthy。
- Product 后续成功注册为 `192.168.1.100:9504`。

## 预防

- 账号初始化与 Nacos 配置使用同一权威值。
- 凭据变更同时验证数据库和客户端。
- 修改前备份 Nacos；路径见 [[Ubuntu服务器/03-运维手册/备份与升级#已有备份]]。