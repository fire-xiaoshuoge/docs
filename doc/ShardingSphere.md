# ShardingSphere

## 概览

### ShardingSphere

ShardingSphere是一套开源的分布式数据库中间件解决方案组成的生态圈，它由Sharding-JDBC、Sharding-Proxy和Sharding-Sidecar（计划中）这3款相互独立的产品组成。 他们均提供标准化的数据分片、分布式事务和数据库治理功能，可适用于如Java同构、异构语言、云原生等各种多样化的应用场景。

ShardingSphere定位为关系型数据库中间件，旨在充分合理地在分布式的场景下利用关系型数据库的计算和存储能力，而并非实现一个全新的关系型数据库。 它与NoSQL和NewSQL是并存而非互斥的关系。NoSQL和NewSQL作为新技术探索的前沿，放眼未来，拥抱变化，是非常值得推荐的。反之，也可以用另一种思路看待问题，放眼未来，关注不变的东西，进而抓住事物本质。 关系型数据库当今依然占有巨大市场，是各个公司核心业务的基石，未来也难于撼动，我们目前阶段更加关注在原有基础上的增量，而非颠覆。

![ShardingSphere Scope](https://shardingsphere.apache.org/document/current/img/shardingsphere-scope_cn.png)

### Sharding-JDBC

定位为轻量级Java框架，在Java的JDBC层提供的额外服务。 它使用客户端直连数据库，以jar包形式提供服务，无需额外部署和依赖，可理解为增强版的JDBC驱动，完全兼容JDBC和各种ORM框架。

- 适用于任何基于JDBC的ORM框架，如：JPA, Hibernate, Mybatis, Spring JDBC Template或直接使用JDBC。
- 支持任何第三方的数据库连接池，如：DBCP, C3P0, BoneCP, Druid, HikariCP等。
- 支持任意实现JDBC规范的数据库。目前支持MySQL，Oracle，SQLServer，PostgreSQL以及任何遵循SQL92标准的数据库。

![Sharding-JDBC Architecture](https://shardingsphere.apache.org/document/current/img/sharding-jdbc-brief.png)

### Sharding-Proxy

定位为透明化的数据库代理端，提供封装了数据库二进制协议的服务端版本，用于完成对异构语言的支持。 目前先提供MySQL/PostgreSQL版本，它可以使用任何兼容MySQL/PostgreSQL协议的访问客户端(如：MySQL Command Client, MySQL Workbench, Navicat等)操作数据，对DBA更加友好。

- 向应用程序完全透明，可直接当做MySQL/PostgreSQL使用。
- 适用于任何兼容MySQL/PostgreSQL协议的的客户端。

定位为透明化的数据库代理端，提供封装了数据库二进制协议的服务端版本，用于完成对异构语言的支持。 目前先提供MySQL/PostgreSQL版本，它可以使用任何兼容MySQL/PostgreSQL协议的访问客户端(如：MySQL Command Client, MySQL Workbench, Navicat等)操作数据，对DBA更加友好。

- 向应用程序完全透明，可直接当做MySQL/PostgreSQL使用。
- 适用于任何兼容MySQL/PostgreSQL协议的的客户端。

![Sharding-Proxy Architecture](https://shardingsphere.apache.org/document/current/img/sharding-proxy-brief_v2.png)

### Sharding-Sidecar（TODO）

定位为Kubernetes的云原生数据库代理，以Sidecar的形式代理所有对数据库的访问。 通过无中心、零侵入的方案提供与数据库交互的的啮合层，即Database Mesh，又可称数据网格。

Database Mesh的关注重点在于如何将分布式的数据访问应用与数据库有机串联起来，它更加关注的是交互，是将杂乱无章的应用与数据库之间的交互有效的梳理。使用Database Mesh，访问数据库的应用和数据库终将形成一个巨大的网格体系，应用和数据库只需在网格体系中对号入座即可，它们都是被啮合层所治理的对象。

![Sharding-Sidecar Architecture](https://shardingsphere.apache.org/document/current/img/sharding-sidecar-brief_v2.png)

| Sharding-JDBC | Sharding-Proxy | Sharding-Sidecar |        |
| :------------ | :------------- | :--------------- | ------ |
| 数据库        | 任意           | MySQL            | MySQL  |
| 连接消耗数    | 高             | 低               | 高     |
| 异构语言      | 仅Java         | 任意             | 任意   |
| 性能          | 损耗低         | 损耗略高         | 损耗低 |
| 无中心化      | 是             | 否               | 是     |
| 静态入口      | 无             | 有               | 无     |

### 混合架构

Sharding-JDBC采用无中心化架构，适用于Java开发的高性能的轻量级OLTP应用；Sharding-Proxy提供静态入口以及异构语言的支持，适用于OLAP应用以及对分片数据库进行管理和运维的场景。

ShardingSphere是多接入端共同组成的生态圈。 通过混合使用Sharding-JDBC和Sharding-Proxy，并采用同一注册中心统一配置分片策略，能够灵活的搭建适用于各种场景的应用系统，架构师可以更加自由的调整适合于当前业务的最佳系统架构。

![ShardingSphere Hybrid Architecture](https://shardingsphere.apache.org/document/current/img/shardingsphere-hybrid.png)

## 入门

### SHARDING-JDBC

1. 引入maven依赖

```xml
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-core</artifactId>
    <version>${latest.release.version}</version>
</dependency>
```

注意: 请将`${latest.release.version}`更改为实际的版本号。

2. 规则配置

Sharding-JDBC可以通过`Java`，`YAML`，`Spring命名空间`和`Spring Boot Starter`四种方式配置，开发者可根据场景选择适合的配置方式。

3. 创建DataSource

通过ShardingDataSourceFactory工厂和规则配置对象获取ShardingDataSource，ShardingDataSource实现自JDBC的标准接口DataSource。然后即可通过DataSource选择使用原生JDBC开发，或者使用JPA, MyBatis等ORM工具。

```java
DataSource dataSource = ShardingDataSourceFactory.createDataSource(dataSourceMap, shardingRuleConfig, props);
```

### SHARDING-PROXY

1. 规则配置

编辑`%SHARDING_PROXY_HOME%\conf\config-xxx.yaml`。

编辑`%SHARDING_PROXY_HOME%\conf\server.yaml`。

2. 引入依赖

如果后端连接PostgreSQL数据库，不需要引入额外依赖。

如果后端连接MySQL数据库，需要下载[MySQL Connector/J](https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz)， 解压缩后，将mysql-connector-java-5.1.47.jar拷贝到${sharding-proxy}\lib目录。

3. 启动服务

- 使用默认配置项

```sh
${sharding-proxy}\bin\start.sh
```

- 配置端口

```sh
${sharding-proxy}\bin\start.sh ${port}
```

### SHARDING-SCALING

**部署启动**

1. 执行以下命令，编译生成sharding-scaling二进制包：

```sh
git clone https://github.com/apache/incubator-shardingsphere.git；
cd incubarot-shardingsphere;
mvn clean install -Prelease;
```

发布包所在目录

为：`/sharding-distribution/sharding-scaling-distribution/target/apache-shardingsphere-incubating-${latest.release.version}-sharding-scaling-bin.tar.gz`。

2.解压缩发布包，修改配置文件`conf/server.yaml`，这里主要修改启动端口，保证不与本机其他端口冲突，其他值保持默认即可：

```sh
port: 8888
blockQueueSize: 10000
pushTimeout: 1000
workerThread: 30
```

3.启动sharding-scaling：

```sh
sh bin/start.sh
```

**注意**： 如果后端连接MySQL数据库，需要下载[MySQL Connector/J](https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz)， 解压缩后，将mysql-connector-java-5.1.47.jar拷贝到${sharding-scaling}\lib目录。

4.查看日志`logs/stdout.log`，确保启动成功。

**创建迁移任务**

Sharding-Scaling提供相应的HTTP接口来管理迁移任务，部署启动成功后，我们可以调用相应的接口来启动迁移任务。

创建迁移任务：

```sh
curl -X POST \
  http://localhost:8888/shardingscaling/job/start \
  -H 'content-type: application/json' \
  -d '{
   "ruleConfiguration": {
      "sourceDatasource": "ds_0: !!YamlDataSourceConfiguration\n  dataSourceClassName: com.zaxxer.hikari.HikariDataSource\n  properties:\n    jdbcUrl: jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&useSSL=false\n    username: root\n    password: '\''123456'\''\n    connectionTimeout: 30000\n    idleTimeout: 60000\n    maxLifetime: 1800000\n    maxPoolSize: 50\n    minPoolSize: 1\n    maintenanceIntervalMilliseconds: 30000\n    readOnly: false\n",
      "sourceRule": "defaultDatabaseStrategy:\n  inline:\n    algorithmExpression: ds_${user_id % 2}\n    shardingColumn: user_id\ntables:\n  t1:\n    actualDataNodes: ds_0.t1\n    keyGenerator:\n      column: order_id\n      type: SNOWFLAKE\n    logicTable: t1\n    tableStrategy:\n      inline:\n        algorithmExpression: t1\n        shardingColumn: order_id\n  t2:\n    actualDataNodes: ds_0.t2\n    keyGenerator:\n      column: order_item_id\n      type: SNOWFLAKE\n    logicTable: t2\n    tableStrategy:\n      inline:\n        algorithmExpression: t2\n        shardingColumn: order_id\n",
      "destinationDataSources": {
         "name": "dt_0",
         "password": "123456",
         "url": "jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&useSSL=false",
         "username": "root"
      }
   },
   "jobConfiguration": {
      "concurrency": 3
   }
}'
```

注意：上述需要修改`ruleConfiguration.sourceDatasource`和`ruleConfiguration.sourceRule`，分别为源端ShardingSphere数据源和数据表规则相关配置；

以及`ruleConfiguration.destinationDataSources`中目标端sharding-proxy的相关信息。

返回如下信息，表示任务创建成功：

```json
{
   "success": true,
   "errorCode": 0,
   "errorMsg": null,
   "model": null
}
```

需要注意的是，目前Sharding-Scaling任务创建成功后，便会自动运行，进行数据的迁移。

**查询任务进度**

执行如下命令获取当前所有迁移任务：

```sh
curl -X GET \
  http://localhost:8888/shardingscaling/job/list
```

返回示例如下：

```json
{
  "success": true,
  "errorCode": 0,
  "model": [
    {
      "jobId": 1,
      "jobName": "Local Sharding Scaling Job",
      "status": "RUNNING"
    }
  ]
}
```

进一步查询任务具体迁移状态：

```sh
curl -X GET \
  http://localhost:8888/shardingscaling/job/progress/1
```

返回任务详细信息如下：

```json
{
   "success": true,
   "errorCode": 0,
   "errorMsg": null,
   "model": {
        "id": 1,
        "jobName": "Local Sharding Scaling Job",
        "status": "RUNNING"
        "syncTaskProgress": [{
            "id": "127.0.0.1-3306-test",
            "status": "SYNCHRONIZE_REALTIME_DATA",
            "historySyncTaskProgress": [{
                "id": "history-test-t1#0",
                "estimatedRows": 41147,
                "syncedRows": 41147
            }, {
                "id": "history-test-t1#1",
                "estimatedRows": 42917,
                "syncedRows": 42917
            }, {
                "id": "history-test-t1#2",
                "estimatedRows": 43543,
                "syncedRows": 43543
            }, {
                "id": "history-test-t2#0",
                "estimatedRows": 39679,
                "syncedRows": 39679
            }, {
                "id": "history-test-t2#1",
                "estimatedRows": 41483,
                "syncedRows": 41483
            }, {
                "id": "history-test-t2#2",
                "estimatedRows": 42107,
                "syncedRows": 42107
            }],
            "realTimeSyncTaskProgress": {
                "id": "realtime-test",
                "delayMillisecond": 1576563771372,
                "logPosition": {
                    "filename": "ON.000007",
                    "position": 177532875,
                    "serverId": 0
                }
            }
        }]
   }
}
```

**结束任务**

数据迁移完成后，我们可以调用接口结束任务：

```
curl -X POST \
  http://localhost:8888/shardingscaling/job/stop \
  -H 'content-type: application/json' \
  -d '{
   "jobId":1
}'
```

返回如下信息表示任务成功结束：

```
{
   "success": true,
   "errorCode": 0,
   "errorMsg": null,
   "model": null
}
```

**结束Sharding-Scaling**

```
sh bin/stop.sh
```

## 概念 & 功能

### 数据分片

数据分片指按照某个维度将存放在单一数据库中的数据分散地存放至多个数据库或表中以达到提升性能瓶颈以及可用性的效果。 数据分片的有效手段是对关系型数据库进行分库和分表。分库和分表均可以有效的避免由数据量超过可承受阈值而产生的查询瓶颈。 除此之外，分库还能够用于有效的分散对数据库单点的访问量；分表虽然无法缓解数据库压力，但却能够提供尽量将分布式事务转化为本地事务的可能，一旦涉及到跨库的更新操作，分布式事务往往会使问题变得复杂。 使用多主多从的分片方式，可以有效的避免数据单点，从而提升数据架构的可用性。

通过分库和分表进行数据的拆分来使得各个表的数据量保持在阈值以下，以及对流量进行疏导应对高访问量，是应对高并发和海量数据系统的有效手段。 数据分片的拆分方式又分为垂直分片和水平分片。

#### 垂直分片

按照业务拆分的方式称为垂直分片，又称为纵向拆分，它的核心理念是专库专用。 在拆分之前，一个数据库由多个数据表构成，每个表对应着不同的业务。而拆分之后，则是按照业务将表进行归类，分布到不同的数据库中，从而将压力分散至不同的数据库。 下图展示了根据业务需要，将用户表和订单表垂直分片到不同的数据库的方案。

[![垂直分片](https://shardingsphere.apache.org/document/current/img/sharding/vertical_sharding.png)](https://shardingsphere.apache.org/document/current/img/sharding/vertical_sharding.png)

垂直分片往往需要对架构和设计进行调整。通常来讲，是来不及应对互联网业务需求快速变化的；而且，它也并无法真正的解决单点瓶颈。 垂直拆分可以缓解数据量和访问量带来的问题，但无法根治。如果垂直拆分之后，表中的数据量依然超过单节点所能承载的阈值，则需要水平分片来进一步处理。

#### 水平分片

水平分片又称为横向拆分。 相对于垂直分片，它不再将数据根据业务逻辑分类，而是通过某个字段（或某几个字段），根据某种规则将数据分散至多个库或表中，每个分片仅包含数据的一部分。 例如：根据主键分片，偶数主键的记录放入0库（或表），奇数主键的记录放入1库（或表），如下图所示。

[![水平分片](https://shardingsphere.apache.org/document/current/img/sharding/horizontal_sharding.png)](https://shardingsphere.apache.org/document/current/img/sharding/horizontal_sharding.png)

水平分片从理论上突破了单机数据量处理的瓶颈，并且扩展相对自由，是分库分表的标准解决方案。

#### 分片键

用于分片的数据库字段，是将数据库(表)水平拆分的关键字段。例：将订单表中的订单主键的尾数取模分片，则订单主键为分片字段。 SQL中如果无分片字段，将执行全路由，性能较差。 除了对单分片字段的支持，ShardingSphere也支持根据多个字段进行分片。

#### 分片算法

通过分片算法将数据分片，支持通过`=`、`>=`、`<=`、`>`、`<`、`BETWEEN`和`IN`分片。分片算法需要应用方开发者自行实现，可实现的灵活度非常高。

目前提供4种分片算法。由于分片算法和业务实现紧密相关，因此并未提供内置分片算法，而是通过分片策略将各种场景提炼出来，提供更高层级的抽象，并提供接口让应用开发者自行实现分片算法。

- 精确分片算法

对应PreciseShardingAlgorithm，用于处理使用单一键作为分片键的=与IN进行分片的场景。需要配合StandardShardingStrategy使用。

- 范围分片算法

对应RangeShardingAlgorithm，用于处理使用单一键作为分片键的BETWEEN AND、>、<、>=、<=进行分片的场景。需要配合StandardShardingStrategy使用。

- 复合分片算法

对应ComplexKeysShardingAlgorithm，用于处理使用多键作为分片键进行分片的场景，包含多个分片键的逻辑较复杂，需要应用开发者自行处理其中的复杂度。需要配合ComplexShardingStrategy使用。

- Hint分片算法

对应HintShardingAlgorithm，用于处理使用Hint行分片的场景。需要配合HintShardingStrategy使用。

#### 分片策略

包含分片键和分片算法，由于分片算法的独立性，将其独立抽离。真正可用于分片操作的是分片键 + 分片算法，也就是分片策略。目前提供5种分片策略。

- 标准分片策略

对应StandardShardingStrategy。提供对SQL语句中的=, >, <, >=, <=, IN和BETWEEN AND的分片操作支持。StandardShardingStrategy只支持单分片键，提供PreciseShardingAlgorithm和RangeShardingAlgorithm两个分片算法。PreciseShardingAlgorithm是必选的，用于处理=和IN的分片。RangeShardingAlgorithm是可选的，用于处理BETWEEN AND, >, <, >=, <=分片，如果不配置RangeShardingAlgorithm，SQL中的BETWEEN AND将按照全库路由处理。

- 复合分片策略

对应ComplexShardingStrategy。复合分片策略。提供对SQL语句中的=, >, <, >=, <=, IN和BETWEEN AND的分片操作支持。ComplexShardingStrategy支持多分片键，由于多分片键之间的关系复杂，因此并未进行过多的封装，而是直接将分片键值组合以及分片操作符透传至分片算法，完全由应用开发者实现，提供最大的灵活度。

- 行表达式分片策略

对应InlineShardingStrategy。使用Groovy的表达式，提供对SQL语句中的=和IN的分片操作支持，只支持单分片键。对于简单的分片算法，可以通过简单的配置使用，从而避免繁琐的Java代码开发，如: `t_user_$->{u_id % 8}` 表示t_user表根据u_id模8，而分成8张表，表名称为`t_user_0`到`t_user_7`。

- Hint分片策略

对应HintShardingStrategy。通过Hint指定分片值而非从SQL中提取分片值的方式进行分片的策略。

- 不分片策略

对应NoneShardingStrategy。不分片的策略。

#### 分片策略配置

对于分片策略存有数据源分片策略和表分片策略两种维度。

- 数据源分片策略

对应于DatabaseShardingStrategy。用于配置数据被分配的目标数据源。

- 表分片策略

对应于TableShardingStrategy。用于配置数据被分配的目标表，该目标表存在与该数据的目标数据源内。故表分片策略是依赖与数据源分片策略的结果的。

两种策略的API完全相同。

#### 分片路由

用于根据分片键进行路由的场景，又细分为直接路由、标准路由和笛卡尔积路由这3种类型。

#### 直接路由

满足直接路由的条件相对苛刻，它需要通过Hint（使用HintAPI直接指定路由至库表）方式分片，并且是只分库不分表的前提下，则可以避免SQL解析和之后的结果归并。 因此它的兼容性最好，可以执行包括子查询、自定义函数等复杂情况的任意SQL。直接路由还可以用于分片键不在SQL中的场景。例如，设置用于数据库分片的键为`3`，

```java
hintManager.setDatabaseShardingValue(3);
```

假如路由算法为`value % 2`，当一个逻辑库`t_order`对应2个真实库`t_order_0`和`t_order_1`时，路由后SQL将在`t_order_1`上执行。下方是使用API的代码样例：

```java
String sql = "SELECT * FROM t_order";
try (
        HintManager hintManager = HintManager.getInstance();
        Connection conn = dataSource.getConnection();
        PreparedStatement pstmt = conn.prepareStatement(sql)) {
    hintManager.setDatabaseShardingValue(3);
    try (ResultSet rs = pstmt.executeQuery()) {
        while (rs.next()) {
            //...
        }
    }
}
```

#### 标准路由

标准路由是ShardingSphere最为推荐使用的分片方式，它的适用范围是不包含关联查询或仅包含绑定表之间关联查询的SQL。 当分片运算符是等于号时，路由结果将落入单库（表），当分片运算符是BETWEEN或IN时，则路由结果不一定落入唯一的库（表），因此一条逻辑SQL最终可能被拆分为多条用于执行的真实SQL。 举例说明，如果按照`order_id`的奇数和偶数进行数据分片，一个单表查询的SQL如下：

```sql
SELECT * FROM t_order WHERE order_id IN (1, 2);
```

那么路由的结果应为：

```sql
SELECT * FROM t_order_0 WHERE order_id IN (1, 2);
SELECT * FROM t_order_1 WHERE order_id IN (1, 2);
```

绑定表的关联查询与单表查询复杂度和性能相当。举例说明，如果一个包含绑定表的关联查询的SQL如下：

```sql
SELECT * FROM t_order o JOIN t_order_item i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
```

那么路由的结果应为：

```sql
SELECT * FROM t_order_0 o JOIN t_order_item_0 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
SELECT * FROM t_order_1 o JOIN t_order_item_1 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
```

可以看到，SQL拆分的数目与单表是一致的。

#### 笛卡尔路由

笛卡尔路由是最复杂的情况，它无法根据绑定表的关系定位分片规则，因此非绑定表之间的关联查询需要拆解为笛卡尔积组合执行。 如果上个示例中的SQL并未配置绑定表关系，那么路由的结果应为：

```sql
SELECT * FROM t_order_0 o JOIN t_order_item_0 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
SELECT * FROM t_order_0 o JOIN t_order_item_1 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
SELECT * FROM t_order_1 o JOIN t_order_item_0 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
SELECT * FROM t_order_1 o JOIN t_order_item_1 i ON o.order_id=i.order_id  WHERE order_id IN (1, 2);
```

笛卡尔路由查询性能较低，需谨慎使用。

#### 广播路由

对于不携带分片键的SQL，则采取广播路由的方式。根据SQL类型又可以划分为全库表路由、全库路由、全实例路由、单播路由和阻断路由这5种类型。

#### 全库表路由

全库表路由用于处理对数据库中与其逻辑表相关的所有真实表的操作，主要包括不带分片键的DQL和DML，以及DDL等。例如：

```sql
SELECT * FROM t_order WHERE good_prority IN (1, 10);
```

则会遍历所有数据库中的所有表，逐一匹配逻辑表和真实表名，能够匹配得上则执行。路由后成为

```sql
SELECT * FROM t_order_0 WHERE good_prority IN (1, 10);
SELECT * FROM t_order_1 WHERE good_prority IN (1, 10);
SELECT * FROM t_order_2 WHERE good_prority IN (1, 10);
SELECT * FROM t_order_3 WHERE good_prority IN (1, 10);
```

#### 全库路由

全库路由用于处理对数据库的操作，包括用于库设置的SET类型的数据库管理命令，以及TCL这样的事务控制语句。 在这种情况下，会根据逻辑库的名字遍历所有符合名字匹配的真实库，并在真实库中执行该命令，例如：

```sql
SET autocommit=0;
```

在`t_order`中执行，`t_order`有2个真实库。则实际会在`t_order_0`和`t_order_1`上都执行这个命令。

#### 全实例路由

全实例路由用于DCL操作，授权语句针对的是数据库的实例。无论一个实例中包含多少个Schema，每个数据库的实例只执行一次。例如：

```sql
CREATE USER customer@127.0.0.1 identified BY '123';
```

这个命令将在所有的真实数据库实例中执行，以确保customer用户可以访问每一个实例。

#### 单播路由

单播路由用于获取某一真实表信息的场景，它仅需要从任意库中的任意真实表中获取数据即可。例如：

```sql
DESCRIBE t_order;
```

t_order的两个真实表t_order_0，t_order_1的描述结构相同，所以这个命令在任意真实表上选择执行一次。

#### 阻断路由

阻断路由用于屏蔽SQL对数据库的操作，例如：

```sql
USE order_db;
```

这个命令不会在真实数据库中执行，因为ShardingSphere采用的是逻辑Schema的方式，无需将切换数据库Schema的命令发送至数据库中。

路由引擎的整体结构划分如下图。

[![路由引擎结构](https://shardingsphere.apache.org/document/current/img/sharding/route_architecture.png)](https://shardingsphere.apache.org/document/current/img/sharding/route_architecture.png)

### 读写分离

通过一主多从的配置方式，可以将查询请求均匀的分散到多个数据副本，能够进一步的提升系统的处理能力。 使用多主多从的方式，不但能够提升系统的吞吐量，还能够提升系统的可用性，可以达到在任何一个数据库宕机，甚至磁盘物理损坏的情况下仍然不影响系统的正常运行。

与将数据根据分片键打散至各个数据节点的水平分片不同，读写分离则是根据SQL语义的分析，将读操作和写操作分别路由至主库与从库。

[![读写分离](https://shardingsphere.apache.org/document/current/img/read-write-split/read-write-split.png)](https://shardingsphere.apache.org/document/current/img/read-write-split/read-write-split.png)

读写分离的数据节点中的数据内容是一致的，而水平分片的每个数据节点的数据内容却并不相同。将水平分片和读写分离联合使用，能够更加有效的提升系统性能。

#### 挑战

读写分离虽然可以提升系统的吞吐量和可用性，但同时也带来了数据不一致的问题。 这包括多个主库之间的数据一致性，以及主库与从库之间的数据一致性的问题。 并且，读写分离也带来了与数据分片同样的问题，它同样会使得应用开发和运维人员对数据库的操作和运维变得更加复杂。 下图展现了将分库分表与读写分离一同使用时，应用程序与数据库集群之间的复杂拓扑关系。

[![数据分片 + 读写分离](https://shardingsphere.apache.org/document/current/img/read-write-split/sharding-read-write-split.png)](https://shardingsphere.apache.org/document/current/img/read-write-split/sharding-read-write-split.png)

#### 主库

添加、更新以及删除数据操作所使用的数据库，目前仅支持单主库。

#### 从库

查询数据操作所使用的数据库，可支持多从库。

#### 主从同步

将主库的数据异步的同步到从库的操作。由于主从同步的异步性，从库与主库的数据会短时间内不一致。

#### 负载均衡策略

通过负载均衡策略将查询请求疏导至不同从库。

###  编排治理

编排治理模块提供配置中心/注册中心(以及规划中的元数据中心)、配置动态化、数据库熔断禁用、调用链路等治理能力。

#### 配置中心

实现动机

- 配置集中化：越来越多的运行时实例，使得散落的配置难于管理，配置不同步导致的问题十分严重。将配置集中于配置中心，可以更加有效进行管理。
- 配置动态化：配置修改后的分发，是配置中心可以提供的另一个重要能力。它可支持数据源、表与分片及读写分离策略的动态切换。

配置中心数据结构

配置中心在定义的命名空间的config下，以YAML格式存储，包括数据源，数据分片，读写分离、Properties配置，可通过修改节点来实现对于配置的动态管理。

```
config
    ├──authentication                            # Sharding-Proxy权限配置
    ├──props                                     # 属性配置
    ├──schema                                    # Schema配置
    ├      ├──sharding_db                        # SchemaName配置
    ├      ├      ├──datasource                  # 数据源配置
    ├      ├      ├──rule                        # 数据分片规则配置
    ├      ├──masterslave_db                     # SchemaName配置
    ├      ├      ├──datasource                  # 数据源配置
    ├      ├      ├──rule                        # 读写分离规则
```

config/authentication

```yaml
password: root
username: root
```

config/sharding/props

相对于sharding-sphere配置里面的Sharding Properties。

```yaml
executor.size: 20
sql.show: true
```

config/schema/schemeName/datasource

多个数据库连接池的集合，不同数据库连接池属性自适配（例如：DBCP，C3P0，Druid, HikariCP）。

```yaml
ds_0: !!org.apache.shardingsphere.orchestration.yaml.YamlDataSourceConfiguration
  dataSourceClassName: com.zaxxer.hikari.HikariDataSource
  properties:
    url: jdbc:mysql://127.0.0.1:3306/demo_ds_0?serverTimezone=UTC&useSSL=false
    password: null
    maxPoolSize: 50
    maintenanceIntervalMilliseconds: 30000
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    minPoolSize: 1
    username: root
    maxLifetimeMilliseconds: 1800000
ds_1: !!org.apache.shardingsphere.orchestration.yaml.YamlDataSourceConfiguration
  dataSourceClassName: com.zaxxer.hikari.HikariDataSource
  properties:
    url: jdbc:mysql://127.0.0.1:3306/demo_ds_1?serverTimezone=UTC&useSSL=false
    password: null
    maxPoolSize: 50
    maintenanceIntervalMilliseconds: 30000
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    minPoolSize: 1
    username: root
    maxLifetimeMilliseconds: 1800000
```

config/schema/sharding_db/rule

数据分片配置，包括数据分片 + 读写分离配置。

```yaml
tables:
  t_order:
    actualDataNodes: ds_$->{0..1}.t_order_$->{0..1}
    databaseStrategy:
      inline:
        shardingColumn: user_id
        algorithmExpression: ds_$->{user_id % 2}
    keyGenerator:
      column: order_id
    logicTable: t_order
    tableStrategy:
      inline:
        shardingColumn: order_id
        algorithmExpression: t_order_$->{order_id % 2}
  t_order_item:
    actualDataNodes: ds_$->{0..1}.t_order_item_$->{0..1}
    databaseStrategy:
      inline:
        shardingColumn: user_id
        algorithmExpression: ds_$->{user_id % 2}
    keyGenerator:
      column: order_item_id
    logicTable: t_order_item
    tableStrategy:
      inline:
        shardingColumn: order_id
        algorithmExpression: t_order_item_$->{order_id % 2}
bindingTables:
  - t_order,t_order_item
broadcastTables:
  - t_config
  
defaultDataSourceName: ds_0
    
masterSlaveRules: {}
```

config/schema/masterslave/rule

读写分离独立使用时使用该配置。

```yaml
name: ds_ms
masterDataSourceName: ds_master 
slaveDataSourceNames:
  - ds_slave0
  - ds_slave1
loadBalanceAlgorithmType: ROUND_ROBIN
```

动态生效

在注册中心上修改、删除、新增相关配置，会动态推送到生产环境并立即生效。

#### 注册中心

实现动机

- 相对于配置中心管理配置数据，注册中心存放运行时的动态/临时状态数据，比如可用的proxy的实例，需要禁用或熔断的datasource实例。
- 通过注册中心，可以提供熔断数据库访问程序对数据库的访问和禁用从库的访问的编排治理能力。治理仍然有大量未完成的功能（比如流控等）。

注册中心数据结构

注册中心在定义的命名空间的state下，创建数据库访问对象运行节点，用于区分不同数据库访问实例。包括instances和datasources节点。

```
instances
    ├──your_instance_ip_a@-@your_instance_pid_x
    ├──your_instance_ip_b@-@your_instance_pid_y
    ├──....
datasources
    ├──ds0
    ├──ds1
    ├──....
```

Sharding-Proxy支持多逻辑数据源，因此datasources子节点的名称采用schema_name.data_source_name的形式。

```
instances
    ├──your_instance_ip_a@-@your_instance_pid_x
    ├──your_instance_ip_b@-@your_instance_pid_y
    ├──....
datasources
    ├──sharding_db.ds0
    ├──sharding_db.ds1
    ├──....
```

state/instances

数据库访问对象运行实例信息，子节点是当前运行实例的标识。 运行实例标识由运行服务器的IP地址和PID构成。运行实例标识均为临时节点，当实例上线时注册，下线时自动清理。 注册中心监控这些节点的变化来治理运行中实例对数据库的访问等。

state/datasources

可以治理读写分离从库，可动态添加删除以及禁用。

操作指南

熔断实例

可在IP地址@-@PID节点写入`DISABLED`（忽略大小写）表示禁用该实例，删除DISABLED表示启用。

Zookeeper命令如下：

```
[zk: localhost:2181(CONNECTED) 0] set /your_zk_namespace/your_app_name/state/instances/your_instance_ip_a@-@your_instance_pid_x DISABLED
```

禁用从库

在读写分离（或数据分片+读写分离）场景下，可在数据源名称子节点中写入`DISABLED`（忽略大小写）表示禁用从库数据源，删除DISABLED或节点表示启用。

Zookeeper命令如下：

```
[zk: localhost:2181(CONNECTED) 0] set /your_zk_namespace/your_app_name/state/datasources/your_slave_datasource_name DISABLED
```

#### 支持的配置中心/注册中心

SPI

[Service Provider Interface (SPI)](https://docs.oracle.com/javase/tutorial/sound/SPI-intro.html)是一种为了被第三方实现或扩展的API。它可以用于实现框架扩展或组件替换。

ShardingSphere在数据库治理模块使用SPI方式载入数据到配置中心/注册中心，进行实例熔断和数据库禁用。 目前，ShardingSphere内部支持Zookeeper和etcd这种常用的配置中心/注册中心。 此外，您可以使用其他第三方配置中心/注册中心，并通过SPI的方式注入到ShardingSphere，从而使用该配置中心/注册中心，实现数据库治理功能。

Zookeeper

ShardingSphere官方使用[Apache Curator](http://curator.apache.org/)作为Zookeeper的实现方案（支持配置中心和注册中心）。 请使用Zookeeper 3.4.6及其以上版本，详情请参见[官方网站](https://zookeeper.apache.org/)。

Etcd

ShardingSphere官方使用[io.etcd/jetcd](https://github.com/etcd-io/jetcd)作为Etcd的实现方案（支持配置中心和注册中心）。 请使用Etcd v3以上版本，详情请参见[官方网站](https://etcd.io/)。

Apollo

ShardingSphere官方使用[Apollo Client](https://github.com/ctripcorp/apollo)作为Apollo的实现方案（支持配置中心）。 请使用Apollo Client 1.5.0及其以上版本，详情请参见[官方网站](https://github.com/ctripcorp/apollo)。

Nacos

ShardingSphere官方使用[Nacos Client](https://nacos.io/zh-cn/docs/sdk.html)作为Nacos的实现方案（支持配置中心）。 请使用Nacos Client 1.0.0及其以上版本，详情请参见[官方网站](https://nacos.io/zh-cn/docs/sdk.html)。

#### 应用性能监控集成

`APM`是应用性能监控的缩写。目前`APM`的主要功能着眼于分布式系统的性能诊断，其主要功能包括调用链展示，应用拓扑分析等。

ShardingSphere并不负责如何采集、存储以及展示应用性能监控的相关数据，而是将SQL解析与SQL执行这两块数据分片的最核心的相关信息发送至应用性能监控系统，并交由其处理。 换句话说，ShardingSphere仅负责产生具有价值的数据，并通过标准协议递交至相关系统。ShardingSphere可以通过两种方式对接应用性能监控系统。

第一种方式是使用OpenTracing API发送性能追踪数据。面向OpenTracing协议的APM产品都可以和ShardingSphere自动对接，比如SkyWalking，Zipkin和Jaeger。使用这种方式只需要在启动时配置OpenTracing协议的实现者即可。 它的优点是可以兼容所有的与OpenTracing协议兼容的产品作为APM的展现系统，如果采用公司愿意实现自己的APM系统，也只需要实现OpenTracing协议，即可自动展示ShardingSphere的链路追踪信息。 缺点是OpenTracing协议发展并不稳定，较新的版本实现者较少，且协议本身过于中立，对于个性化的相关产品的实现不如原生支持强大。

第二种方式是使用SkyWalking的自动探针。 [ShardingSphere](https://shardingsphere.apache.org/)团队与[SkyWalking](https://skywalking.apache.org/)团队共同合作，在`SkyWalking`中实现了`ShardingSphere`自动探针，可以将相关的应用性能数据自动发送到`SkyWalking`中。

使用OpenTracing协议

- 方法1：通过读取系统参数注入APM系统提供的Tracer实现类

启动时添加参数

```
    -Dorg.apache.shardingsphere.opentracing.tracer.class=org.apache.skywalking.apm.toolkit.opentracing.SkywalkingTracer
```

调用初始化方法

```java
    ShardingTracer.init();
```

- 方法2：通过参数注入APM系统提供的Tracer实现类

```java
    ShardingTracer.init(new SkywalkingTracer());
```

*注意:使用SkyWalking的OpenTracing探针时，应将原ShardingSphere探针插件禁用，以防止两种插件互相冲突*

使用SkyWalking自动探针

请参考[SkyWalking部署手册](https://github.com/apache/skywalking/blob/5.x/docs/cn/Quick-start-CN.md)。

效果展示

无论使用哪种方式，都可以方便的将APM信息展示在对接的系统中，以下以SkyWalking为例。

应用架构

使用`Sharding-Proxy`访问两个数据库`192.168.0.1:3306`和`192.168.0.2:3306`，且每个数据库中有两个分表。

拓扑图展示

[![拓扑图](https://shardingsphere.apache.org/document/current/img/apm/5x_topology.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_topology.png)

从图中看，用户访问18次Sharding-Proxy应用，每次每个数据库访问了两次。这是由于每次访问涉及到每个库中的两个分表，所以每次访问了四张表。

跟踪数据展示

[![跟踪图](https://shardingsphere.apache.org/document/current/img/apm/5x_trace.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_trace.png)

从跟踪图中可以能够看到SQL解析和执行的情况。

`/Sharding-Sphere/parseSQL/` : 表示本次SQL的解析性能。

[![解析节点](https://shardingsphere.apache.org/document/current/img/apm/5x_parse.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_parse.png)

`/Sharding-Sphere/executeSQL/` : 表示具体执行的实际SQL的性能。

[![实际访问节点](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL.png)

异常情况展示

[![异常跟踪图](https://shardingsphere.apache.org/document/current/img/apm/5x_trace_err.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_trace_err.png)

从跟踪图中可以能够看到发生异常的节点。

`/Sharding-Sphere/executeSQL/` : 表示执行SQL异常的结果。

[![异常节点](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL_Tags_err.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL_Tags_err.png)

`/Sharding-Sphere/executeSQL/` : 表示执行SQL异常的日志。

[![异常节点日志](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL_Logs_err.png)](https://shardingsphere.apache.org/document/current/img/apm/5x_executeSQL_Logs_err.png)

#### 数据脱敏

一、背景

安全控制一直是治理的重要环节，数据脱敏属于安全控制的范畴。对互联网公司、传统行业来说，数据安全一直是极为重视和敏感的话题。数据脱敏是指对某些敏感信息通过脱敏规则进行数据的变形，实现敏感隐私数据的可靠保护。涉及客户安全数据或者一些商业性敏感数据，如身份证号、手机号、卡号、客户号等个人信息按照相关部门规定，都需要进行数据脱敏。

在真实业务场景中，相关业务开发团队则往往需要针对公司安全部门需求，自行实行并维护一套加解密系统，而当脱敏场景发生改变时，自行维护的脱敏系统往往又面临着重构或修改风险。此外，对于已经上线的业务，如何在不修改业务逻辑、业务SQL的情况下，透明化、安全低风险地实现无缝进行脱敏改造呢？

Apache ShardingSphere根据业界对脱敏的需求及业务改造痛点，提供了一套完整、安全、透明化、低改造成本的数据脱敏整合解决方案。

二、前序

Apache ShardingSphere是一套开源的分布式数据库中间件解决方案组成的生态圈，它由Sharding-JDBC、Sharding-Proxy和Sharding-Sidecar（规划中）这3款相互独立，却又能够混合部署配合使用的产品组成。它们均能够提供标准化的数据分片、分布式事务和分布式治理功能，可适用于如Java同构、异构语言、容器、云原生等各种多样化的应用场景。

数据脱敏模块属于ShardingSphere分布式治理这一核心功能下的子功能模块。它通过对用户输入的SQL进行解析，并依据用户提供的脱敏配置对SQL进行改写，从而实现对原文数据进行加密，并将原文数据(可选)及密文数据同时存储到底层数据库。在用户查询数据时，它又从数据库中取出密文数据，并对其解密，最终将解密后的原始数据返回给用户。Apache ShardingSphere分布式数据库中间件自动化&透明化了数据脱敏过程，让用户无需关注数据脱敏的实现细节，像使用普通数据那样使用脱敏数据。此外，无论是已在线业务进行脱敏改造，还是新上线业务使用脱敏功能，ShardingSphere都可以提供一套相对完善的解决方案。

三、需求场景分析

对于数据脱敏的需求，在现实的业务场景中一般分为两种情况：

1. 新业务上线，安全部门规定需将涉及用户敏感信息，例如银行、手机号码等进行加密后存储到数据库，在使用的时候再进行解密处理。因为是全新系统，因而没有存量数据清洗问题，所以实现相对简单。
2. 已上线业务，之前一直将明文存储在数据库中。相关部门突然需要对已上线业务进行脱敏整改。这种场景一般需要处理三个问题：

a) 历史数据需要如何进行脱敏处理，即洗数。

b) 如何能在不改动业务SQL和逻辑情况下，将新增数据进行脱敏处理，并存储到数据库；在使用时，再进行解密取出。

c) 如何较为安全、无缝、透明化地实现业务系统在明文与密文数据间的迁移。

四、处理流程详解

整体架构

ShardingSphere提供的Encrypt-JDBC和业务代码部署在一起。业务方需面向Encrypt-JDBC进行JDBC编程。由于Encrypt-JDBC实现所有JDBC标准接口，业务代码无需做额外改造即可兼容使用。此时，业务代码所有与数据库的交互行为交由Encrypt-JDBC负责。业务只需提供脱敏规则即可。**作为业务代码与底层数据库中间的桥梁，Encrypt-JDBC便可拦截用户行为，并在改造行为后与数据库交互。**

[![1](https://shardingsphere.apache.org/document/current/img/encrypt/1.png)](https://shardingsphere.apache.org/document/current/img/encrypt/1.png)

Encrypt-JDBC将用户发起的SQL进行拦截，并通过SQL语法解析器进行解析、理解SQL行为，再依据用户传入的脱敏规则，找出需要脱敏的字段和所使用的加解密器对目标字段进行加解密处理后，再与底层数据库进行交互。ShardingSphere会将用户请求的明文进行加密后存储到底层数据库；并在用户查询时，将密文从数据库中取出进行解密后返回给终端用户。ShardingSphere通过屏蔽对数据的脱敏处理，使用户无需感知解析SQL、数据加密、数据解密的处理过程，就像在使用普通数据一样使用脱敏数据。

脱敏规则

在详解整套流程之前，我们需要先了解下脱敏规则与配置，这是认识整套流程的基础。脱敏配置主要分为四部分：数据源配置，加密器配置，脱敏表配置以及查询属性配置，其详情如下图所示：

[![2](https://shardingsphere.apache.org/document/current/img/encrypt/2.png)](https://shardingsphere.apache.org/document/current/img/encrypt/2.png)

**数据源配置**：是指DataSource的配置。

**加密器配置**：是指使用什么加密策略进行加解密。目前ShardingSphere内置了两种加解密策略：AES/MD5。用户还可以通过实现ShardingSphere提供的接口，自行实现一套加解密算法。

**脱敏表配置**：用于告诉ShardingSphere数据表里哪个列用于存储密文数据（cipherColumn）、哪个列用于存储明文数据（plainColumn）以及用户想使用哪个列进行SQL编写（logicColumn）。

> 如何理解`用户想使用哪个列进行SQL编写（logicColumn）`？
>
> 我们可以从Encrypt-JDBC存在的意义来理解。Encrypt-JDBC最终目的是希望屏蔽底层对数据的脱敏处理，也就是说我们不希望用户知道数据是如何被加解密的、如何将明文数据存储到plainColumn，将密文数据存储到cipherColumn。换句话说，我们不希望用户知道plainColumn和cipherColumn的存在和使用。所以，我们需要给用户提供一个概念意义上的列，这个列可以脱离底层数据库的真实列，它可以是数据库表里的一个真实列，也可以不是，从而使得用户可以随意改变底层数据库的plainColumn和cipherColumn的列名。或者删除plainColumn，选择永远不再存储明文，只存储密文。只要用户的SQL面向这个逻辑列进行编写，并在脱敏规则里给出logicColumn和plainColumn、cipherColumn之间正确的映射关系即可。
>
> 为什么要这么做呢？答案在文章后面，即为了让已上线的业务能无缝、透明、安全地进行数据脱敏迁移。

**查询属性的配置**：当底层数据库表里同时存储了明文数据、密文数据后，该属性开关用于决定是直接查询数据库表里的明文数据进行返回，还是查询密文数据通过Encrypt-JDBC解密后返回。

脱敏处理过程

举个栗子，假如数据库里有一张表叫做t_user，这张表里实际有两个字段pwd_plain，用于存放明文数据、pwd_cipher，用于存放密文数据，同时定义logicColumn为pwd。那么，用户在编写SQL时应该面向logicColumn进行编写，即INSERT INTO t_user SET pwd = ‘123’。ShardingSphere接收到该SQL，通过用户提供的脱敏配置，发现pwd是logicColumn，于是便对逻辑列及其对应的明文数据进行脱敏处理。可以看出**ShardingSphere将面向用户的逻辑列与面向底层数据库的明文列和密文列进行了列名以及数据的脱敏映射转换。**如下图所示：

[![3](https://shardingsphere.apache.org/document/current/img/encrypt/3.png)](https://shardingsphere.apache.org/document/current/img/encrypt/3.png)

**这也正是Encrypt-JDBC核心意义所在，即依据用户提供的脱敏规则，将用户SQL与底层数据表结构割裂开来，使得用户的SQL编写不再依赖于真实的数据库表结构。而用户与底层数据库之间的衔接、映射、转换交由ShardingSphere进行处理。**为什么我们要这么做？还是那句话：为了让已上线的业务能无缝、透明、安全地进行数据脱敏迁移。

为了让读者更清晰了解到Encrypt-JDBC的核心处理流程，下方图片展示了使用Encrypt-JDBC进行增删改查时，其中的处理流程和转换逻辑，如下图所示。

[![4](https://shardingsphere.apache.org/document/current/img/encrypt/4.png)](https://shardingsphere.apache.org/document/current/img/encrypt/4.png)

五、解决方案详解

在了解了ShardingSphere脱敏处理流程后，即可将脱敏配置、脱敏处理流程与实际场景进行结合。所有的设计开发都是为了解决业务场景遇到的痛点。那么面对之前提到的业务场景需求，又应该如何使用ShardingSphere这把利器来满足业务需求呢？

新上线业务

业务场景分析：新上线业务由于一切从零开始，不存在历史数据清洗问题，所以相对简单。

解决方案说明：选择合适的加密器，如AES后，只需配置逻辑列（面向用户编写SQL）和密文列（数据表存密文数据）即可，**逻辑列和密文列可以相同也可以不同**。建议配置如下（Yaml格式展示）：

```yaml
encryptRule:
  encryptors:
    aes_encryptor:
      type: aes
      props:
        aes.key.value: 123456abc
  tables:
    t_user:
      columns:
        pwd:
          cipherColumn: pwd
          encryptor: aes_encryptor
```

使用这套配置，Encrypt-JDBC只需将logicColumn和cipherColumn进行转换，底层数据表不存储明文，只存储了密文，这也是安全审计部分的要求所在。如果用户希望将明文、密文一同存储到数据库，只需添加plainColumn配置即可。整体处理流程如下图所示：

[![5](https://shardingsphere.apache.org/document/current/img/encrypt/5.png)](https://shardingsphere.apache.org/document/current/img/encrypt/5.png)

已上线业务改造

业务场景分析：由于业务已经在线上运行，数据库里必然存有大量明文历史数据。现在的问题是如何让历史数据得以加密清洗、如何让增量数据得以加密处理、如何让业务在新旧两套数据系统之间进行无缝、透明化迁移。

解决方案说明：在提供解决方案之前，我们先来头脑风暴一下：首先，既然是旧业务需要进行脱敏改造，那一定存储了非常重要且敏感的信息。这些信息含金量高且业务相对基础重要。如果搞错了，整个团队KPI就再见了。所以不可能一上来就停业务，禁止新数据写入，再找个加密器把历史数据全部加密清洗，再把之前重构的代码部署上线，使其能把存量和增量数据进行在线加密解密。如此简单粗暴的方式，按照历史经验来谈，一定凉凉。

那么另一种相对安全的做法是：重新搭建一套和生产环境一模一样的预发环境，然后通过相关迁移洗数工具把生产环境的**存量原文数据**加密后存储到预发环境，而**新增数据**则通过例如MySQL主从复制及业务方自行开发的工具加密后存储到预发环境的数据库里，再把重构后可以进行加解密的代码部署到预发环境。这样生产环境是一套**以明文为核心的查询修改**的环境；预发环境是一套**以密文为核心加解密查询修改**的环境。在对比一段时间无误后，可以夜间操作将生产流量切到预发环境中。此方案相对安全可靠，只是时间、人力、资金、成本较高，主要包括：预发环境搭建、生产代码整改、相关辅助工具开发等。除非无路可走，否则业务开发人员一般是从入门到放弃。

业务开发人员最希望的做法是：减少资金费用的承担、最好不要修改业务代码、能够安全平滑迁移系统。于是，ShardingSphere的脱敏功能模块便应用而生。可分为三步进行：

1. 系统迁移前

假设系统需要对t_user的pwd字段进行脱敏处理，业务方使用Encrypt-JDBC来代替标准化的JDBC接口，此举基本不需要额外改造（我们还提供了SpringBoot，SpringNameSpace，Yaml等接入方式，满足不同业务方需求）。另外，提供一套脱敏配置规则，如下所示：

```yaml
   encryptRule:
     encryptors:
       aes_encryptor:
         type: aes
         props:
           aes.key.value: 123456abc
     tables:
       t_user:
         columns:
           pwd:
             plainColumn: pwd
             cipherColumn: pwd_cipher
             encryptor: aes_encryptor
   props:
       query.with.cipher.column: false
```

依据上述脱敏规则可知，首先需要在数据库表t_user里新增一个字段叫做pwd_cipher，即cipherColumn，用于存放密文数据，同时我们把plainColumn设置为pwd，用于存放明文数据，而把logicColumn也设置为pwd。由于之前的代码SQL就是使用pwd进行编写，即面向逻辑列进行SQL编写，所以业务代码无需改动。通过Encrypt-JDBC，针对新增的数据，会把明文写到pwd列，并同时把明文进行加密存储到pwd_cipher列。此时，由于query.with.cipher.column设置为false，对业务应用来说，依旧使用pwd这一明文列进行查询存储，却在底层数据库表pwd_cipher上额外存储了新增数据的密文数据，其处理流程如下图所示：

[![6](https://shardingsphere.apache.org/document/current/img/encrypt/6.png)](https://shardingsphere.apache.org/document/current/img/encrypt/6.png)

新增数据在插入时，就通过Encrypt-JDBC加密为密文数据，并被存储到了cipherColumn。而现在就需要处理历史明文存量数据。**由于Apache ShardingSphere目前并未提供相关迁移洗数工具，此时需要业务方自行将pwd中的明文数据进行加密处理存储到pwd_cipher。**

1. 系统迁移中

新增的数据已被Encrypt-JDBC将密文存储到密文列，明文存储到明文列；历史数据被业务方自行加密清洗后，将密文也存储到密文列。也就是说现在的数据库里即存放着明文也存放着密文，只是由于配置项中的query.with.cipher.column=false，所以密文一直没有被使用过。现在我们为了让系统能切到密文数据进行查询，需要将脱敏配置中的query.with.cipher.column设置为true。在重启系统后，我们发现系统业务一切正常，但是Encrypt-JDBC已经开始从数据库里取出密文列的数据，解密后返回给用户；而对于用户的增删改需求，则依旧会把原文数据存储到明文列，加密后密文数据存储到密文列。

虽然现在业务系统通过将密文列的数据取出，解密后返回；但是，在存储的时候仍旧会存一份原文数据到明文列，这是为什么呢？答案是：为了能够进行系统回滚。**因为只要密文和明文永远同时存在，我们就可以通过开关项配置自由将业务查询切换到cipherColumn或plainColumn。**也就是说，如果将系统切到密文列进行查询时，发现系统报错，需要回滚。那么只需将query.with.cipher.column=false，Encrypt-JDBC将会还原，即又重新开始使用plainColumn进行查询。处理流程如下图所示：

[![7](https://shardingsphere.apache.org/document/current/img/encrypt/7.png)](https://shardingsphere.apache.org/document/current/img/encrypt/7.png)

1. 系统迁移后

由于安全审计部门要求，业务系统一般不可能让数据库的明文列和密文列永久同步保留，我们需要在系统稳定后将明文列数据删除。即我们需要在系统迁移后将plainColumn，即pwd进行删除。那问题来了，现在业务代码都是面向pwd进行编写SQL的，把底层数据表中的存放明文的pwd删除了，换用pwd_cipher进行解密得到原文数据，那岂不是意味着业务方需要整改所有SQL，从而不使用即将要被删除的pwd列？还记得我们Encrypt-JDBC的核心意义所在吗？

> 这也正是Encrypt-JDBC核心意义所在，即依据用户提供的脱敏规则，将用户SQL与底层数据库表结构割裂开来，使得用户的SQL编写不再依赖于真实的数据库表结构。而用户与底层数据库之间的衔接、映射、转换交由ShardingSphere进行处理。

是的，因为有logicColumn存在，用户的编写SQL都面向这个虚拟列，Encrypt-JDBC就可以把这个逻辑列和底层数据表中的密文列进行映射转换。于是迁移后的脱敏配置即为：

```yaml
   encryptRule:
     encryptors:
       aes_encryptor:
         type: aes
         props:
           aes.key.value: 123456abc
     tables:
       t_user:
         columns:
           pwd: # pwd与pwd_cipher的转换映射
             cipherColumn: pwd_cipher
             encryptor: aes_encryptor
    props:
       query.with.cipher.column: true
```

其处理流程如下：

[![8](https://shardingsphere.apache.org/document/current/img/encrypt/8.png)](https://shardingsphere.apache.org/document/current/img/encrypt/8.png)

至此，已在线业务脱敏整改解决方案全部叙述完毕。我们提供了Java、Yaml、SpringBoot、SpringNameSpace多种方式供用户选择接入，力求满足业务不同的接入需求。该解决方案目前已在京东数科不断落地上线，提供对内基础服务支撑。

六、中间件脱敏服务优势

1. 自动化&透明化数据脱敏过程，用户无需关注脱敏中间实现细节。
2. 提供多种内置、第三方(AKS)的脱敏策略，用户仅需简单配置即可使用。
3. 提供脱敏策略API接口，用户可实现接口，从而使用自定义脱敏策略进行数据脱敏。
4. 支持切换不同的脱敏策略。
5. 针对已上线业务，可实现明文数据与密文数据同步存储，并通过配置决定使用明文列还是密文列进行查询。可实现在不改变业务查询SQL前提下，已上线系统对加密前后数据进行安全、透明化迁移。

七、适用场景说明

1. 用户项目使用Java语言进行编程。
2. 后端数据库为MySQL、Oracle、PostgreSQL、SQLServer。
3. 用户需要对数据库表中某个或多个列进行脱敏(数据加密&解密)。
4. 兼容所有常用SQL；

八、限制条件

1. 用户需要自行处理数据库中原始的存量数据、洗数。
2. 使用脱敏功能+分库分表功能，部分特殊SQL不支持，请参考[SQL使用规范](https://shardingsphere.apache.org/document/current/cn/features/sharding/use-norms/sql/)。
3. 脱敏字段无法支持比较操作，如：大于小于、ORDER BY、BETWEEN、LIKE等
4. 脱敏字段无法支持计算操作，如：AVG、SUM以及计算表达式

九、加密策略解析

ShardingSphere提供了两种加密策略用于数据脱敏，该两种策略分别对应ShardingSphere的两种加解密的接口，即ShardingEncryptor和ShardingQueryAssistedEncryptor。

一方面，ShardingSphere为用户提供了内置的加解密实现类，用户只需进行配置即可使用；另一方面，为了满足用户不同场景的需求，我们还开放了相关加解密接口，用户可依据该两种类型的接口提供具体实现类。再进行简单配置，即可让ShardingSphere调用用户自定义的加解密方案进行数据脱敏。

ShardingEncryptor

该解决方案通过提供`encrypt()`, `decrypt()`两种方法对需要脱敏的数据进行加解密。在用户进行`INSERT`, `DELETE`, `UPDATE`时，ShardingSphere会按照用户配置，对SQL进行解析、改写、路由，并会调用`encrypt()`将数据加密后存储到数据库, 而在`SELECT`时，则调用`decrypt()`方法将从数据库中取出的脱敏数据进行逆向解密，最终将原始数据返回给用户。

当前，ShardingSphere针对这种类型的脱敏解决方案提供了两种具体实现类，分别是MD5(不可逆)，AES(可逆)，用户只需配置即可使用这两种内置的方案。

ShardingQueryAssistedEncryptor

相比较于第一种脱敏方案，该方案更为安全和复杂。它的理念是：即使是相同的数据，如两个用户的密码相同，它们在数据库里存储的脱敏数据也应当是不一样的。这种理念更有利于保护用户信息，防止撞库成功。

它提供三种函数进行实现，分别是`encrypt()`, `decrypt()`, `queryAssistedEncrypt()`。在`encrypt()`阶段，用户通过设置某个变动种子，例如时间戳。针对原始数据+变动种子组合的内容进行加密，就能保证即使原始数据相同，也因为有变动种子的存在，致使加密后的脱敏数据是不一样的。在`decrypt()`可依据之前规定的加密算法，利用种子数据进行解密。

虽然这种方式确实可以增加数据的保密性，但是另一个问题却随之出现：相同的数据在数据库里存储的内容是不一样的，那么当用户按照这个加密列进行等值查询(`SELECT FROM table WHERE encryptedColumnn = ?`)时会发现无法将所有相同的原始数据查询出来。为此，我们提出了辅助查询列的概念。该辅助查询列通过`queryAssistedEncrypt()`生成，与`decrypt()`不同的是，该方法通过对原始数据进行另一种方式的加密，但是针对原始数据相同的数据，这种加密方式产生的加密数据是一致的。将`queryAssistedEncrypt()`后的数据存储到数据中用于辅助查询真实数据。因此，数据库表中多出这一个辅助查询列。

由于`queryAssistedEncrypt()`和`encrypt()`产生不同加密数据进行存储，而`decrypt()`可逆，`queryAssistedEncrypt()`不可逆。 在查询原始数据的时候，我们会自动对SQL进行解析、改写、路由，利用辅助查询列进行 `WHERE`条件的查询，却利用 `decrypt()`对`encrypt()`加密后的数据进行解密，并将原始数据返回给用户。这一切都是对用户透明化的。

当前，ShardingSphere针对这种类型的脱敏解决方案并没有提供具体实现类，却将该理念抽象成接口，提供给用户自行实现。ShardingSphere将调用用户提供的该方案的具体实现类进行数据脱敏。

十、后续

本篇文章介绍了如何使用ShardingSphere产品之一的Encrypt-JDBC进行接入，接入形式还可以选择使用SpringBoot、SpringNameSpace等，这种形态的接入端主要面向JAVA同构，并与业务代码共同部署在生产环境中。面向异构语言，ShardingSphere还提供Encrypt-Proxy客户端。Encrypt-Proxy是一款实现MySQL、PostgreSQL的二进制协议的服务器端产品，用户可独立部署Encrypt-Proxy服务，并且像使用普通MySQL、PostgreSQL数据库一样，使用例如Navicat第三方数据库管理工具、JAVA连接池、命令行的方式访问这台具有脱敏功能的`虚拟数据库服务器`。

脱敏功能属于Apache ShardingSphere分布式治理的功能范畴。事实上，Apache ShardingSphere这个生态还拥有其他更强大的能力，例如数据分片、读写分离、分布式事务、监控治理等。您甚至可以选择任意多种功能模块进行叠加使用，例如同时使用数据脱敏+数据分片，或是数据分片+读写分离，再或者是监控治理+数据分片等。除了在功能层面的叠加选择，ShardingSphere还提供了各种接入端形式，例如Sharding-JDBC或Sharding-Proxy等以满足大家不同场景需求。

### 分布式事务

#### 本地事务

在不开启任何分布式事务管理器的前提下，让每个数据节点各自管理自己的事务。 它们之间没有协调以及通信的能力，也并不互相知晓其他数据节点事务的成功与否。 本地事务在性能方面无任何损耗，但在强一致性以及最终一致性方面则力不从心。

#### 两阶段提交

XA协议最早的分布式事务模型是由`X/Open`国际联盟提出的`X/Open Distributed Transaction Processing（DTP）`模型，简称XA协议。

基于XA协议实现的分布式事务对业务侵入很小。 它最大的优势就是对使用方透明，用户可以像使用本地事务一样使用基于XA协议的分布式事务。 XA协议能够严格保障事务`ACID`特性。

严格保障事务`ACID`特性是一把双刃剑。 事务执行在过程中需要将所需资源全部锁定，它更加适用于执行时间确定的短事务。 对于长事务来说，整个事务进行期间对数据的独占，将导致对热点数据依赖的业务系统并发性能衰退明显。 因此，在高并发的性能至上场景中，基于XA协议的分布式事务并不是最佳选择。

#### 柔性事务

如果将实现了`ACID`的事务要素的事务称为刚性事务的话，那么基于`BASE`事务要素的事务则称为柔性事务。 `BASE`是基本可用、柔性状态和最终一致性这三个要素的缩写。

- 基本可用（Basically Available）保证分布式事务参与方不一定同时在线。
- 柔性状态（Soft state）则允许系统状态更新有一定的延时，这个延时对客户来说不一定能够察觉。
- 而最终一致性（Eventually consistent）通常是通过消息传递的方式保证系统的最终一致性。

在`ACID`事务中对隔离性的要求很高，在事务执行过程中，必须将所有的资源锁定。 柔性事务的理念则是通过业务逻辑将互斥锁操作从资源层面上移至业务层面。通过放宽对强一致性要求，来换取系统吞吐量的提升。

基于`ACID`的强一致性事务和基于`BASE`的最终一致性事务都不是银弹，只有在最适合的场景中才能发挥它们的最大长处。 可通过下表详细对比它们之间的区别，以帮助开发者进行技术选型。

|          | *本地事务*       | *两（三）阶段事务* | *柔性事务*      |
| :------- | :--------------- | :----------------- | :-------------- |
| 业务改造 | 无               | 无                 | 实现相关接口    |
| 一致性   | 不支持           | 支持               | 最终一致        |
| 隔离性   | 不支持           | 支持               | 业务方保证      |
| 并发性能 | 无影响           | 严重衰退           | 略微衰退        |
| 适合场景 | 业务方处理不一致 | 短事务 & 低并发    | 长事务 & 高并发 |

#### 两阶段事务-XA

两阶段事务提交采用的是X/OPEN组织所定义的[DTP模型](http://pubs.opengroup.org/onlinepubs/009680699/toc.pdf)，通过抽象出来的`AP`, `TM`, `RM`的概念可以保证事务的强一致性。 其中`TM`和`RM`间采用`XA`的协议进行双向通信。 与传统的本地事务相比，XA事务增加了prepare阶段，数据库除了被动接受提交指令外，还可以反向通知调用方事务是否可以被提交。 因此`TM`可以收集所有分支事务的prepare结果，最后进行原子的提交，保证事务的强一致性。

[![两阶段提交模型](https://shardingsphere.apache.org/document/current/img/transaction/2pc-tansaction-modle_cn.png)](https://shardingsphere.apache.org/document/current/img/transaction/2pc-tansaction-modle_cn.png)

Java通过定义JTA接口实现了XA的模型，JTA接口里的`ResourceManager`需要数据库厂商提供XA的驱动实现，而`TransactionManager`则需要事务管理器的厂商实现，传统的事务管理器需要同应用服务器绑定，因此使用的成本很高。 而嵌入式的事务管器可以以jar包的形式提供服务，同ShardingSphere集成后，可保证分片后跨库事务强一致性。

通常，只有使用了事务管理器厂商所提供的XA事务连接池，才能支持XA的事务。ShardingSphere整合XA事务时，分离了XA事务管理和连接池管理，这样接入XA时，可以做到对业务的零侵入。

实现原理

ShardingSphere里定义了分布式事务的SPI接口ShardingTransactionManager，Sharding-JDBC和Sharding-Proxy为分布式事务的两个接入端。`XAShardingTransactionManager`为分布式事务的XA实现类，通过引入`sharding-transaction-xa-core`依赖，即可加入ShardingSphere 的分布式事务生态中。XAShardingTransactionManager主要负责对actual datasource进行管理和适配，并且将接入端事务的begin/commit/rollback操作委托给具体的XA事务管理器。

[![XA事务实现原理](https://shardingsphere.apache.org/document/current/img/transaction/2pc-xa-transaction-design_cn.png)](https://shardingsphere.apache.org/document/current/img/transaction/2pc-xa-transaction-design_cn.png)

1.Begin（开启XA全局事务）

通常收到接入端的set autoCommit=0时，XAShardingTransactionManager会调用具体的XA事务管理器开启XA的全局事务，通常以XID的形式进行标记。

2.执行物理SQL

ShardingSphere进行解析/优化/路由后，会生成逻辑SQL的分片SQLUnit，执行引擎为每个物理SQL创建连接的同时，物理连接所对应的XAResource也会被注册到当前XA事务中，事务管理器会在此阶段发送`XAResource.start`命令给数据库，数据库在收到`XAResource.end`命令之前的所有SQL操作，会被标记为XA事务。

例如:

```java
XAResource1.start             ## Enlist阶段执行
statement.execute("sql1");    ## 模拟执行一个分片SQL1
statement.execute("sql2");    ## 模拟执行一个分片SQL2
XAResource1.end               ## 提交阶段执行
```

这里sql1和sql2将会被标记为XA事务。

3.Commit/rollback（提交XA事务）

XAShardingTransactionManager收到接入端的提交命令后，会委托实际的XA事务管理进行提交动作，这时事务管理器会收集当前线程里所有注册的XAResource，首先发送`XAResource.end`指令，用以标记此XA事务的边界。 接着会依次发送prepare指令，收集所有参与XAResource投票，如果所有XAResource的反馈结果都是OK，则会再次调用commit指令进行最终提交，如果有一个XAResource的反馈结果为No，则会调用rollback指令进行回滚。 在事务管理器发出提交指令后，任何XAResource产生的异常都会通过recovery日志进行重试，来保证提交阶段的操作原子性，和数据强一致性。

例如:

```java
XAResource1.prepare           ## ack: yes
XAResource2.prepare           ## ack: yes
XAResource1.commit
XAResource2.commit
     
XAResource1.prepare           ## ack: yes
XAResource2.prepare           ## ack: no
XAResource1.rollback
XAResource2.rollback
```

#### SAGA柔性事务

Saga事务

Saga这个概念来源于三十多年前的一篇数据库论文[Sagas](http://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf) ，一个Saga事务是一个有多个短时事务组成的长时的事务。 在分布式事务场景下，我们把一个Saga分布式事务看做是一个由多个本地事务组成的事务，每个本地事务都有一个与之对应的补偿事务。在Saga事务的执行过程中，如果某一步执行出现异常，Saga事务会被终止，同时会调用对应的补偿事务完成相关的恢复操作，这样保证Saga相关的本地事务要么都是执行成功，要么通过补偿恢复成为事务执行之前的状态。

自动反向补偿

Saga定义了一个事务中的每个子事务都有一个与之对应的反向补偿操作。由Saga事务管理器根据程序执行结果生成一张有向无环图，并在需要执行回滚操作时，根据该图依次按照相反的顺序调用反向补偿操作。Saga事务管理器只用于控制何时重试，何时补偿，并不负责补偿的内容，补偿的具体操作需要由开发者自行提供。

ShardingSphere采用反向SQL技术，将对数据库进行更新操作的SQL自动生成反向SQL，并交由[saga-actuator](https://github.com/apache/servicecomb-saga-actuator)执行，使用方则无需再关注如何实现补偿方法，将柔性事务管理器的应用范畴成功的定位回了事务的本源——数据库层面。

实现原理

Saga柔性事务的实现类为`SagaShardingTransactionMananger`, ShardingSphere通过Hook的方式拦截逻辑SQL的解析和路由结果，这样，在分片物理SQL执行前，可以生成逆向SQL，在事务提交阶段再把SQL调用链交给Saga引擎处理。

[![柔性事务Saga](https://shardingsphere.apache.org/document/current/img/transaction/sharding-transaction-base-saga-design.png)](https://shardingsphere.apache.org/document/current/img/transaction/sharding-transaction-base-saga-design.png)

1.Init（Saga引擎初始化）

包含Saga柔性事务的应用启动时，saga-actuator引擎会根据`saga.properties`的配置进行初始化的流程。

2.Begin（开启Saga全局事务）

每次开启Saga全局事务时，将会生成本次全局事务的上下文（`SagaTransactionContext`），事务上下文记录了所有子事务的正向SQL和逆向SQL，作为生成事务调用链的元数据使用。

3.执行物理SQL

在物理SQL执行前，ShardingSphere根据SQL的类型生成逆向SQL，这里是通过Hook的方式拦截Parser的解析结果进行实现。

4.Commit/rollback（提交Saga事务）

提交阶段会生成Saga执行引擎所需的调用链路图，commit操作产生ForwardRecovery（正向SQL补偿）任务，rollback操作产生BackwardRecovery任务（逆向SQL补偿）。

#### SEATA柔性事务

Seata柔性事务

[Seata](https://github.com/seata/seata)是阿里集团和蚂蚁金服联合打造的分布式事务框架，截止到0.5.x版本包含了AT事务和TCC事务。其中AT事务的目标是在微服务架构下，提供增量的事务ACID语意，让用户像使用本地事务一样，使用分布式事务，核心理念同ShardingSphere一脉相承。

Seata AT事务模型

`Seata AT`事务模型包含TM(事务管理器)，RM(资源管理器)，TC(事务协调器)。其中TC是一个独立的服务需要单独部署，TM和RM以jar包的方式同业务应用部署在一起，它们同TC建立长连接，在整个事务生命周期内，保持RPC通信。 其中全局事务的发起方作为TM，全局事务的参与者作为RM ; TM负责全局事务的begin和commit/rollback，RM负责分支事务的执行结果上报，并且通过TC的协调进行commit/rollback。

[![Seata AT事务模型](https://shardingsphere.apache.org/document/current/img/transaction/seata-at-transaction.png)](https://shardingsphere.apache.org/document/current/img/transaction/seata-at-transaction.png)

实现原理

整合`Seata AT`事务时，需要把TM，RM，TC的模型融入到ShardingSphere 分布式事务的SPI的生态中。在数据库资源上，Seata通过对接DataSource接口，让JDBC操作可以同TC进行RPC通信。同样，ShardingSphere也是面向DataSource接口对用户配置的物理DataSource进行了聚合，因此把物理DataSource二次包装为Seata 的DataSource后，就可以把Seata AT事务融入到ShardingSphere的分片中。

[![柔性事务Seata](https://shardingsphere.apache.org/document/current/img/transaction/sharding-transaciton-base-seata-at-design.png)](https://shardingsphere.apache.org/document/current/img/transaction/sharding-transaciton-base-seata-at-design.png)

1.Init（Seata引擎初始化）

包含Seata柔性事务的应用启动时，用户配置的数据源会按`seata.conf`的配置，适配为Seata事务所需的`DataSourceProxy`，并且注册到RM中。

2.Begin（开启Seata全局事务）

TM控制全局事务的边界，TM通过向TC发送Begin指令，获取全局事务ID，所有分支事务通过此全局事务ID，参与到全局事务中；全局事务ID的上下文存放在当前线程变量中。

3.执行分片物理SQL

处于Seata全局事务中的分片SQL通过RM生成undo快照，并且发送participate指令到TC，加入到全局事务中。ShardingSphere的分片物理SQL是按多线程方式执行，因此整合Seata AT事务时，需要在主线程和子线程间进行全局事务ID的上下文传递，这同服务间的上下文传递思路完全相同。

4.Commit/rollback（提交Seata事务）

提交Seata事务时，TM会向TC发送全局事务的commit和rollback指令，TC根据全局事务ID协调所有分支事务进行commit和rollback。

### SPI

在Apache ShardingSphere中，很多功能实现类的加载方式是通过SPI注入的方式完成的。 [Service Provider Interface (SPI)](https://docs.oracle.com/javase/tutorial/sound/SPI-intro.html)是一种为了被第三方实现或扩展的API，它可以用于实现框架扩展或组件替换。

Apache ShardingSphere之所以采用SPI方式进行扩展，是出于整体架构最优设计考虑。 为了让高级用户通过实现Apache ShardingSphere提供的相应接口，动态将用户自定义的实现类加载其中，从而在保持Apache ShardingSphere架构完整性与功能稳定性的情况下，满足用户不同场景的实际需求。

本章节汇总了Apache ShardingSphere所有通过SPI方式载入的功能模块。 如无特殊需求，用户可以使用Apache ShardingSphere提供的内置实现，并通过简单配置即可实现相应功能；高级用户则可以参考各个功能模块的接口进行自定义实现。 我们非常欢迎大家将您的实现类反馈至[开源社区](https://github.com/apache/incubator-shardingsphere/pulls)，让更多用户从中收益。

### 弹性伸缩

数据库节点的单机处理能力是有限的，而ShardingSphere提供了数据分片的能力，可以将数据分散到不同的数据库节点上，提升了整体的处理能力。

但对于已经使用单机数据库运行的业务来说，如何安全简单地把数据迁移至新的分片上，一直以来都是一个迫切的需求。

同时，对于已经使用了ShardingSphere的用户来说，业务规模的快速变化，也可能导致单一数据库分片甚至整体分片集群达到处理瓶颈。

如何对现有的分片集群进行弹性的伸缩，同样也是一个迫切需求。

Sharding-Scaling是一个提供给用户的通用的ShardingSphere数据接入迁移，及弹性伸缩的解决方案。

从**4.1.0**开始向用户提供。

[![结构总揽](https://shardingsphere.apache.org/document/current/img/scaling/scaling-overview.cn.png)](https://shardingsphere.apache.org/document/current/img/scaling/scaling-overview.cn.png)

#### 挑战

ShardingSphere在分片策略和算法上提供给用户极大的自由度，但却给弹性伸缩造成了极大的挑战。如何找到一种方式，即能支持各类不同用户的分片策略和算法，又能高效地将数据节点进行伸缩，是弹性伸缩面临的第一个挑战；

同时，弹性伸缩过程中，不应该对正在运行的业务造成影响，尽可能减少伸缩时数据不可用的时间窗口，甚至做到用户完全无感知，是弹性伸缩的一个大挑战；

最后，弹性伸缩不应该现有的数据造成影响，如何保证数据的可用性和正确性，是弹性伸缩的第三个挑战。

#### 目标

支持各类用户自定义的分片策略，减少用户在数据伸缩及迁移时的重复工作及业务影响，提供一站式的通用弹性伸缩解决方案，是ShardingSphere弹性伸缩的主要设计目标。

#### 状态

当前处于alpha开发阶段。

[![路线图](https://shardingsphere.apache.org/document/current/img/scaling/roadmap.cn.png)](https://shardingsphere.apache.org/document/current/img/scaling/roadmap.cn.png)

#### 弹性伸缩作业

指一次将数据由旧分片规则伸缩至新分片规则的完整流程。

#### 数据节点

同数据分片中的[数据节点](https://shardingsphere.apache.org/document/current/cn/features/sharding/concept/sql/)

#### 存量数据

在弹性伸缩作业开始前，数据分片中已有的数据。

#### 增量数据

在弹性伸缩作业执行过程中，业务系统所产生的新数据。

#### 核心功能

1. 首次使用ShardingSphere时，将已有数据迁移至ShardingSphere；
2. 已使用ShardingSphere，进行数据节点的扩容或缩容；
3. 已使用ShardingSphere，进行分片策略的修改；

#### 实现原理

考虑到ShardingSphere的弹性伸缩模块的几个挑战，目前的弹性伸缩解决方案为：临时地使用两个数据库集群，伸缩完成后切换的方式实现。

[![伸缩总揽](https://shardingsphere.apache.org/document/current/img/scaling/scaling-principle-overview.cn.png)](https://shardingsphere.apache.org/document/current/img/scaling/scaling-principle-overview.cn.png)

这种实现方式有以下优点：

1. 伸缩过程中，原始数据没有任何影响。
2. 伸缩失败无风险。
3. 不受分片策略限制。

同时也存在一定的缺点：

1. 在一定时间内存在冗余服务器
2. 所有数据都需要移动

弹性伸缩模块会通过解析旧分片规则，提取配置中的数据源、数据节点等信息，之后创建伸缩作业工作流，将一次弹性伸缩拆解为4个主要阶段

1. 准备阶段
2. 存量数据迁移阶段
3. 增量数据同步阶段
4. 规则切换阶段

[![伸缩工作流](https://shardingsphere.apache.org/document/current/img/scaling/workflow.cn.png)](https://shardingsphere.apache.org/document/current/img/scaling/workflow.cn.png)

准备阶段

在准备阶段，弹性伸缩模块会进行数据源连通性及权限的校验，同时进行存量数据的统计、日志位点的记录，最后根据数据量和用户设置的并行度，对任务进行分片。

存量数据迁移阶段

执行在准备阶段拆分好的存量数据迁移作业，存量迁移阶段采用JDBC查询的方式，直接从数据节点中读取数据，并使用新规则写入到新集群中。

增量数据同步阶段

由于存量数据迁移耗费的时间受到数据量和并行度等因素影响，此时需要对这段时间内业务新增的数据进行同步。不同的数据库使用的技术细节不同，但总体上均为基于复制协议或WAL日志实现的变更数据捕获功能。

- MySQL：订阅并解析binlog
- PostgreSQL：采用官方逻辑复制 [test_decoding](https://www.postgresql.org/docs/9.4/test-decoding.html)

这些捕获的增量数据，同样会由弹性伸缩模块根据新规则写入到新数据节点中。当增量数据基本同步完成时（由于业务系统未停止，增量数据是不断的），则进入规则切换阶段。

规则切换阶段

在此阶段，可能存在一定时间的业务只读窗口期，通过设置数据库只读或ShardingSphere的熔断机制，让旧数据节点中的数据短暂静态，确保增量同步已完全完成。

这个窗口期时间短则数秒，长则数分钟，取决于数据量和用户是否需要对数据进行强校验。确认完成后，ShardingSphere可通过配置中心修改配置，将业务导向新规则的集群，弹性伸缩完成。