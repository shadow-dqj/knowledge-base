---
title: Dubbo Mapping CAS
type: incident-guide
tags: [dubbo, nacos, cas]
updated: 2026-08-13
server: 192.168.1.117
status: known-warning
---

# Dubbo Mapping CAS

[[故障记录索引|← 故障索引]] · [[Ubuntu服务器/02-平台服务/Nacos]] · [[Order Provider缺失]]

## 典型日志

```text
[cas-publish-config-fail] casMd5 = 0, server md5 may have changed
```

## 已确认机制

1. 第一个线程发现 mapping 不存在并成功创建。
2. Provider metadata 成功发布。
3. 第二个线程仍携带 `casMd5=0` 重复创建。
4. Nacos 拒绝第二次创建。
5. 客户端随后可读取 mapping。

这是 Dubbo 3.3.6 启动期并发/重复发布产生的幂等竞争。

## 可以忽略

- mapping 最终存在且内容正确。
- Provider metadata 存在。
- Nacos 有 healthy/enabled 服务实例。
- 消费者能发现并调用 Provider。
- 失败集中在启动阶段且不是同一 dataId 持续循环。

## 必须处理

- mapping/metadata/实例任一缺失。
- 同一 dataId 启动后持续失败。
- 消费者出现 `No provider available`。
- Provider metadata 发布本身失败。

> [!danger] 不要这样做
> 不要删除 `public-dubbo`、关闭 mapping 或直接改 Nacos 数据库来消除日志。

## 后续优化

在测试环境验证更新的 Dubbo 3.3.x 补丁版本，并检查重复扫描/重复导出。