# Mall4j 测试环境访问地址及账号密码

> 更新时间：2026-08-04  
> 环境地址：192.168.0.252  
> 访问方式：测试环境不配置域名和 HTTPS，统一通过 IP、端口或二级路径访问。  
> 安全提示：本文包含测试环境明文凭据，仅限项目成员使用，请勿提交到 Git、聊天群或外部文档平台。

## 一、商城各端入口与登录账号

| 端           | 访问地址                                  | 登录账号               | 登录密码                | 当前状态                       |
| ----------- | ------------------------------------- | ------------------ | ------------------- | -------------------------- |
| 导航首页        | http://192.168.0.252/                 | 无                  | 无                   | 可访问                        |
| 平台管理端（零售场景） | http://192.168.0.252/platform/        | <code>admin</code> | <code>123456</code> | 已通过实时数据库 BCrypt 哈希验证       |
| 平台客服工作台     | http://192.168.0.252/platform/im-box  | 与平台端共用             | 与平台端共用              | Platform 内部路由，不是独立服务       |
| 商家管理端       | http://192.168.0.252/multishop/       | 未初始化               | 未初始化                | 当前数据库无商家账号，需由平台端创建店铺及管理员   |
| 商家客服工作台     | http://192.168.0.252/multishop/im-box | 与商家端共用             | 未初始化                | Multishop 内部路由，不是独立服务      |
| 商家移动端 H5    | http://192.168.0.252/multishop-h5/    | 与商家端共用             | 未初始化                | 当前数据库无商家账号                 |
| 用户 H5       | http://192.168.0.252/h5/              | 未初始化               | 未初始化                | 当前数据库无普通用户，可按业务流程注册测试账号    |
| PC 用户端      | http://192.168.0.252/pc/              | 与用户端共用             | 未初始化                | 当前数据库无普通用户                 |
| 门店端         | http://192.168.0.252/station/         | 未初始化               | 未初始化                | 当前数据库无门店账号，需先创建店铺、门店及门店员工  |
| 供应商端        | http://192.168.0.252/supplier/        | 未初始化               | 未初始化                | 当前数据库无供应商账号，需由平台端创建供应商及管理员 |

补充说明：

- 数据库中还存在一个采购场景平台管理员，账号同为 <code>admin</code>，密码同上；当前部署未提供独立采购平台前端入口。
- 商家、供应商、门店、用户端不能使用平台管理员账号登录，不同端由 sys_type 隔离。
- 创建商家、供应商或门店账号后，应及时把新账号补充到本文档。

## 二、API 与 Workflow

| 服务 | 访问地址 | 认证说明 |
| --- | --- | --- |
| Nginx API 统一入口 | http://192.168.0.252/api/ | 业务接口按各端 Token 鉴权，无统一人工登录账号 |
| Gateway 直连 | http://192.168.0.252:8000/ | 根路径返回 404 属于正常现象 |
| Workflow UI（Gateway） | http://192.168.0.252/api/mall4cloud_workflow/warm-flow-ui/index.html | 无独立 Workflow 账号，业务操作沿用商城权限体系 |
| Workflow UI（直连） | http://192.168.0.252:9117/warm-flow-ui/index.html | 仅用于测试和排障 |

## 三、中间件控制台与连接凭据

| 服务 | 地址 | 用户名/身份 | 密码/凭据 | 说明 |
| --- | --- | --- | --- | --- |
| SSH | <code>192.168.0.252:22</code> | <code>root</code> | <code>rootroot</code> | 测试服务器管理账号 |
| Nacos 控制台 | http://192.168.0.252:8848/nacos/ | <code>nacos</code> | <code>admin123</code> | 注册中心、配置中心 |
| XXL-Job 控制台 | http://192.168.0.252:8090/xxl-job-admin/ | <code>admin</code> | <code>admin123</code> | 已通过实际登录接口验证 |
| RocketMQ Dashboard | http://192.168.0.252:8180/ | 无 | 无 | 当前配置关闭登录认证 |
| MinIO Console | http://192.168.0.252:9001/ | <code>admin</code> | <code>admin123</code> | 对象存储管理 |
| MinIO API | http://192.168.0.252:9000/ | <code>admin</code> | <code>admin123</code> | S3 兼容 API |
| Elasticsearch | http://192.168.0.252:9200/ | <code>elastic</code> | <code>elastic123</code> | 未认证访问返回 401 属于正常现象 |
| WuKongIM API | http://192.168.0.252:5001/ | <code>Manager Token</code> | <code>wukong123</code> | 作为请求 Token 使用，不是网页登录账号 |
| WuKongIM WebSocket | ws://192.168.0.252:5200/ | 业务用户 Token | 由商城登录流程签发 | WebSocket 连接入口 |
| MySQL | <code>192.168.0.252:3306</code> | <code>root</code> | <code>root</code> | 商城及中间件数据库 |
| Redis | <code>192.168.0.252:6379</code> | 无用户名 | <code>redis123</code> | 使用 AUTH 密码 |
| MongoDB | <code>192.168.0.252:27017</code> | <code>root</code> | <code>root</code> | 认证库使用 <code>admin</code> |
| Seata | <code>192.168.0.252:8091</code> | 无人工控制台账号 | 无 | 分布式事务服务端口 |
| RocketMQ Nameserver | <code>192.168.0.252:9876</code> | 无 | 无 | 名称服务端口 |
| RocketMQ Broker | <code>192.168.0.252:10911</code> | 无 | 无 | Broker 主端口 |

不在本文档中记录 Nacos 服务端身份密钥、Nacos Auth Token 和 XXL-Job Executor Access Token；这些服务间密钥只保存在服务器受限 .env 中。

## 四、资源访问地址

| 资源 | 地址 | 说明 |
| --- | --- | --- |
| 商城公开资源前缀 | http://192.168.0.252:9000/mall4cloud | 图片和文件资源访问前缀 |
| MinIO Console | http://192.168.0.252:9001/ | <code>admin</code> | <code>admin123</code> | 对象存储管理 |

## 五、后端服务直连端口

| 服务 | HTTP 地址 | 服务 | HTTP 地址 |
| --- | --- | --- | --- |
| Gateway | http://192.168.0.252:8000/ | Auth | http://192.168.0.252:9101/ |
| Product | http://192.168.0.252:9104/ | User | http://192.168.0.252:9105/ |
| Order | http://192.168.0.252:9106/ | Search | http://192.168.0.252:9108/ |
| Marketing | http://192.168.0.252:9109/ | Admin | http://192.168.0.252:9112/ |
| Payment | http://192.168.0.252:9113/ | Group | http://192.168.0.252:9114/ |
| Seckill | http://192.168.0.252:9115/ | Flow | http://192.168.0.252:9116/ |
| Workflow | http://192.168.0.252:9117/ | Biz | http://192.168.0.252:9118/ |

后端直连端口主要用于联调与排障；前端业务请求统一使用 http://192.168.0.252/api/。

## 六、账号核对结果

- 平台零售管理员：已确认存在并启用，密码已用实时 BCrypt 哈希验证。
- 平台采购管理员：已确认存在并启用，与零售管理员使用相同账号和密码。
- XXL-Job 管理员：已通过实际登录接口验证。
- 商家、供应商、门店、普通用户：实时数据库账号数量均为 0，因此本文档不填写猜测账号。
- 中间件账号：用户名和密码取自服务器 /data/mall4cloud/deploy/mall4j-test-env/.env 的实时配置。