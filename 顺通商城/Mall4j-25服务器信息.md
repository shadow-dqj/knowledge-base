---
title: Mall4j-25 测试环境部署与访问清单
type: reference
tags:
  - mall4j
  - 测试环境
  - 服务器信息
  - 敏感
created: 2026-09-02
updated: 2026-09-02
resource: "192.168.0.25"
---

# Mall4j-25 测试环境部署与访问清单

> [!warning] 内部敏感资料
> 本文包含明文账号、密码和对象存储密钥，**不得提交到 Git 或对外发送**。

> **更新时间**：2026-09-02  
> **环境地址**：`192.168.0.25`  
> **发布范围**：16 个后端服务、7 个前端项目、完整测试中间件与数据  
> **生产环境使用原则**：仅以 `192.168.0.12` 为路径和配置参考，本次未修改生产环境

## 目录

1. [[#一、发布信息]]
2. [[#二、中间件访问]]
3. [[#三、项目访问]]
4. [[#四、后端服务端口]]
5. [[#五、部署路径]]
6. [[#六、数据来源与同步结果]]
7. [[#七、前端测试配置]]
8. [[#八、备份与审计位置]]
9. [[#九、验证结论]]
10. [[#十、变更与排障记录]]

## 一、发布信息

| 项目           | 内容                                                   |
| ------------ | ---------------------------------------------------- |
| 环境           | 测试环境                                                 |
| 发布日期         | 2026-09-01                                           |
| SSH 地址       | `192.168.0.25:22`                                    |
| SSH 账号       | `root`                                               |
| SSH 密码       | `rootroot`                                           |
| SSH Host Key | `SHA256:1xnn2uBVHPp1iL32Jw96p3FnH76D9criHdtATY+cs9U` |
| 发布范围         | 16 个后端服务、7 个前端项目、完整测试中间件与数据                          |

## 二、中间件访问

> [!note] MinIO 凭据区分
> MinIO Console 使用 **Root 登录**账号密码；MinIO S3 文件上传使用独立、限权的 **Service Account** Access Key/Secret Key，二者不得混用。

| 系统                 | 地址                                       | 用户名 / Access Key       | 密码 / Secret Key                            | 备注                                                      |
| ------------------ | ---------------------------------------- | ---------------------- | ------------------------------------------ | ------------------------------------------------------- |
| MySQL              | `192.168.0.25:3306`                      | `root`                 | `hn02le.34lkdLKD`                          | 业务库与本地 Docker 同步；已关闭 `ONLY_FULL_GROUP_BY`               |
| Redis              | `192.168.0.25:6379`                      | 无                      | `hn02le.34lkdLKD`                          | 生产一致密码                                                  |
| Nacos Console      | `http://192.168.0.25:8080/index.html`    | `nacos`                | `hn02le.34lkdLKD`                          | Nacos API/注册中心端口 `8848`                                 |
| XXL-JOB            | `http://192.168.0.25:8090/xxl-job-admin` | `admin`                | `hn02le.34lkdLKD`                          | 管理控制台                                                   |
| RocketMQ Dashboard | `http://192.168.0.25:8180`               | `admin`                | `hn02le.34lkdLKD`                          | NameServer `9876`，Broker `10911`                        |
| Elasticsearch      | `http://192.168.0.25:9200`               | `elastic`              | `hn02le.34lkdLKD`                          | 版本 `7.17.28`                                            |
| MongoDB            | `192.168.0.25:27017`                     | `root`                 | `hn02le.34lkdLKD`                          | 认证库 `admin`                                             |
| MinIO Console      | `http://192.168.0.25:9001`               | `admin`                | `hn02le.34lkdLKD`                          | 也可通过 `http://192.168.0.25/minio/login` 访问               |
| MinIO S3           | `http://192.168.0.25:9000`               | `M4cdb5b18f1baf11023b` | `ab938af8800fa249c232b80714da323dd8baa8de` | 实际 Service Account API 密钥；仅限 mall4cloud 桶，已通过上传、读取和删除验证 |
| MinIO 资源前缀         | `http://192.168.0.25:9000/mall4cloud`    | -                      | -                                          | 前端 `.env.testing` 已使用该地址；桶仅开放匿名对象读取，不开放列表、上传或删除         |
| Seata              | `192.168.0.25:8091`                      | -                      | -                                          | 服务端口                                                    |
| WuKongIM           | `ws://192.168.0.25:5200`                 | -                      | -                                          | HTTP 管理/接口端口 `5001`                                     |
| Canal              | 测试机内部服务                                  | MySQL 用户 `canal`       | 由容器配置管理                                    | 只发送 `canal-topic`，不挂载到所有 Topic                          |


## 三、项目访问

| 项目        | 地址                                        | 账号     | 密码          | 备注                        |
| --------- | ----------------------------------------- | ------ | ----------- | ------------------------- |
| 平台管理端     | `http://192.168.0.25/platform/`            | `admin` | `123456`    | 平台后台                     |
| 商家管理端     | `http://192.168.0.25/multishop/`            | `shuntong` | `St123456.` | 商家/品牌/店铺后台              |
| 供应商管理端    | `http://192.168.0.25/supplier/`             | 未记录   | 未记录         | 需由平台创建或重置供应商账号           |
| PC 商城     | `http://192.168.0.25/pc/`                   | 用户业务账号   | 用户业务密码       | 无统一固定用户账号                |
| 用户 H5     | `http://192.168.0.25/h5/`                   | 用户业务账号   | 用户业务密码       | 无统一固定用户账号                |
| 商家 H5     | `http://192.168.0.25/multishop-h5/`         | `shuntong` | `St123456.` | 与商家管理端账号体系一致            |
| 门店 H5     | `http://192.168.0.25/station/`              | 门店业务账号   | 门店业务密码       | 现有资料未记录统一门店密码            |
| API Gateway | `http://192.168.0.25:8000`                  | -        | -           | Nginx 同源代理前缀 `/api/`     |
| IM WebSocket | `ws://192.168.0.25:5200`                    | -        | -           | 前端测试配置已更新                |


## 四、后端服务端口

| 服务       | 端口    | 服务       | 端口    |
| -------- | -----: | -------- | -----: |
| Gateway  | `8000` | Auth     | `9101` |
| Biz      | `9104` | User     | `9105` |
| Order    | `9106` | Product  | `9108` |
| Search   | `9109` | Admin    | `9112` |
| Marketing | `9113` | Payment  | `9114` |
| Group    | `9115` | Flow     | `9116` |
| Seckill  | `9117` | Workflow | `9118` |
| Invoice  | `9119` | Alliance | `9120` |

## 五、部署路径

| 类型         | 测试环境路径                          |
| ---------- | ------------------------------ |
| Java Compose | `/data/java/docker-compose.yaml` |
| Java 服务目录 | `/data/java/<service>`          |
| Java 当前 JAR | `/data/java/<service>/<service>.jar` |
| Java 服务备份 | `/data/java/<service>/backup/`  |
| Nginx 根目录 | `/data/nginx`                   |
| 前端当前文件    | `/data/nginx/html/<project>`    |
| 前端备份根目录   | `/data/nginx/html/backup/`      |
| 中间件 Compose | `/data/mall4cloud/middleware/docker-compose.yaml` |
| 中间件数据根目录  | `/data/mall4cloud/middleware`   |
| 部署审计备份根目录 | `/data/mall4cloud/backup`       |

**后端服务目录名称：**

`mall4cloud-admin`、`mall4cloud-alliance`、`mall4cloud-auth`、`mall4cloud-biz`、`mall4cloud-flow`、`mall4cloud-gateway`、`mall4cloud-group`、`mall4cloud-invoice`、`mall4cloud-marketing`、`mall4cloud-order`、`mall4cloud-payment`、`mall4cloud-product`、`mall4cloud-search`、`mall4cloud-seckill`、`mall4cloud-user`、`mall4cloud-workflow`。

**前端目录名称：**

`platform`、`multishop`、`supplier`、`pc`、`h5`、`multishop-h5`、`station`。


## 六、数据来源与同步结果

| 数据       | 来源                                   | 测试环境结果                          |
| -------- | ------------------------------------ | ------------------------------- |
| MySQL 业务库 | 本机 Docker `mall4cloud-mysql`         | 已同步 14 个业务 Schema；未覆盖 Nacos、XXL-JOB、Seata 库 |
| MySQL 导出文件 | 本机 `.tmp/deploy-25-continue-20260901/mall4cloud-business-20260901_111746.sql.gz` | 远端 `/tmp/mall4cloud-business-20260901_111746.sql.gz`，SHA-256 `F50E06AD0727859D9FC2D7FCC0302632139BBA6EEA8F299A1F3A87628DE965CD` |
| Elasticsearch | 本机 Docker `mall4cloud-es`，缺项参考 `mall4cloud-doc` ES mapping | 12 个业务索引的 mapping 哈希与文档数逐项一致 |
| MongoDB    | 本机 Docker `mall4cloud-mongo`         | `mall4cloud`、`mall4cloud-bak` 业务库及集合已同步；运行后统计集合会继续增长 |
| RocketMQ   | 本机 Docker `mall4cloud-rmq-broker`    | 47 个业务 Topic 与本地集合一致，系统/重试 Topic 由 Broker 和消费者生成 |
| Canal      | 生产过滤范围作为参考，测试 IP 改为 `192.168.0.25` | 监听指定商品、店铺、订单、门店等表，只投递 `canal-topic` |

> Canal 新增门店链路已实测：测试 INSERT 触发 MySQL binlog，`canal-topic` 位点增长，Search 消费后通过 Order Dubbo 查询门店并创建 `station` ES 文档。延迟重试也已成功消费，测试数据库行和 ES 文档均已清理。

## 七、前端测试配置

- 7 个当前项目的 `.env.testing` 均使用 `192.168.0.25`，不使用生产域名。
- API 使用 `http://192.168.0.25:8000` 或同源 `/api`。
- WebSocket 使用 `ws://192.168.0.25:5200`。
- MinIO 资源地址使用 `http://192.168.0.25:9000/mall4cloud`。
- 天地图 Key 使用 `91a32aa91d5537e5145d9ebb21b49ba5`；已进入所有实际使用地图的测试构建。
- 已部署静态文件未发现 `192.168.0.12`、`192.168.0.252`、`192.168.1.41` 残留。

## 八、备份与审计位置

| 内容             | 位置                                                       |
| -------------- | -------------------------------------------------------- |
| 后端路径迁移备份      | `/data/mall4cloud/backup/backend-path-fix-20260901_105513` |
| MySQL 导入审计   | `/data/mall4cloud/backup/mysql-import-20260901_112315`    |
| Admin Nacos 配置备份 | `/data/mall4cloud/backup/admin-alert-key-20260901_114016` |
| Nginx 路径迁移备份    | `/data/mall4cloud/backup/nginx-path-fix-20260901_115428`  |
| RocketMQ Broker 配置备份 | `/data/mall4cloud/backup/rocketmq-broker-config-20260901_124435` |
| Java Compose 路径迁移备份 | `/data/mall4cloud/backup/java-compose-path-20260901_133412` |
| 部署前应用备份        | `/data/mall4cloud/backup/app-deploy-20260901_100948`      |
| 每服务版本备份        | `/data/java/<service>/backup/`                            |
| 前端版本备份         | `/data/nginx/html/backup/`                                |
| 旧发布元数据保留       | `/data/mall4cloud/backup/release-metadata-prune-20260901_124633` |

磁盘处理记录：清理 Docker 构建缓存和已部署的旧发布暂存副本后，根盘使用率由 96% 降至约 79%；未删除运行中镜像、容器、当前发布暂存和正式备份。

## 九、验证结论

- Nacos 健康状态为 `UP`。
- 16 个后端 HTTP 端口全部监听。
- 7 个前端入口全部返回 HTTP `200`。
- MySQL `ONLY_FULL_GROUP_BY` 已关闭。
- ES mapping/数据、MongoDB 业务数据、RocketMQ 业务 Topic 已与本地核对。
- Canal -> RocketMQ -> Search -> Elasticsearch 新增门店链路实测通过。
- RocketMQ Broker 磁盘阈值及基础参数已与生产配置对齐，测试 IP 保持 `192.168.0.25`。


## 十、变更与排障记录

### Seata 修正 (20260901_134534)

- Seata 持久化数据库已根据仓库自带的 Mall4cloud Seata 2.6 初始化 SQL 完成初始化。
- 已验证的表：branch_table、distributed_lock、global_table、lock_table、vgroup_table。
- 已确认 Seata Server 存储连通性，以及重启后 Marketing 全局事务可正常创建。
- 备份与初始化证据：`/data/mall4cloud/backup/seata-db-init-20260901_134534`

### Nginx 仅 IP 拆分配置 (20260901_154306)

- 主 include：`/data/nginx/nginx.conf` 加载 `/etc/nginx/conf.d/test-ip.conf`。
- IP server：`test-ip.conf` 定义了 `server_name 192.168.0.25 _`。
- 保留的配置：原有的 14 个文件仍保留在 `/data/nginx/conf.d`；各系统文件是 IP server 所 include 的、当前激活的 `location` 路由片段。
- 域名状态：当前没有任何激活配置包含生产域名，也不存在独立的、基于域名的 server 块。
- 外部验证：7 个前端路由、Gateway API 与 Nacos 代理均返回 HTTP 200。
- 可通过浏览器访问的 MinIO 端点：`http://192.168.0.25:9000`。
- 备份：`/data/mall4cloud/backup/nginx-ip-split-20260901_154306`。

### 咕咚 (Gudong) 测试连通性 (20260901)

- 已独立确认 Mall4j `192.168.0.25/24` 与咕咚 `192.168.0.23/24` 之间双向网络连通；两次三包 ping 检查均 0% 丢包。
- Mall4j -> 咕咚：TCP `22`、`80`、`443`、`62336`（人脸）与 `62340`（主站）可达。`GET /externalpay/notice` 通过 `https://kjzxface.stsh.vip`（本检查解析到 `192.168.0.23`）与直连 `http://192.168.0.23:62336` 均返回 `405 Allow: POST`，确认回调路由已生效。
- 咕咚 -> Mall4j：TCP `22`、`80`、`8000` 与 `9000` 可达；Mall4j 上 `443` 未监听。访问 `http://192.168.0.25/api/mall4cloud_payment/ua/external/pay/query` 与 `http://192.168.0.25/api/mall4cloud_invoice/ua/external/pay/invoice/query` 均可到达后端，并返回预期的缺参校验响应。
- Mall4j 仅 IP 模式下 Nginx API 前缀为 `/api/`。诸如 `http://192.168.0.25/mall4cloud_payment/...` 的直连路径未开放，返回 `404`。
- 咕咚 `fore-kf2` 当前将 `api.sdstsh.cn` 解析到生产 `192.168.0.12`。在接入测试前，请将咕咚测试运行时的支付/发票基础 URL 替换为 `http://192.168.0.25/api/mall4cloud_payment` 与 `http://192.168.0.25/api/mall4cloud_invoice`；不要保留生产主机覆盖。
- 若需严格按测试仅 IP 模式对接，请以 `http://192.168.0.23:62336/externalpay/notice` 作为咕咚支付通知端点。在进行端到端支付测试前，请另行确认已配置的访问令牌或 IP 白名单。

### MinIO 浏览器图片修正 (20260901_191327)

- 平台兜底地址 `/platform/static/png/def-CpHfKefR.png` 是打包内置的默认图片，并非上传端点。`img-show` 组件在原始图片请求失败后切换至该图。
- 平台源码、`.env.testing` 与已部署包均使用 `http://192.168.0.25:9000/mall4cloud`；已部署平台包中不再残留生产 `.12` 资源地址。
- 根因：`mall4cloud` 桶虽包含已同步的对象，但没有匿名桶策略，导致无签名的浏览器图片请求返回 `403`。
- 应用策略：匿名访问仅限定为 `arn:aws:s3:::mall4cloud/*` 上的 `s3:GetObject`。桶列表、匿名 PUT 与匿名 DELETE 仍返回 `403` 拒绝。
- 来自咕咚主机 `192.168.0.23` 的验证：多个真实 PNG 对象通过直连 MinIO `9000` 及保留的 Nginx `/minio/` 代理均返回 `200`；平台源端能收到预期的 CORS 头。
- 无需前端重新构建或服务重启。策略备份与验证证据：`/data/mall4cloud/backup/minio-public-read-20260901_191327`。

### Nacos 控制台防火墙修正 (20260901_194214)

- Nacos 已在 `8080` 监听并在本机返回登录页面，但 firewalld 未向其他主机开放 `8080/tcp`。
- 已在 `public` 区域的运行时与永久规则中均加入 `8080/tcp`；无需重启 Nacos 或改动其配置。
- 已从咕咚主机 `192.168.0.23` 验证：`/index.html`、主 JavaScript、主 CSS 与 Nacos 徽标均返回 HTTP `200`。
- 控制台直连地址：`http://192.168.0.25:8080/index.html#/login`。
- 防火墙状态备份：`/data/mall4cloud/backup/nacos-firewall-20260901_194214`。
