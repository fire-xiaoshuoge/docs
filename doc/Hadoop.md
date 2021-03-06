# Apache Hadoop

## Hadoop

Hadoop是Apache基金会下的一个开源分布式计算平台，以Hadoop分布式文件系统（Hadoop Distributed File System，HDFS）和MapReduce分布式计算框架为核心，为用户提供了底层细节透明的分布式基础设施。HDFS的高容错性、高伸缩性等优点，允许用户将Hadoop部署在廉价的硬件上，构建分布式系统；MapReduce分布式计算计算框架则允许用户在不了解分布式系统底层细节的情况下开发并行、分布的应用程序，充分利用大规模的计算资源，解决传统高性能单机无法解决的大数据处理问题。

### Hadoop生态系统

#### Common

从Hadoop 0.20版本开始，原来Hadoop项目的Core部分更名为Hadoop Common。Common为Hadoop的其他项目提供了一些常用工具，主要包括系统配置工具Configuration、远程过程调用RPC、序列化机制和Hadoop抽象文件系统FileSystem等。它们为在通用硬件上搭建云计算环境提供基本的服务，并为运行在该平台上的软件开发提供了所需的API。

#### Avro

Avro由Doug Cutting牵头开发，是一个数据序列化系统。类似于其他序列化机制，Avro可以将数据结构或者对象转换成便于存储和传输的格式，其设计目标是用于支持数据密集型应用，适合大规模数据的存储与交换。Avro提供了丰富的数据结构类型、快速可压缩的二进制数据格式、存储持久性数据的文件集、远程调用RPC和简单动态语言集成等功能。

#### ZooKeeper

在分布式系统中如何就某个值（决议）达成一致，是一个十分重要的基础问题。ZooKeeper作为一个分布式的服务框架，解决了分布式计算中的一致性问题。在此基础上，ZooKeeper可用于处理分布式应用中经常遇到的一些数据管理问题，如统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。ZooKeeper常作为其他Hadoop相关项目的主要组件，发挥着越来越重要的作用。

#### HDFS

HDFS（Hadoop Distributed File System，Hadoop分布式文件系统）是Hadoop体系中数据存储管理的基础。它是一个高度容错的系统，能检测和应对硬件故障，用于在低成本的通用硬件上运行。HDFS简化了文件的一致性模型，通过流式数据访问，提供高吞吐量应用程序数据访问功能，适合带有大型数据集的应用程序。

#### MapReduce

MapReduce是一种计算模型，用以进行大数据量的计算。Hadoop的MapReduce实现，和Common、HDFS一起，构成了Hadoop发展初期的三个组件。MapReduce将应用划分为Map和Reduce两个步骤，其中Map对数据集上的独立元素进行指定的操作，生成键-值对形式中间结果。Reduce则对中间结果中相同“键”的所有“值”进行规约，以得到最终结果。MapReduce这样的功能划分，非常适合在大量计算机组成的分布式并行环境里进行数据处理。

#### HBase

Google发表了BigTable系统论文后，开源社区就开始在HDFS上构建相应的实现HBase。HBase是一个针对结构化数据的可伸缩、高可靠、高性能、分布式和面向列的动态模式数据库。和传统关系数据库不同，HBase采用了BigTable的数据模型：增强的稀疏排序映射表（Key/Value），其中，键由行关键字、列关键字和时间戳构成。HBase提供了对大规模数据的随机、实时读写访问，同时，HBase中保存的数据可以使用MapReduce来处理，它将数据存储和并行计算完美地结合在一起。

#### Hive

Hive是Hadoop中的一个重要子项目，最早由Facebook设计，是建立在Hadoop基础上的数据仓库架构，它为数据仓库的管理提供了许多功能，包括：数据ETL（抽取、转换和加载）工具、数据存储管理和大型数据集的查询和分析能力。Hive提供的是一种结构化数据的机制，定义了类似于传统关系数据库中的类SQL语言：Hive QL，通过该查询语言，数据分析人员可以很方便地运行数据分析业务。

#### Pig

Pig运行在Hadoop上，是对大型数据集进行分析和评估的平台。它简化了使用Hadoop进行数据分析的要求，提供了一个高层次的、面向领域的抽象语言：Pig Latin。通过Pig Latin，数据工程师可以将复杂且相互关联的数据分析任务编码为Pig操作上的数据流脚本，通过将该脚本转换为MapReduce任务链，在Hadoop上执行。和Hive一样，Pig降低了对大型数据集进行分析和评估的门槛。

#### Mahout

Mahout起源于2008年，最初是Apache Lucent的子项目，它在极短的时间内取得了长足的发展，现在是Apache的顶级项目。Mahout的主要目标是创建一些可扩展的机器学习领域经典算法的实现，旨在帮助开发人员更加方便快捷地创建智能应用程序。Mahout现在已经包含了聚类、分类、推荐引擎（协同过滤）和频繁集挖掘等广泛使用的数据挖掘方法。除了算法，Mahout还包含数据的输入/输出工具、与其他存储系统（如数据库、MongoDB或Cassandra）集成等数据挖掘支持架构。

#### X-RIME

X-RIME是一个开源的社会网络分析工具，它提供了一套基于Hadoop的大规模社会网络/复杂网络分析工具包。X-RIME在MapReduce的框架上对十几种社会网络分析算法进行了并行化与分布式化，从而实现了对互联网级大规模社会网络/复杂网络的分析。它包括HDFS存储系统上的一套适合大规模社会网络分析的数据模型、基于MapReduce实现的一系列社会网络分析分布式并行算法和X-RIME处理模型，即X-RIME工具链等三部分。

#### Crossbow

Crossbow是在Bowtie和SOAPsnp基础上，结合Hadoop的可扩展工具，该工具能够充分利用集群进行生物计算。其中，Bowtie是一个快速、高效的基因短序列拼接至模板基因组工具；SOAPsnp则是一个重测序一致性序列建造程序。它们在复杂遗传病和肿瘤易感的基因定位，到群体和进化遗传学研究中发挥着重要的作用。Crossbow利用了Hadoop Stream，将Bowtie、SOAPsnp上的计算任务分布到Hadoop集群中，满足了新一代基因测序技术带来的海量数据存储及计算分析要求。

#### Chukwa

Chukwa是开源的数据收集系统，用于监控大规模分布式系统（2000+以上的节点，系统每天产生的监控数据量在T级别）。它构建在Hadoop的HDFS和MapReduce基础之上，继承了Hadoop的可伸缩性和鲁棒性。Chukwa包含一个强大和灵活的工具集，提供了数据的生成、收集、排序、去重、分析和展示等一系列功能，是Hadoop使用者、集群运营人员和管理人员的必备工具。

#### Flume

Flume是Cloudera开发维护的分布式、可靠、高可用的日志收集系统。它将数据从产生、传输、处理并最终写入目标的路径的过程抽象为数据流，在具体的数据流中，数据源支持在Flume中定制数据发送方，从而支持收集各种不同协议数据。同时，Flume数据流提供对日志数据进行简单处理的能力，如过滤、格式转换等。此外，Flume还具有能够将日志写往各种数据目标（可定制）的能力。总的来说，Flume是一个可扩展、适合复杂环境的海量日志收集系统。

#### Sqoop

Sqoop是SQL-to-Hadoop的缩写，是Hadoop的周边工具，它的主要作用是在结构化数据存储与Hadoop之间进行数据交换。Sqoop可以将一个关系型数据库（例如MySQL、Oracle、PostgreSQL等）中的数据导入Hadoop的HDFS、Hive中，也可以将HDFS、Hive中的数据导入关系型数据库中。Sqoop充分利用了Hadoop的优点，整个数据导入导出过程都是用MapReduce实现并行化，同时，该过程中的大部分步骤自动执行，非常方便。

#### Oozie

在Hadoop中执行数据处理工作，有时候需要把多个作业连接到一起，才能达到最终目的。针对上述需求，Yahoo开发了开源工作流引擎Oozie，用于管理和协调多个运行在Hadoop平台上的作业。在Oozie中，计算作业被抽象为动作，控制流节点则用于构建动作间的依赖关系，它们一起组成一个有向无环的工作流，描述了一项完整的数据处理工作。Oozie工作流系统可以提高数据处理流程的柔性，改善Hadoop集群的效率，并降低开发和运营人员的工作量。

#### Karmasphere

Karmasphere包括Karmasphere Analyst和Karmasphere Studio。其中，Analyst提供了访问保存在Hadoop里面的结构化和非结构化数据的能力，用户可以运用SQL或其他语言，进行即时查询并做进一步的分析。Studio则是基于NetBeans的MapReduce集成开发环境，开发人员可以利用它方便快速地创建基于Hadoop的MapReduce应用。同时，该工具还提供了一些可视化工具，用于监控任务的执行，显示任务间的输入输出和交互等。需要注意的是，在上面提及的这些项目中，Karmasphere是唯一不开源的工具。





















### HDFS

HDFS（Hadoop Distributed File System，Hadoop分布式文件系统）是Hadoop体系中数据存储管理的基础。它是一个高度容错的系统，能检测和应对硬件故障，用于在低成本的通用硬件上运行。HDFS简化了文件的一致性模型，通过流式数据访问，提供高吞吐量应用程序数据访问功能，适合带有大型数据集的应用程序

### MapReduce

MapReduce是一种计算模型，用以进行大数据量的计算。Hadoop的MapReduce实现，和Common、HDFS一起，构成了Hadoop发展初期的三个组件。MapReduce将应用划分为Map和Reduce两个步骤，其中Map对数据集上的独立元素进行指定的操作，生成键-值对形式中间结果。Reduce则对中间结果中相同“键”的所有“值”进行规约，以得到最终结果。MapReduce这样的功能划分，非常适合在大量计算机组成的

### 压缩

计算机处理的数据都存在一些冗余度，同时数据中间，尤其是相邻数据间存在着相关性，所以可以通过一些有别于原始编码的特殊编码方式来保存数据，使数据占用的存储空间比较小，这个过程一般叫压缩。和压缩对应的概念是解压缩，就是将被压缩的数据从特殊编码方式还原为原始数据的过程。

压缩广泛应用于海量数据处理中，对数据文件进行压缩，可以有效减少存储文件所需的空间，并加快数据在网络上或者到磁盘上的传输速度。在Hadoop中，压缩应用于文件存储、Map阶段到Reduce阶段的数据交换（需要打开相关的选项）等情景。

### Hadoop支持的压缩格式

![image-20200226203246667](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226203246667.png)

### HDFS体系结构

为了支持流式数据访问和存储超大文件，HDFS引入了一些比较特殊的设计，在一个全配置的集群上，“运行HDFS”意味着在网络分布的不同服务器上运行一些守护进程（daemon），这些进程有各自的特殊角色，并相互配合，一起形成一个分布式文件系统。

HDFS采用了主从（Master/Slave）体系结构，名字节点NameNode、数据节点DataNode和客户端Client是HDFS中3个重要的角色。

在一个HDFS中，有一个名字节点和一个第二名字节点，典型的集群有几十到几百个数据节点，规模大的系统可以有上千、甚至几千个数据节点；而客户端，一般情况下，比数据节点的个数还多。名字节点和第二名字节点、数据节点和客户端的关系如图6-1所示。

![image-20200226205521355](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226205521355.png)

