---
title: Order Provider 缺失
type: incident
tags: [dubbo, order, xxl-job, product]
updated: 2026-08-13
server: 192.168.1.117
status: dependency-missing
---

# Order Provider 缺失

[[故障记录索引|← 故障索引]] · [[Dubbo Mapping CAS]] · [[Ubuntu服务器/02-平台服务/XXL-Job]]

## 现象

Product 的 `handleSkuStockChange` 调用 `PurchaseOrderFeignClient`：

```text
No provider available
invokers: 0[]
validInvokers: 0[]
```

## 证据

- Product 已运行并以 `192.168.1.100:9504` 健康注册。
- `mall4cloud-order` Java 进程未运行。
- Nacos 中 `public-mall4cloud-order-dubbo-server` 实例数为 0。
- `PurchaseOrderFeignClient` mapping 和 Provider metadata 均不存在。
- 接口实现属于 Order 服务。

## 根因

实际 Provider 未启动，不是 Nacos connectivity filter、CAS 或路由配置问题。

## 恢复

1. 启动 `mall4cloud-order`。
2. 验证 Nacos healthy Order 实例。
3. 验证 mapping 和 Provider metadata。
4. Product 通常无需重启，等待动态发现。
5. 手动重跑失败的 `handleSkuStockChange`。

## 数据一致性风险

旧实现无论任务成功失败都会在 `finally` 推进 `SKU_CHANGE_STATISTICS_TIME`；下一次只回溯 15 秒，可能跳过失败窗口。

### 补偿

- 小数据量：执行 `intiStock` 全量对齐。
- 大数据量：恢复游标到失败前，再重跑任务。
- 未知数据量：不要直接删除游标触发全量。

## 代码预防

仅成功时推进游标；失败保留旧值并记录可重放窗口。