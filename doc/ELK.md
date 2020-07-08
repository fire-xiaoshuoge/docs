## Elasticsearch

### Elasticsearch

ElasticSearch是一个基于[Lucene](https://baike.baidu.com/item/Lucene/6753302)的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java语言开发的，并作为Apache许可条款下的开放源码发布，是一种流行的企业级搜索引擎。ElasticSearch用于[云计算](https://baike.baidu.com/item/云计算/9969353)中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。

Elasticsearch提供了搜集、分析、存储数据三大功能，其主要特点有：分布式、零配置、易装易用、自动发现、索引自动分片、索引副本机制、RESTful风格接口、多数据源和自动搜索负载等。

### Elasticsearch的核心概念

（1）Node：即节点。节点是组成Elasticsearch集群的基本服务单元，集群中的每个运行中的Elasticsearch服务器都可称之为节点。

（2） Cluster：即集群。Elasticsearch的集群是由具有相同cluster.name （默认值为elasticsearch）的一个或多个Elasticsearch节点组成的，各个节点协同工作，共享数据。同一个集群内节点的名字不能重复，但集群名称一定要相同。在实际使用Elasticsearch集群时，一般需要给集群起一个合适的名字来替代cluster.name的默认值。自定义集群名称的好处是，可以防止一个新启动的节点加入相同网络中的另一个同名的集群中。在Elasticsearch集群中，节点的状态有Green、Yellow和Red三种，分别如下所述。

① Green：绿色，表示节点运行状态为健康状态。所有的主分片和副本分片都可以正常工作，集群100%健康。

② Yellow：黄色，表示节点的运行状态为预警状态。所有的主分片都可以正常工作，但至少有一个副本分片是不能正常工作的。此时集群依然可以正常工作，但集群的高可用性在某种程度上被弱化。

③ Red：红色，表示集群无法正常使用。此时，集群中至少有一个分片的主分片及它的全部副本分片都不可正常工作。虽然集群的查询操作还可以进行，但是也只能返回部分数据（其他正常分片的数据可以返回），而分配到这个有问题分片上的写入请求将会报错，最终导致数据丢失。

（3）Shards：即分片。当索引的数据量太大时，受限于单个节点的内存、磁盘处理能力等，节点无法足够快地响应客户端的请求，此时需要将一个索引上的数据进行水平拆分。拆分出来的每个数据部分称之为一个分片。一般来说，每个分片都会放到不同的服务器上。

（4）Replicas：即备份，也可称之为副本。副本指的是对主分片的备份，这种备份是精确复制模式。每个主分片可以有零个或多个副本，主分片和备份分片都可以对外提供数据查询服务。当构建索引进行写入操作时，首先在主分片上完成数据的索引，然后数据会从主分片分发到备份分片上进行索引。

（5）Index：即索引。在Elasticsearch中，索引由一个和多个分片组成。在使用索引时，需要通过索引名称在集群内进行唯一标识。

（6）Type：即类别。类别指的是索引内部的逻辑分区，通过Type的名字在索引内进行唯一标识。在查询时如果没有该值，则表示需要在整个索引中查询。

（7）Document：即文档。索引中的每一条数据叫作一个文档，与关系数据库的使用方法类似，一条文档数据通过_id在Type内进行唯一标识。

（8）Settings：Settings是对集群中索引的定义信息，比如一个索引默认的分片数、副本数等。

（9）Mapping：Mapping表示中保存了定义索引中字段（Field）的存储类型、分词方式、是否存储等信息，有点类似于关系数据库（如MySQL）中的表结构信息。

（10） Analyzer：Analyzer表示的是字段分词方式的定义。一个Analyzer通常由一个Tokenizer和零到多个Filter组成。在Elasticsearch中，默认的标准Analyzer包含一个标准的Tokenizer和三个Filter，即Standard Token Filter、Lower Case Token Filter和Stop TokenFilter。

### Elasticsearch的架构设计

![image-20200510084103477](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200510084103477.png)

### Elasticsearch的节点自动发现机制

Elasticsearch内嵌自动发现功能，主要提供了4种可供选择的发现机制。其中一种是默认实现，其他都是通过插件实现的，具体如下所示。

（1）Azure discovery插件方式：多播模式。

（2）EC2 discovery插件方式：多播模式。

（3）Google Compute Engine（GCE）discovery插件方式：多播模式。

（4）Zen Discovery，默认实现方式，支持多播模式和单播模式。

1.单播模式

Elasticsearch支持多播模式和单播模式自动两种节点发现机制，不过多播模式已经不被大多数操作系统所支持，加之其安全性不高，所以一般我们会主动关闭多播模式。

在Elasticsearch中，发现机制默认被配置为使用单播模式，以防止节点无意中加入集群。Elasticsearch支持同一个主机启动多个节点，因此只有在同一台机器上运行的节点才会自动组成集群。

2.多播模式

在多播模式下，我们仅需在每个节点配置好集群名称和节点名称即可。互相通信的节点会根据Elasticsearch自定义的服务发现协议，按照多播的方式寻找网络上配置在同样集群内的节点。