## 1. Azure的计算除了VM还包含哪些（具体问了Azure function）

| 产品名称                                | 类型            | 简介                             | 使用场景               |
| ----------------------------------- | ------------- | ------------------------------ | ------------------ |
| **Azure App Service**               | PaaS          | 托管 Web 应用、API、移动后端，无需管理基础设施    | 网站、Web API、轻量业务系统  |
| **Azure Functions**                 | Serverless    | 事件驱动的函数级计算平台，自动弹性扩展            | 定时任务、事件响应、Webhook  |
| **Azure Container Instances (ACI)** | PaaS          | 运行容器应用的轻量平台，支持快速启动和停止          | 短时任务、容器测试          |
| **Azure Kubernetes Service (AKS)**  | CaaS          | 托管 Kubernetes 集群，适用于微服务、大型容器平台 | 微服务、DevOps、大型 SaaS |
| **Azure Batch**                     | PaaS          | 批量任务调度和大规模并行计算平台               | 高性能计算（HPC）、渲染、模型训练 |
| **Azure Service Fabric**            | PaaS          | 支持微服务和有状态服务的分布式系统平台            | 大规模微服务、有状态服务       |
| **Azure Spring Apps**               | PaaS          | 托管 Spring Boot 应用              | Java 应用开发人员        |
| **Azure Container Apps**            | Serverless 容器 | 无需管理基础设施即可运行容器，支持 KEDA 弹性伸缩    | 微服务、事件驱动架构         |
| **Azure Dedicated Hosts**           | IaaS          | 提供物理服务器专属租用，满足合规和隔离要求          | 安全合规、许可绑定          |
| **Azure Virtual Desktop**           | DaaS          | 云端桌面环境，提供远程办公能力                | 企业远程办公、教育平台        |

Azure Functions 是一种无服务器（Serverless）计算平台，可以编写小型的函数来响应事件，如 HTTP 请求、定时器、消息队列等。它自动弹性扩展，无需管理服务器或底层架构。
| 特性             | 说明                                             |
| -------------- | ---------------------------------------------- |
| **事件驱动**       | 支持触发器（Trigger）如 HTTP 请求、Blob 创建、队列消息、定时器等      |
| **按需计费**       | 消耗多少计算资源就付费多少（按执行时间和次数计费）                      |
| **自动扩展**       | 根据负载自动横向扩展（并发处理）                               |
| **支持多种语言**     | C#, JavaScript, Python, Java, PowerShell 等     |
| **无状态设计**      | 默认函数是无状态的（可用 Durable Functions 实现状态机）          |
| **与Azure服务集成** | 可直接连接 Blob、Cosmos DB、Event Hub、Service Bus 等服务 |
| **可开发复杂工作流**   | 可通过 **Durable Functions** 实现有状态的长流程任务，如审批流、编排  |

✅ 什么时候用 AKS？什么时候用 VM？
| 场景                       | 建议使用     |
| ------------------------ | -------- |
| 传统应用、非容器化                | Azure VM |
| 自建数据库、高性能计算等需要底层控制       | Azure VM |
| 微服务架构、希望自动扩展             | AKS      |
| 希望 DevOps + CI/CD + 自动部署 | AKS      |
| 云原生开发、需要弹性部署容器           | AKS      |


| 对比项          | Azure VM              | AKS                                  |
| ------------ | --------------------- | ------------------------------------ |
| **基础设施管理**   | 用户需手动管理 VM、OS、打补丁     | Azure 托管控制平面，只需维护节点                  |
| **部署方式**     | 应用通常直接部署在 VM 上（手动或脚本） | 应用以容器形式部署，声明式管理                      |
| **弹性扩展**     | 手动添加/删除 VM            | 自动水平扩展 Pod / 节点                      |
| **高可用性**     | 自行设计负载均衡、故障转移机制       | Kubernetes 内建负载均衡、重启、滚动升级            |
| **环境一致性**    | 开发/测试/生产环境差异大         | 使用容器，环境一致性强                          |
| **资源利用率**    | 单机资源隔离差，容易浪费          | 容器共享主机，资源利用率高                        |
| **CI/CD 集成** | 自行构建或脚本化              | 与 DevOps 工具链（GitHub Actions、ADO）深度集成 |
| **学习曲线**     | 低                     | 高，需要理解 Kubernetes 架构                 |
| **成本控制**     | 按 VM 数量和规格计费          | 控制面免费，仅按节点和负载计费（支持自动缩容）              |

## 2.常见微服务架构类型
1. API Gateway 架构
概念：所有客户端请求先经过一个统一入口（API 网关），再转发给后端各个微服务。

优点：安全、集中路由、统一身份验证、负载均衡。

典型产品：Azure API Management, Kong, NGINX, Istio Ingress Gateway

Client ──▶ API Gateway ──▶ Service A
                        └─▶ Service B
                        └─▶ Service C
2. Service Mesh 架构
概念：使用“sidecar代理”自动管理服务间通信（流量控制、加密、重试等），核心组件是数据面和控制面。

典型产品：Istio, Linkerd, Open Service Mesh (OSM), Consul

Service A ──▶ Sidecar A ─┐
                         ├──▶ Service Mesh Proxy ──▶ Service B
Service C ──▶ Sidecar C ─┘
3. Event-Driven 架构（EDA）
概念：服务之间通过消息队列或事件总线进行异步通信，解耦依赖关系。

优势：高扩展性、容错性强。

典型产品：Azure Event Grid, Azure Service Bus, Kafka, RabbitMQ


Order Service ──▶ publish "OrderPlaced"
Inventory Service ◀── subscribes to "OrderPlaced"
Email Service ◀───────┘
4. Serverless 微服务架构
概念：使用 Azure Functions、AWS Lambda 等无服务器平台来部署每个微服务，自动弹性扩展。

优点：按需付费、无需管理底层架构。

适用场景：轻量微服务、事件驱动型系统。

5. 分布式数据库架构
概念：每个微服务拥有自己的数据库（Database per service），避免共享数据库。

优势：数据自治、强解耦，但会有数据一致性挑战。

Service A ──▶ Database A
Service B ──▶ Database B

## 3.云计算存储
| 存储服务                             | 类型            | 说明                        | 常见用途                          |
| -------------------------------- | ------------- | ------------------------- | ----------------------------- |
| **Azure Blob Storage**           | 对象存储          | 存储非结构化数据，如图片、视频、日志、备份     | 静态资源、备份、AI训练数据、大文件            |
| **Azure File Storage**           | 文件存储（SMB/NFS） | 云端共享文件夹，支持 SMB / NFS 协议挂载 | 跨平台共享文件、Lift & Shift、应用配置文件   |
| **Azure Disk Storage**           | 块存储           | 为 Azure VM 提供高性能持久化磁盘     | VM 系统盘 / 数据盘、数据库存储            |
| **Azure Queue Storage**          | 消息队列          | 简单可靠的消息存储与传输              | 微服务异步解耦、任务排队                  |
| **Azure Table Storage**          | NoSQL 表存储     | 键-属性对结构的 NoSQL 存储，支持快速查询  | 轻量级元数据存储、IoT、日志系统             |
| **Azure Archive Storage**        | 冷存储（Blob 层）   | 超低成本存储，非常适合长期归档           | 合规归档、录像、审计日志                  |
| **Azure NetApp Files**           | 企业级文件存储       | 高性能、低延迟、POSIX 兼容的文件共享     | SAP HANA、HPC、大型企业文件系统         |
| **Azure Data Lake Storage Gen2** | 分析型存储         | 大数据分析优化的 Blob 存储（兼容 HDFS） | Spark、Synapse、Databricks 计算引擎 |

| Blob 类型         | 是否可修改                | 是否支持并发上传  | 最大容量         | 使用场景       |
| --------------- | -------------------- | --------- | ------------ | ---------- |
| **Block Blob**  | 可覆盖整个 blob 或某些 block | ✅ 支持并发上传  | 高达 190.7 TiB | 文本/视频/模型文件 |
| **Append Blob** | 只能追加写入               | ❌ 不支持并发写入 | 195 GiB      | 日志记录       |
| **Page Blob**   | 支持随机读写               | ✅ 支持      | 8 TiB        | VM磁盘、数据库   |

## 3.Azure files是什么

Azure Files 是一个 可通过 SMB/NFS 挂载的共享文件系统，适用于 Lift-and-Shift 应用、配置共享、日志存储等多种场景

| 特性         | 描述                                                      |
| ---------- | ------------------------------------------------------- |
| **支持标准协议** | SMB（2.1 / 3.0 / 3.1.1） 和 NFS（4.1）                       |
| **支持挂载路径** | Windows、Linux、macOS、容器、Azure VM、本地服务器                   |
| **持久存储**   | 文件永久保存在 Azure 存储中                                       |
| **支持身份验证** | 支持 Azure AD DS、Storage account key、RBAC、NTFS ACLs（权限控制） |
| **可快照与备份** | 支持文件快照、软删除、定期备份                                         |
| **跨区域访问**  | 支持 geo-redundant 存储（GRS）和 zone-redundant 存储（ZRS）        |
| **集成备份服务** | 与 Azure Backup 集成，支持文件级还原                               |

| 特性   | Azure Files    | Azure Blob          | Azure Disk    |
| ---- | -------------- | ------------------- | ------------- |
| 协议   | SMB / NFS      | HTTP/HTTPS REST API | iSCSI-like 接入 |
| 挂载方式 | 共享挂载（多个实例）     | 仅 API 访问            | 绑定到单个 VM      |
| 使用方式 | 文件系统挂载         | 对象存储                | 块级访问          |
| 典型场景 | 文件共享 / 配置 / 日志 | 备份 / 静态文件           | VM 系统盘 / 数据盘  |

SMB 和 NFS 都是网络中文件共享的协议：

SMB 主要用于 Windows 系统；

NFS 主要用于 Linux / UNIX 系统。

| 特性       | SMB                             | NFS                   |
| -------- | ------------------------------- | --------------------- |
| 协议适用系统   | Windows 优先                      | Linux / UNIX 优先       |
| 安全性      | 支持 Kerberos、NTLM、TLS            | 通常基于 IP 或 UID/GID     |
| 文件权限控制   | NTFS ACL，支持细粒度                  | 基于 UNIX 权限            |
| 挂载方式     | `\\server\share`                | `mount server:/share` |
| 使用场景     | 共享文件夹、打印机                       | 高性能并发访问、容器挂载          |
| Azure 支持 | Azure Files SMB、Azure File Sync | Azure Files NFS 4.1   |

## 4.数据库的种类有哪些
| 类型                        | 简介                 | 典型代表                               | 适用场景             |
| ------------------------- | ------------------ | ---------------------------------- | ---------------- |
| **关系型数据库（RDBMS）**         | 基于表格结构，使用 SQL 查询   | MySQL、PostgreSQL、SQL Server、Oracle | 企业系统、财务、ERP、网站后台 |
| **键值数据库（Key-Value）**      | 数据是键值对形式，极致快速      | Redis、DynamoDB                     | 缓存、排行榜、会话管理      |
| **文档数据库（Document）**       | 数据是 JSON 或 BSON 文档 | MongoDB、Couchbase                  | 内容管理、购物车、移动App   |
| **列式数据库（Column-based）**   | 按列存储，适合分析查询        | Cassandra、HBase、Azure Synapse      | 大数据分析、OLAP       |
| **图数据库（Graph）**           | 用节点和边存储数据          | Neo4j、Amazon Neptune               | 社交网络、推荐系统、知识图谱   |
| **时序数据库（Time Series）**    | 针对时间序列数据优化         | InfluxDB、TimescaleDB、Prometheus    | IoT、监控、指标采集      |
| **全文搜索引擎（Search Engine）** | 支持全文索引和搜索          | Elasticsearch                      | 日志、全文搜索、电商搜索引擎   |

| Azure 服务名称                        | 数据库类型         | 对应开源/商业产品                      |
| --------------------------------- | ------------- | ------------------------------ |
| **Azure SQL Database**            | 关系型           | SQL Server                     |
| **Azure Database for MySQL**      | 关系型           | MySQL                          |
| **Azure Database for PostgreSQL** | 关系型           | PostgreSQL                     |
| **Azure Cosmos DB**               | 多模型（文档、键值、图等） | 类似 MongoDB、Cassandra、Gremlin 等 |
| **Azure Cache for Redis**         | 键值缓存          | Redis                          |
| **Azure Synapse Analytics**       | 分析型（列存）       | 类似 Redshift、Snowflake          |
| **Azure Data Explorer (Kusto)**   | 时序/日志分析       | 类似 InfluxDB、Elasticsearch      |

一、核心目标
主库（Master）：负责处理写操作

从库（Slave）：负责处理只读请求，并从主库同步数据

🚀 目标：主库写入的数据可以实时或准实时地同步到从库，实现数据一致性和分离读写压力。

| 阶段                             | 位置     | 描述                                                |
| ------------------------------ | ------ | ------------------------------------------------- |
| **1. 写入 binlog（主库）**           | Master | 主库将写操作记录到 **binary log（二进制日志）** 中                 |
| **2. 读取 binlog（从库 IO 线程）**     | Slave  | 从库启动 IO 线程连接主库，读取 binlog 并保存到 **中继日志（relay log）** |
| **3. 执行 relay log（从库 SQL 线程）** | Slave  | 从库启动 SQL 线程，读取 relay log 并**执行相同的写操作**，保持数据一致     |

| 架构模式     | 描述                 |
| -------- | ------------------ |
| 一主一从     | 最基本架构，读写分离         |
| 一主多从     | 多个从库扩展读能力          |
| 主主复制（双主） | 双向同步，但要处理冲突，需慎用    |
| 主从链式复制   | A -> B -> C，适合分层同步 |
| 半同步复制    | 提高同步安全性，从库确认后主库才提交 |

- Redis 是一个开源的高性能键值数据库，也可以理解为一个内存数据结构存储系统，常用于缓存、分布式锁、消息队列等高频读写场景。
  | 特性                   | 说明                                                        |
| -------------------- | --------------------------------------------------------- |
| **内存存储，超快读写**        | 以 RAM 作为主要存储介质，读写速度达到百万 QPS                               |
| **多数据结构**            | 不只是字符串，还支持 List、Hash、Set、ZSet、Bitmap、HyperLogLog、Stream 等 |
| **支持持久化**            | 可以将数据定期保存到磁盘（RDB/AOF），重启后恢复                               |
| **支持发布/订阅（Pub/Sub）** | 可作为轻量消息队列使用                                               |
| **支持分布式部署**          | 支持哨兵（Sentinel）、主从复制、集群模式                                  |
| **原子操作 & 事务**        | 所有单条命令是原子性的，支持事务（MULTI/EXEC）                              |
| **轻量级**              | 单个进程，单线程模型，部署和使用非常简单                                      |

| 特性    | Redis       | MySQL       |
| ----- | ----------- | ----------- |
| 存储介质  | 内存（为主）      | 磁盘（为主）      |
| 数据结构  | 丰富（键值、集合等）  | 表结构（行、列）    |
| 操作速度  | 极快（纳秒级）     | 慢（毫秒级）      |
| 持久化保障 | 可选，弱于数据库    | 强一致、写入即持久   |
| 使用场景  | 缓存、队列、分布式系统 | 业务数据存储、事务支持 |

- 常见使用场景
  | 场景             | 描述                           |
| -------------- | ---------------------------- |
| **缓存系统**       | 提升数据库读写性能，如商品详情缓存、热点数据缓存     |
| **分布式锁**       | 保证多个服务对共享资源的同步访问（SETNX + EX） |
| **消息队列**       | 使用 List 或 Stream 实现轻量异步消息系统  |
| **排行榜系统**      | 使用 ZSet 存储排名和分数              |
| **限流和计数器**     | 秒杀系统、IP 限流、点赞数等操作频率限制        |
| **Session 存储** | 登录态、用户会话信息存 Redis（比数据库快）     |

✅ 企业中的实际例子
淘宝：缓存商品详情页、控制活动限流、防止重复下单

知乎/微博：话题热榜、阅读数排行榜、缓存内容

滴滴/美团：用户 session、计费配置、任务调度

游戏公司：玩家在线状态、排行榜、活动锁定

“Redis 你具体用来干嘛”，可以选 缓存 + 分布式锁 + 排行榜 + 限流 四个作为主答方向。
| 场景   | Redis 类型      | 用法         |
| ---- | ------------- | ---------- |
| 缓存系统 | String / Hash | 存储页面、用户信息等 |
| 排行榜  | ZSet          | 分数排名       |
| 消息队列 | List / Stream | 推送任务 / 日志  |
| 分布式锁 | String + NX   | 控制并发访问     |
| 限流防刷 | String + INCR | 每分钟访问次数计数  |
| 活跃统计 | Bitmap        | 标记某日是否访问   |
| 去重计数 | HyperLogLog   | UV / 访问去重  |
| 配置中心 | String / Hash | 存储动态配置参数   |

## 5.application gateway可以除了流量分发以外还有什么功能
1. Web 应用防火墙（WAF）
集成 WAF，可开启 OWASP Core Rule Set (CRS)

防止 SQL 注入、XSS、命令注入等攻击

支持自定义规则（IP 黑名单、请求路径规则等）

2. 基于路径/主机名的路由（URL-based Routing & Host-based Routing）
/api/* 走一组后端，/images/* 走另一组

a.contoso.com 和 b.contoso.com 路由到不同后端池

🌐 用于微服务、多个子系统共享同一入口。

3. SSL 卸载（SSL Termination）
可以在 Application Gateway 层解密 TLS 流量

后端服务器不需要处理 HTTPS，提高性能

支持上传证书（.pfx），也可配置 Key Vault 自动管理证书

🔐 降低后端系统的 TLS 成本，集中管理证书。

4. 重定向规则（Redirect Rules）
支持 HTTP 转 HTTPS 强制跳转

支持 301/302 重定向到其他 URL 路径或主机名

🚦 可统一安全接入入口，简化客户端访问逻辑。

5. 会话保持（Session Affinity / Cookie-based）
使用 Gateway-Managed Cookie（Affinity Cookie）保持用户请求始终命中同一后端

适用于有状态应用（如购物车）

🛒 避免购物中断、会话丢失。

## 6.vnet如何划分（/24是256 个 IP）
| 子网名称                     | 用途                          | 示例IP段                                |
| ------------------------ | --------------------------- | ------------------------------------ |
| **subnet-app**           | 应用服务器                       | 10.0.1.0/24                          |
| **subnet-db**            | 数据库，仅内部通信                   | 10.0.2.0/24                          |
| **subnet-bastion**       | Bastion / Jumpbox           | 10.0.3.0/27                          |
| **subnet-azurefirewall** | 专属子网，用于部署 Azure Firewall    | 10.0.4.0/24                          |
| **subnet-gateway**       | VPN/ExpressRoute Gateway 子网 | 10.0.255.0/27（必须命名为 `GatewaySubnet`） |

## 7.消息中间件
在 Azure 中，“中间件”是指位于应用程序和操作系统/数据库/网络服务之间的一层软件，用于处理通信、消息传递、事务、队列、缓存等功能，帮助构建 分布式、高可用、可扩展的云原生系统。
| 类别            | Azure 服务                                 | 对应中间件作用                      |
| ------------- | ---------------------------------------- | ---------------------------- |
| **消息队列**      | **Azure Service Bus**                    | 企业级消息队列，支持持久化、顺序、事务、死信队列     |
| **事件总线**      | **Azure Event Grid**                     | 事件驱动架构（EDA）的核心组件，轻量、广播型      |
| **数据流管道**     | **Azure Event Hubs**                     | 高吞吐流式数据管道，用于日志、IoT、实时分析      |
| **缓存**        | **Azure Cache for Redis**                | 键值型缓存中间件，加速系统响应、做分布式锁        |
| **配置管理**      | **Azure App Configuration**              | 管理应用配置和 Feature Flag 的中间件服务  |
| **API 管理**    | **Azure API Management**                 | 网关中间件，用于流量控制、认证、限流、版本管理      |
| **服务发现 / 网关** | **Azure Application Gateway**            | 第七层流量转发，支持 WAF、路由、中转等        |
| **工作流中间件**    | **Azure Logic Apps / Durable Functions** | 自动化流程、任务编排、状态管理等             |
| **日志与监控**     | **Azure Monitor / Application Insights** | 提供应用运行中的日志、指标和依赖追踪           |
| **身份认证中间件**   | **Microsoft Entra ID / B2C**             | 提供 SSO、OAuth2.0、OIDC 等认证协议支持 |

## 8.Azure安全产品有哪些
一、身份与访问管理类
| 产品名称                                     | 作用                                   |
| ---------------------------------------- | ------------------------------------ |
| **Microsoft Entra ID**（原 Azure AD）       | 云端身份认证，支持 SSO、MFA、RBAC、OAuth2/OpenID |
| **Microsoft Entra ID P2**                | 高级身份保护（如风险登录检测、条件访问策略）               |
| **Microsoft Entra Domain Services**      | 提供域控服务（AD DS），用于传统应用兼容性              |
| **Privileged Identity Management (PIM)** | 临时权限提升，管理特权账号                        |
| **Conditional Access**                   | 条件访问控制（如地理位置、设备合规性）                  |

二、网络安全类
| 产品名称                                | 作用                                                   |
| ----------------------------------- | ---------------------------------------------------- |
| **Azure Firewall**                  | 第四层 & 第七层防火墙，集中式管理出入站流量                              |
| **Azure DDoS Protection**           | 防御
分布式拒绝服务攻击，自动检测 & 缓解                                |
| **Network Security Group (NSG)**    | 子网/网络接口级别的包过滤（ACL）                                   |
| **Web Application Firewall (WAF)**  | Web 应用防护（SQL 注入、XSS 等），与 App Gateway / Front Door 集成 |
| **Private Endpoint / Private Link** | 将 PaaS 服务接入 VNet，实现私有访问                              |
| **VPN Gateway / ExpressRoute**      | 加密连接、专线传输                                            |

三、威胁防护 & 监控类
| 产品名称                                                              | 作用                                      |
| ----------------------------------------------------------------- | --------------------------------------- |
| **Microsoft Defender for Cloud**（前 Azure Security Center）         | 安全评分、威胁检测、建议修复、合规评估                     |
| **Microsoft Defender for Endpoint / SQL / Storage / Key Vault 等** | 针对具体服务的入侵检测、异常行为拦截                      |
| **Microsoft Sentinel**                                            | 云原生 SIEM + SOAR，集中日志分析、威胁告警、自动响应        |
| **Azure Monitor + Log Analytics**                                 | 采集安全相关日志（如 NSG Flow Logs、Activity Logs） |
| **Security Insights Workbook**                                    | 可视化仪表盘展示安全状态                            |

四、数据保护类
| 产品名称                                                     | 作用                                  |
| -------------------------------------------------------- | ----------------------------------- |
| **Azure Key Vault**                                      | 管理密钥、证书、密码等机密数据，支持硬件加密模块（HSM）       |
| **Azure Disk Encryption / SQL TDE / Storage Encryption** | 加密存储、数据库透明加密                        |
| **Customer Managed Key (CMK)**                           | 使用用户自带密钥对 Azure 资源加密                |
| **Azure Information Protection (AIP)**                   | 文档级别的加密与权限管控（需配合 Microsoft Purview） |
| **Microsoft Purview**                                    | 数据发现、分类、合规审计、DLP 数据防泄露（跨服务）         |

五、合规与治理类
| 产品名称                              | 作用                              |
| --------------------------------- | ------------------------------- |
| **Azure Policy**                  | 强制策略合规（如必须启用加密、不允许公开 IP）        |
| **Azure Blueprints**              | 快速部署一套合规环境模板（包含策略、RBAC 等）       |
| **Microsoft Defender for DevOps** | 检查 CI/CD 流水线中安全问题（如 secrets 检测） |
| **Compliance Manager**            | 对照 ISO、GDPR 等法规，提供合规评分和改进建议     |

## 8.大数据的组件有哪些
采集靠 Kafka，存储靠 HDFS，计算用 Spark，查询跑 Hive，调度交给 YARN，分析靠 BI 工具。

## 9.Devops是什么 有哪些工具和流程
DevOps 是开发（Development）与运维（Operations）的结合，强调通过自动化流程、协同文化和持续交付实践，提升软件交付效率与质量。

一、DevOps 的核心目标
| 目标              | 说明                       |
| --------------- | ------------------------ |
| 🚀 持续交付（CD）     | 快速、安全地将新版本交付到生产环境        |
| 🔄 持续集成（CI）     | 开发代码及时合并测试，避免集成地狱        |
| 🧪 自动化测试        | 每次变更都自动验证正确性             |
| ☁️ 基础设施即代码（IaC） | 通过代码部署环境，标准化、可重复         |
| 📈 可观测性         | 全面监控系统状态与部署效果            |
| 🤝 跨团队协作        | 打破 Dev 与 Ops 的隔阂，共同为交付负责 |

| 阶段                | 工具                                                                | 说明                |
| ----------------- | ----------------------------------------------------------------- | ----------------- |
| 📋 计划与协作          | **Azure Boards, Jira, Trello, GitHub Projects**                   | 需求管理、任务跟踪、团队协作    |
| 🧑‍💻 版本管理        | **Git, GitHub, GitLab, Azure Repos**                              | 代码版本控制、分支管理       |
| ⚙️ 构建集成（CI）       | **GitHub Actions, Azure Pipelines, Jenkins, GitLab CI**           | 自动构建、单元测试、制品生成    |
| 🧪 自动化测试          | **Selenium, Postman, JUnit, PyTest**                              | 自动化回归测试、接口测试      |
| 📦 制品管理           | **Azure Artifacts, JFrog Artifactory, Nexus**                     | 存储构建产物（包、镜像）      |
| 🚀 部署发布（CD）       | **Azure Pipelines, Argo CD, Spinnaker**                           | 自动部署到测试、生产环境      |
| ☁️ IaC / 环境管理     | **Terraform, Bicep, ARM, Ansible**                                | 基础设施即代码，自动部署环境    |
| 🔍 监控与日志          | **Azure Monitor, Prometheus, Grafana, ELK, Application Insights** | 系统性能、异常监控、用户行为分析  |
| 🛡️ 安全（DevSecOps） | **SonarQube, Snyk, Microsoft Defender for DevOps**                | 代码安全扫描、漏洞检查、CI 保护 |

每次提交代码 → 自动触发构建 → 自动跑测试 → 自动部署到 Dev 环境 → 自动通知团队

每日构建快照 → 部署到测试环境 → QA 验收 → 通过后发布到生产

基础设施用 Terraform 生成 → 自动部署资源组 / AKS 集群 / DB → 系统上线

## 10.AI大模型
| 类型              | 描述                    | 应用场景            |
| --------------- | --------------------- | --------------- |
| **语言大模型（LLM）**  | 主要处理文本的理解与生成          | 问答、写作、翻译、对话     |
| **多模态大模型（VLM）** | 同时处理图像、文本、音频等模态       | 看图说话、图片生成、视频问答  |
| **代码大模型**       | 专注于理解和生成编程代码          | 自动补全、代码解释、写测试用例 |
| **语音模型**        | 将语音转文本 / 文本转语音 / 语音理解 | 智能语音助手、客服、字幕生成  |
