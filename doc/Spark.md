## Spark简介

### Spark

Spark是基于内存计算的大数据并行计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和高可伸缩性，允许用户将Spark部署在大量廉价硬件之上，形成集群。

Spark是MapReduce的替代方案，而且兼容HDFS、Hive等分布式存储层，可融入Hadoop的生态系统，以弥补缺失MapReduce的不足。

**Spark相比Hadoop MapReduce的优势如下。**

（1）中间结果输出

基于MapReduce的计算引擎通常会将中间结果输出到磁盘上，进行存储和容错。出于任务管道承接的考虑，当一些查询翻译到MapReduce任务时，往往会产生多个Stage，而这些串联的Stage又依赖于底层文件系统（如HDFS）来存储每一个Stage的输出结果。

Spark将执行模型抽象为通用的有向无环图执行计划（DAG），这可以将多Stage的任务串联或者并行执行，而无须将Stage中间结果输出到HDFS中。类似的引擎包括Dryad、Tez。

（2）数据格式和内存布局

由于MapReduce Schema on Read处理方式会引起较大的处理开销。Spark抽象出分布式内存存储结构弹性分布式数据集RDD，进行数据的存储。RDD能支持粗粒度写操作，但对于读取操作，RDD可以精确到每条记录，这使得RDD可以用来作为分布式索引。Spark的特性是能够控制数据在不同节点上的分区，用户可以自定义分区策略，如Hash分区等。Shark和Spark SQL在Spark的基础之上实现了列存储和列存储压缩。

（3）执行策略

MapReduce在数据Shuffle之前花费了大量的时间来排序，Spark则可减轻上述问题带来的开销。因为Spark任务在Shuffle中不是所有情景都需要排序，所以支持基于Hash的分布式聚合，调度中采用更为通用的任务执行计划图（DAG），每一轮次的输出结果在内存缓存。

（4）任务调度的开销

传统的MapReduce系统，如Hadoop，是为了运行长达数小时的批量作业而设计的，在某些极端情况下，提交一个任务的延迟非常高。Spark采用了事件驱动的类库AKKA来启动任务，通过线程池复用线程来避免进程或线程启动和切换开销。

**Spark能带来什么？**

（1）打造全栈多计算范式的高效数据流水线

Spark支持复杂查询。在简单的“map”及“reduce”操作之外，Spark还支持SQL查询、流式计算、机器学习和图算法。同时，用户可以在同一个工作流中无缝搭配这些计算范式。

（2）轻量级快速处理

Spark 1.0核心代码只有4万行。这是由于Scala语言的简洁和丰富的表达力，以及Spark充分利用和集成Hadoop等其他第三方组件，同时着眼于大数据处理，数据处理速度是至关重要的，Spark通过将中间结果缓存在内存减少磁盘I/O来达到性能的提升。

（3）易于使用，Spark支持多语言

Spark支持通过Scala、Java及Python编写程序，这允许开发者在自己熟悉的语言环境下进行工作。它自带了80多个算子，同时允许在Shell中进行交互式计算。用户可以利用Spark像书写单机程序一样书写分布式程序，轻松利用Spark搭建大数据内存计算平台并充分利用内存计算，实现海量数据的实时处理。

（4）与HDFS等存储层兼容

Spark可以独立运行，除了可以运行在当下的YARN等集群管理系统之外，它还可以读取已有的任何Hadoop数据。这是个非常大的优势，它可以运行在任何Hadoop数据源上，如Hive、HBase、HDFS等。这个特性让用户可以轻易迁移已有的持久化层数据。

### Spark生态系统BDAS

伯克利将Spark的整个生态系统称为伯克利数据分析栈（BDAS）。其核心框架是Spark，同时BDAS涵盖支持结构化数据SQL查询与分析的查询引擎Spark SQL和Shark，提供机器学习功能的系统MLbase及底层的分布式机器学习库MLlib、并行图计算框架GraphX、流计算框架Spark Streaming、采样近似计算查询引擎BlinkDB、内存分布式文件系统Tachyon、资源管理框架Mesos等子项目。这些子项目在Spark上层提供了更高层、更丰富的计算范式。

![image-20200513101900924](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513101900924.png)

（1）Spark

Spark是整个BDAS的核心组件，是一个大数据分布式编程框架，不仅实现了MapReduce的算子map函数和reduce函数及计算模型，还提供更为丰富的算子，如filter、join、groupByKey等。Spark将分布式数据抽象为弹性分布式数据集（RDD），实现了应用任务调度、RPC、序列化和压缩，并为运行在其上的上层组件提供API。其底层采用Scala这种函数式语言书写而成，并且所提供的API深度借鉴Scala函数式的编程思想，提供与Scala类似的编程接口。

（2）Shark

Shark是构建在Spark和Hive基础之上的数据仓库。目前，Shark已经完成学术使命，终止开发，但其架构和原理仍具有借鉴意义。它提供了能够查询Hive中所存储数据的一套SQL接口，兼容现有的Hive QL语法。这样，熟悉Hive QL或者SQL的用户可以基于Shark进行快速的Ad-Hoc、Reporting等类型的SQL查询。Shark底层复用Hive的解析器、优化器以及元数据存储和序列化接口。Shark会将Hive QL编译转化为一组Spark任务，进行分布式运算。

（3）Spark SQL

Spark SQL提供在大数据上的SQL查询功能，类似于Shark在整个生态系统的角色，它们可以统称为SQL on Spark。之前，Shark的查询编译和优化器依赖于Hive，使得Shark不得不维护一套Hive分支，而Spark SQL使用Catalyst做查询解析和优化器，并在底层使用Spark作为执行引擎实现SQL的Operator。用户可以在Spark上直接书写SQL，相当于为Spark扩充了一套SQL算子，这无疑更加丰富了Spark的算子和功能，同时Spark SQL不断兼容不同的持久化存储（如HDFS、Hive等），为其发展奠定广阔的空间。

（4）Spark Streaming

Spark Streaming通过将流数据按指定时间片累积为RDD，然后将每个RDD进行批处理，进而实现大规模的流数据处理。其吞吐量能够超越现有主流流处理框架Storm，并提供丰富的API用于流数据计算。

（5）GraphX

GraphX基于BSP模型，在Spark之上封装类似Pregel的接口，进行大规模同步全局的图计算，尤其是当用户进行多轮迭代时，基于Spark内存计算的优势尤为明显。

（6）Tachyon

Tachyon是一个分布式内存文件系统，可以理解为内存中的HDFS。为了提供更高的性能，将数据存储剥离Java Heap。用户可以基于Tachyon实现RDD或者文件的跨应用共享，并提供高容错机制，保证数据的可靠性。

（7）Mesos

Mesos是一个资源管理框架，提供类似于YARN的功能。用户可以在其中插件式地运行Spark、MapReduce、Tez等计算框架的任务。Mesos会对资源和任务进行隔离，并实现高效的资源任务调度。

（8）BlinkDB

BlinkDB是一个用于在海量数据上进行交互式SQL的近似查询引擎。它允许用户通过在查询准确性和查询响应时间之间做出权衡，完成近似查询。其数据的精度被控制在允许的误差范围内。为了达到这个目标，BlinkDB的核心思想是：通过一个自适应优化框架，随着时间的推移，从原始数据建立并维护一组多维样本；通过一个动态样本选择策略，选择一个适当大小的示例，然后基于查询的准确性和响应时间满足用户查询需求。

### Spark架构

**1.Spark的代码结构**

![image-20200513102444853](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513102444853.png)

scheduler：文件夹中含有负责整体的Spark应用、任务调度的代码。

broadcast：含有Broadcast（广播变量）的实现代码，API中是Java和Python API的实现。

deploy：含有Spark部署与启动运行的代码。

common：不是一个文件夹，而是代表Spark通用的类和逻辑实现，有5000行代码。

metrics：是运行时状态监控逻辑代码，Executor中含有Worker节点负责计算的逻辑代码。

partial：含有近似评估代码。

network：含有集群通信模块代码。

serializer：含有序列化模块的代码。

storage：含有存储模块的代码。

ui：含有监控界面的代码逻辑。其他的代码模块分别是对Spark生态系统中其他组件的实现。

streaming：是Spark Streaming的实现代码。

YARN：是Spark on YARN的部分实现代码。

graphx：含有GraphX实现代码。

interpreter：代码交互式Shell的代码量为3300行。

mllib：代表MLlib算法实现的代码量。

sql代表Spark SQL的代码量。

**2.Spark的架构**

Spark架构采用了分布式计算中的Master-Slave模型。Master是对应集群中的含有Master进程的节点，Slave是集群中含有Worker进程的节点。Master作为整个集群的控制器，负责整个集群的正常运行；Worker相当于是计算节点，接收主节点命令与进行状态汇报；Executor负责任务的执行；Client作为用户的客户端负责提交应用，Driver负责控制一个应用的执行，如图1-4所示。

![image-20200513104719866](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513104719866.png)

Spark集群部署后，需要在主节点和从节点分别启动Master进程和Worker进程，对整个集群进行控制。在一个Spark应用的执行过程中，Driver和Worker是两个重要角色。Driver程序是应用逻辑执行的起点，负责作业的调度，即Task任务的分发，而多个Worker用来管理计算节点和创建Executor并行处理任务。在执行阶段，Driver会将Task和Task所依赖的file和jar序列化后传递给对应的Worker机器，同时Executor对相应数据分区的任务进行处理。

下面详细介绍Spark的架构中的基本组件。

□ClusterManager：在Standalone模式中即为Master（主节点），控制整个集群，监控Worker。在YARN模式中为资源管理器。

□Worker：从节点，负责控制计算节点，启动Executor或Driver。在YARN模式中为NodeManager，负责计算节点的控制。

□Driver：运行Application的main()函数并创建SparkContext。

□Executor：执行器，在worker node上执行任务的组件、用于启动线程池运行任务。每个Application拥有独立的一组Executors。

□SparkContext：整个应用的上下文，控制应用的生命周期。

□RDD：Spark的基本计算单元，一组RDD可形成执行的有向无环图RDD Graph。

□DAG Scheduler：根据作业（Job）构建基于Stage的DAG，并提交Stage给TaskScheduler。

□TaskScheduler：将任务（Task）分发给Executor执行。

□SparkEnv：线程级别的上下文，存储运行时的重要组件的引用。SparkEnv内创建并包含如下一些重要组件的引用。

□MapOutPutTracker：负责Shuffle元信息的存储。

□BroadcastManager：负责广播变量的控制与元信息的存储。

□BlockManager：负责存储管理、创建和查找块。

□MetricsSystem：监控运行时性能指标信息。

□SparkConf：负责存储配置信息。

## Spark集群的安装与部署

Spark的安装简便，用户可以在官网上下载到最新的软件包，网址为http://spark.apache.org/。

在Linux集群上安装与配置Spark。

1.安装JDK

2.安装Scala

3.配置SSH免密码登录

4.安装Hadoop

5.安装Spark

官网地址为http://spark.apache.org/downloads.html。

在Windows上安装与配置Spark。

（1）安装JDK

（2）安装Cygwin

（3）安装sshd并配置免密码登录

（4）配置SSH免密码登录

（5）配置Hadoop

（6）配置Spark

## Spark计算模型

Spark站在巨人的肩膀上，依靠Scala强有力的函数式编程、Actor通信模式、闭包、容器、泛型，借助统一资源分配调度框架Mesos，融合了MapReduce和Dryad，最后产生了一个简洁、直观、灵活、高效的大数据分布式处理框架。

### Spark程序模型

![image-20200513105947045](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513105947045.png)

在图3-1中，用户程序对RDD通过多个函数进行操作，将RDD进行转换。Block-Manager管理RDD的物理分区，每个Block就是节点上对应的一个数据块，可以存储在内存或者磁盘。而RDD中的partition是一个逻辑数据块，对应相应的物理块Block。本质上一个RDD在代码中相当于是数据的一个元数据结构，存储着数据分区及其逻辑结构映射关系，存储着RDD之前的依赖转换关系。

### 弹性分布式数据集

#### RDD简介

在集群背后，有一个非常重要的分布式数据架构，即弹性分布式数据集（resilient distributeddataset，RDD），它是逻辑集中的实体，在集群中的多台机器上进行了数据分区。通过对多台机器上不同RDD分区的控制，就能够减少机器之间的数据重排（data shuffling）。Spark提供了“partitionBy”运算符，能够通过集群中多台机器之间对原始RDD进行数据再分配来创建一个新的RDD。RDD是Spark的核心数据结构，通过RDD的依赖关系形成Spark的调度顺序。通过对RDD的操作形成整个Spark程序。

**（1）RDD的两种创建方式**

1）从Hadoop文件系统（或与Hadoop兼容的其他持久化存储系统，如Hive、Cassandra、Hbase）输入（如HDFS）创建。

2）从父RDD转换得到新的RDD。

**（2）RDD的两种操作算子**

对于RDD可以有两种计算操作算子：Transformation（变换）与Action（行动）。

1）Transformation（变换）。

Transformation操作是延迟计算的，也就是说从一个RDD转换生成另一个RDD的转换操作不是马上执行，需要等到有Actions操作时，才真正触发运算。

2）Action（行动）

Action算子会触发Spark提交作业（Job），并将数据输出到Spark系统。

**（3）RDD的重要内部属性**

1）分区列表。

2）计算每个分片的函数。

3）对父RDD的依赖列表。

4）对Key-Value 对数据类型RDD的分区器，控制分区策略和分区数。

5）每个数据分区的地址列表（如HDFS上的数据块的地址）。

##### RDD与分布式共享内存的异同

RDD是一种分布式的内存抽象，表3-1列出了RDD与分布式共享内存（Distributed SharedMemory，DSM）的对比。在DSM系统中，应用可以向全局地址空间的任意位置进行读写操作。DSM是一种通用的内存数据抽象，但这种通用性同时也使其在商用集群上实现有效的容错性和一致性更加困难。

RDD与DSM主要区别在于，不仅可以通过批量转换创建（即“写”）RDD，还可以对任意内存位置读写。RDD限制应用执行批量写操作，这样有利于实现有效的容错。特别是，由于RDD可以使用Lineage（血统）来恢复分区，基本没有检查点开销。失效时只需要重新计算丢失的那些RDD分区，就可以在不同节点上并行执行，而不需要回滚（Roll Back）整个程序。

![image-20200513110230756](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513110230756.png)

与DSM相比，RDD模型有两个优势。第一，对于RDD中的批量操作，运行时将根据数据存放的位置来调度任务，从而提高性能。第二，对于扫描类型操作，如果内存不足以缓存整个RDD，就进行部分缓存，将内存容纳不下的分区存储到磁盘上。

另外，RDD支持粗粒度和细粒度的读操作。RDD上的很多函数操作（如count和collect等）都是批量读操作，即扫描整个数据集，可以将任务分配到距离数据最近的节点上。同时，RDD也支持细粒度操作，即在哈希或范围分区的RDD上执行关键字查找。

#### Spark的数据存储

Spark数据存储的核心是弹性分布式数据集（RDD）。RDD可以被抽象地理解为一个大的数组（Array），但是这个数组是分布在集群上的。逻辑上RDD的每个分区叫一个Partition。

在Spark的执行过程中，RDD经历一个个的Transfomation算子之后，最后通过Action算子进行触发操作。逻辑上每经历一次变换，就会将RDD转换为一个新的RDD，RDD之间通过Lineage产生依赖关系，这个关系在容错中有很重要的作用。变换的输入和输出都是RDD。RDD会被划分成很多的分区分布到集群的多个节点中。分区是个逻辑概念，变换前后的新旧分区在物理上可能是同一块内存存储。这是很重要的优化，以防止函数式数据不变性（immutable）导致的内存需求无限扩张。有些RDD是计算的中间结果，其分区并不一定有相应的内存或磁盘数据与之对应，如果要迭代使用数据，可以调cache()函数缓存数据。

![image-20200513110805626](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513110805626.png)

图3-2中的RDD_1含有5个分区（p1、p2、p3、p4、p5），分别存储在4个节点（Node1、node2、Node3、Node4）中。RDD_2含有3个分区（p1、p2、p3），分布在3个节点（Node1、Node2、Node3）中。

在物理上，RDD对象实质上是一个元数据结构，存储着Block、Node等的映射关系，以及其他的元数据信息。一个RDD就是一组分区，在物理数据存储上，RDD的每个分区对应的就是一个Block，Block可以存储在内存，当内存不够时可以存储到磁盘上。

每个Block中存储着RDD所有数据项的一个子集，暴露给用户的可以是一个Block的迭代器（例如，用户可以通过mapPartitions获得分区迭代器进行操作），也可以就是一个数据项（例如，通过map函数对每个数据项并行计算）。本书会在后面章节具体介绍数据管理的底层实现细节。

如果是从HDFS等外部存储作为输入数据源，数据按照HDFS中的数据分布策略进行数据分区，HDFS中的一个Block对应Spark的一个分区。同时Spark支持重分区，数据通过Spark默认的或者用户自定义的分区器决定数据块分布在哪些节点。例如，支持Hash分区（按照数据项的Key值取Hash值，Hash值相同的元素放入同一个分区之内）和Range分区（将属于同一数据范围的数据放入同一分区）等分区策略。

### Spark算子分类及功能

1.Saprk算子的作用

图3-3描述了Spark的输入、运行转换、输出。在运行转换中通过算子对RDD进行转换。算子是RDD中定义的函数，可以对RDD中的数据进行转换和操作。

![image-20200513111328539](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513111328539.png)

1）输入：在Spark程序运行中，数据从外部数据空间（如分布式存储：textFile读取HDFS等，parallelize方法输入Scala集合或数据）输入Spark，数据进入Spark运行时数据空间，转化为Spark中的数据块，通过BlockManager进行管理。

2）运行：在Spark数据输入形成RDD后便可以通过变换算子，如fliter等，对数据进行操作并将RDD转化为新的RDD，通过Action算子，触发Spark提交作业。如果数据需要复用，可以通过Cache算子，将数据缓存到内存。

3）输出：程序运行结束数据会输出Spark运行时空间，存储到分布式存储中（如saveAsTextFile输出到HDFS），或Scala数据或集合中（collect输出到Scala集合，count返回Scala int型数据）。

Spark的核心数据模型是RDD，但RDD是个抽象类，具体由各子类实现，如MappedRDD、ShuffledRDD等子类。Spark将常用的大数据操作都转化成为RDD的子类。

2.算子的分类

大致可以分为三大类算子。

1）Value数据类型的Transformation算子，这种变换并不触发提交作业，针对处理的数据项是Value型的数据。

2）Key-Value数据类型的Transfromation算子，这种变换并不触发提交作业，针对处理的数据项是Key-Value型的数据对。

3）Action算子，这类算子会触发SparkContext提交Job作业。

**Value型Transformation算子**

处理数据类型为Value型的Transformation算子可以根据RDD变换算子的输入分区与输出分区关系分为以下几种类型。

1）输入分区与输出分区一对一型。

2）输入分区与输出分区多对一型。

3）输入分区与输出分区多对多型。

4）输出分区为输入分区子集型。

5）还有一种特殊的输入与输出分区一对一的算子类型：Cache型。Cache算子对RDD分区进行缓存。

**1.输入分区与输出分区一对一型**

（1）map

将原来RDD的每个数据项通过map中的用户自定义函数f映射转变为一个新的元素。源码中的map算子相当于初始化一个RDD，新RDD叫作MappedRDD(this,sc.clean(f))。

（2）flatMap

将原来RDD中的每个元素通过函数f转换为新的元素，并将生成的RDD的每个集合中的元素合并为一个集合。内部创建 FlatMappedRDD(this,sc.clean(f))。

（3）mapPartitions

mapPartitions函数获取到每个分区的迭代器，在函数中通过这个分区整体的迭代器对整个分区的元素进行操作。内部实现是生成MapPartitionsRDD。图3-6中的方框代表一个RDD分区。

（4）glom

glom函数将每个分区形成一个数组，内部实现是返回的GlommedRDD。图3-7中的每个方框代表一个RDD分区。

2.**输入分区与输出分区多对一型**

（1）union

使用union函数时需要保证两个RDD元素的数据类型相同，返回的RDD数据类型和被合并的RDD元素数据类型相同，并不进行去重操作，保存所有元素。如果想去重，可以使用distinct()。++符号相当于uion函数操作。

（2）cartesian

对两个RDD内的所有元素进行笛卡尔积操作。操作后，内部实现返回CartesianRDD。图3-9中左侧的大方框代表两个RDD，大方框内的小方框代表RDD的分区。右侧大方框代表合并后的RDD，大方框内的小方框代表分区。

3.**输入分区与输出分区多对多型**

groupBy：将元素通过函数生成相应的Key，数据就转化为Key-Value 格式，之后将Key相同的元素分为一组。

4.**输出分区为输入分区子集型**

（1）filter

filter的功能是对元素进行过滤，对每个元素应用f函数，返回值为true的元素在RDD中保留，返回为false的将过滤掉。内部实现相当于生成FilteredRDD(this，sc.clean(f))。

（2）distinct

distinct将RDD中的元素进行去重操作。

（3）subtract

subtract相当于进行集合的差操作，RDD 1去除RDD 1和RDD 2交集中的所有元素。

（4）sample

sample将RDD这个集合内的元素进行采样，获取所有元素的子集。用户可以设定是否有放回的抽样、百分比、随机种子，进而决定采样方式。

5）takeSample

takeSample()函数和上面的sample函数是一个原理，但是不使用相对比例采样，而是按设定的采样个数进行采样，同时返回结果不再是RDD，而是相当于对采样后的数据进行Collect()，返回结果的集合为单机的数组。

**5.Cache型**

（1）cache

cache将RDD元素从磁盘缓存到内存，相当于persist(MEMORY_ONLY)函数的功能。图3-14中的方框代表RDD分区。

（2）persis

persist函数对RDD进行缓存操作。数据缓存在哪里由StorageLevel枚举类型确定。

**Key-Value型Transformation算子**

**1.输入分区与输出分区一对一**

mapValues：针对（Key,Value）型数据中的 Value进行Map操作，而不对Key进行处理。

**2.对单个RDD或两个RDD聚集**

（1）单个RDD聚集

（2）对两个RDD进行聚集

**3.连接**

（1）join

（2）leftOutJoin和rightOutJoin

**Actions算子**

**1.无输出**

（1）foreach

对RDD中的每个元素都应用f函数操作，不返回RDD和Array，而是返回Uint。

**2.HDFS**

（1）saveAsTextFile

函数将数据输出，存储到HDFS的指定目录。

（2）saveAsObjectFile

saveAsObjectFile将分区中的每10个元素组成一个Array，然后将这个Array序列化，映射为(Null,BytesWritable(Y))的元素，写入HDFS为SequenceFile的格式。

**3.Scala集合和数据类型**

（1）collect

collect相当于toArray，toArray已经过时不推荐使用，collect将分布式的RDD返回为一个单机的scala Array数组。在这个数组上运用scala的函数式操作。

（2）collectAsMap

collectAsMap对(K,V)型的RDD数据返回一个单机HashMap。对于重复K的RDD元素，后面的元素覆盖前面的元素。

（3）reduceByKeyLocally

实现的是先reduce再collectAsMap的功能，先对RDD的整体进行reduce操作，然后再收集所有结果返回为一个HashMap。

（4）lookup

Lookup函数对(Key,Value)型的RDD操作，返回指定Key对应的元素形成的Seq。

（5）count

count返回整个RDD的元素个数。

（6）top

top可返回最大的k个元素。

（7）reduce

reduce函数相当于对RDD中的元素进行reduceLeft函数的操作。

（8）fold

fold和reduce的原理相同，但是与reduce不同，相当于每个reduce时，迭代器取的第一个元素是zeroValue。

（9）aggregate

aggregate先对每个分区的所有元素进行aggregate操作，再对分区的结果进行fold操作。

## Spark工作机制

### Spark应用执行机制

#### Spark执行机制总览

Spark应用提交后经历了一系列的转换，最后成为Task在每个节点上执行。Spark应用转换（见图4-1）：RDD的Action算子触发Job的提交，提交到Spark中的Job生成RDD DAG，由DAGScheduler转化为Stage DAG，每个Stage中产生相应的Task集合，TaskScheduler将任务分发到Executor执行。每个任务对应相应的一个数据块，使用用户定义的函数处理数据块。

![image-20200513133555063](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513133555063.png)

Spark执行的底层实现原理，如图4-2所示。在Spark的底层实现中，通过RDD进行数据的管理，RDD中有一组分布在不同节点的数据块，当Spark的应用在对这个RDD进行操作时，调度器将包含操作的任务分发到指定的机器上执行，在计算节点通过多线程的方式执行任务。一个操作执行完毕，RDD便转换为另一个RDD，这样，用户的操作依次执行。Spark为了系统的内存不至于快速用完，使用延迟执行的方式执行，即只有操作累计到Action（行动），算子才会触发整个操作序列的执行，中间结果不会单独再重新分配内存，而是在同一个数据块上进行流水线操作。

在集群的程序实现上，有一个重要的分布式数据结构，即弹性分布式数据集（ResilientDistributed Dataset，RDD）。Spark实现了分布式计算和任务处理，并实现了任务的分发、跟踪、执行等工作，最终聚合结果，完成Spark应用的计算。

对RDD的块管理通过BlockManger完成，BlockManager将数据抽象为数据块，在内存或者磁盘进行存储，如果数据不在本节点，则还可以通过远端节点复制到本机进行计算。

![image-20200513133636824](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513133636824.png)

####  Spark应用的概念

Spark应用（Application）是用户提交的应用程序。执行模式有Local、Standalone、YARN、Mesos。根据Spark Application的Driver Program是否在集群中运行，Spark 应用的运行方式又可以分为Cluster模式和Client模式。图4-3为Application包含的组件。

![image-20200513133716780](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513133716780.png)

□Application：用户自定义的Spark程序，用户提交后，Spark为App分配资源，将程序转换并执行。

□Driver Program：运行Application的main()函数并创建SparkContext。

□RDD Graph：RDD是Spark的核心结构，可以通过一系列算子进行操作（主要有Transformation和Action操作）。当RDD遇到Action算子时，将之前的所有算子形成一个有向无环图（DAG），也就是图中的RDD Graph。再在Spark中转化为Job，提交到集群执行。一个App中可以包含多个Job。

□Job：一个RDD Graph触发的作业，往往由Spark Action算子触发，在SparkContext中通过runJob方法向Spark提交Job。

□Stage：每个Job会根据RDD的宽依赖关系被切分很多Stage，每个Stage中包含一组相同的Task，这一组Task也叫TaskSet。

□Task：一个分区对应一个Task，Task执行RDD中对应Stage中包含的算子。Task被封装好后放入Executor的线程池中执行。

#### 应用提交与执行方式

应用的提交包含以下两种方式。

□Driver进程运行在客户端，对应用进行管理监控。

□主节点指定某个Worker节点启动Driver，负责整个应用的监控。

**1.Driver在客户端运行**

![image-20200513134230181](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513134230181.png)

作业执行流程描述如下。

用户启动客户端，之后客户端运行用户程序，启动Driver进程。在Driver中启动或实例化DAGScheduler等组件。客户端的Driver向Master注册。Worker向Master注册，Master命令Worker启动Exeuctor。

Worker通过创建Executor-Runner线程，在ExecutorRunner线程内部启动ExecutorBackend进程。ExecutorBackend启动后，向客户端Driver进程内的SchedulerBackend注册，这样Driver进程就能找到计算资源。

Driver的DAGScheduler解析应用中的RDD DAG并生成相应的Stage，每个Stage包含的TaskSet通过TaskScheduler分配给Executor。在Executor内部启动线程池并行化执行Task。

**2.Driver在Worker运行**

应用执行流程如下。

1）用户启动客户端，客户端提交应用程序给Master。

2）Master调度应用，针对每个应用分发给指定的一个Worker启动Driver，即Scheduler-Backend。Worker接收到Master命令后创建DriverRunner线程，在DriverRunner线程内创建SchedulerBackend进程。Driver充当整个作业的主控进程。Master会指定其他Worker启动Exeuctor，即ExecutorBackend进程，提供计算资源。流程和上面很相似，Worker创建ExecutorRunner线程，ExecutorRunner会启动ExecutorBackend进程。

![image-20200513134312166](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513134312166.png)

3）ExecutorBackend启动后，向Driver的SchedulerBackend注册，这样Driver获取了计算资源就可以调度和将任务分发到计算节点执行。SchedulerBackend进程中包含DAGScheduler，它会根据RDD的DAG切分Stage，生成TaskSet，并调度和分发Task到Executor。对于每个Stage的TaskSet，都会被存放到TaskScheduler中。TaskScheduler将任务分发到Executor，执行多线程并行任务。

### Spark调度与任务分配模块

系统设计很重要的一环便是资源调度。设计者将资源进行不同粒度的抽象建模，然后将资源统一放入调度器，通过一定的算法进行调度，最终达到高吞吐量或者低访问延迟的目的。Spark的调度器设计精良，扩展性极好，为它的后续发展奠定了很好的基础。

#### Spark应用程序之间的调度

每个应用拥有对应的SparkContext.SparkContext维持整个应用的上下文信息，提供一些核心方法，如runJob可以提交Job。然后，通过主节点的分配获得独立的一组Executor JVM进程执行任务。Executor空间内的不同应用之间是不共享的，一个Executor在一个时间段内只能分配给一个应用使用。如果多用户需要共享集群资源，依据集群管理者的配置，用户可以通过不同的配置选项来分配管理资源。

对集群管理者来说简单的配置方式就是静态配置资源分配规则。例如，在不同的运行模式下，用户可以通过配置文件中进行集群调度的配置。配置每个应用可以使用的最大资源总量、调度的优先级等。

**1.调度配置**

（1）Standalone

默认情况下，用户向以Standalone模式运行的Spark集群提交的应用使用FIFO（先进先出）的顺序进行调度。每个应用会独占所有可用节点的资源。用户可以通过配置参数spark.cores.max决定一个应用可以在整个集群申请的CPU core数。注意，这个参数不是控制单节点可用多少核。如果用户没有配置这个参数，则在Standalone模式下，默认每个应用可以分配由参数spark.deploy.defaultCores决定的可用核数。

（2）Mesos

如果用户在Mesos上使用Spark，并且想要静态地配置资源的分配策略，则可以通过配置参数spark.mesos.coarse为true，将Mesos配置为粗粒度调度模式。然后配置参数spark.cores.max来限制应用可以使用的CPU core的最大限额。同时用户应该对参数spark.executor.memory进行配置，进而限制每个Executor的内存使用量。Mesos中还可以配置动态共享CPU core的执行模式，用户只需要使用mesos://URL而不配置spark.mesos.coarse参数为true，就能以这种方式执行，使Mesos运行在细粒度调度模型下。在这种模式下，每个Spark应用程序还是会拥有独立和固定的内存分配，但是当应用占用的一些机器上不再运行任务，机器处于空闲状态时，其他机器可以使用这些机器上空闲的 CPU core来执行任务，相当于复用空闲的CPU提升了资源利用率。这种模式在集群上再运行大量不活跃的应用情景下十分有用，如大量不同用户发起请求的场景。

（3）YARN

当Spark运行在YARN平台上时，用户可以在YARN的客户端通过配置--numexecutors选项控制为这个应用分配多少个Executor，然后通过配置--executor-memory及--executor-cores来控制应用被分到的每个Executor的内存大小和Executor所占用的CPU核数。这样便可以限制用户提交的应用不会过多的占用资源，让不同用户能够共享整个集群资源，提升YARN吞吐量。

**2.FIFO的调度代码**

从源码中可以看到，Master先统计可用资源，然后在waitingDrivers的队列中通过FIFO方式为App分配资源和指定Worker启动Driver执行应用。

#### Spark应用程序内Job的调度

在Spark应用程序内部，用户通过不同线程提交的Job可以并行运行，这里所说的Job就是SparkAction（如count、collect等）算子触发的整个RDD DAG为一个Job，在实现上，算子中的本质是调用SparkContext中的runJob提交了Job。

Spark的调度器是完全线程安全的，并且支持一个应用处理多请求的用例（如多用户进行查询）。

（1）FIFO模式

在默认情况下，Spark的调度器以FIFO（先进先出）方式调度Job的执行，如图4-6所示。每个Job被切分为多个Stage。第一个Job优先获取所有可用的资源，接下来第二个Job再获取剩余资源。以此类推，如果第一个Job并没有占用所有的资源，则第二个Job还可以继续获取剩余资源，这样多个Job可以并行运行。如果第一个Job很大，占用所有资源，则第二个Job就需要等待第一个任务执行完，释放空余资源，再申请和分配Job。

![image-20200513134830424](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513134830424.png)

（2）FAIR模式

从Spark0.8版本开始，可以通过配置FAIR共享调度模式调度Job，如图4-7所示。在FAIR共享模式调度下，Spark在多Job之间以轮询（round robin）方式为任务分配资源，所有的任务拥有大致相当的优先级来共享集群的资源。这就意味着当一个长任务正在执行时，短任务仍可以分配到资源，提交并执行，并且获得不错的响应时间。这样就不用像以前一样需要等待长任务执行完才可以。这种调度模式很适合多用户的场景。用户可以通过配置spark.scheduler.mode方式来让应用以FAIR模式调度。FAIR调度器同样支持将Job分组加入调度池中调度，用户可以同时针对不同优先级对每个调度池配置不同的调度权重。这种方式允许更重要的Job配置在高优先级池中优先调度。这种方式借鉴了Hadoop的FAIR调度模型，如图4-7所示。

![image-20200513134855341](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513134855341.png)

（3）配置调度池

用户可以通过配置文件自定义调度池的属性。每个调度池支持下面3个配置参数。

1）调度模式（schedulingMode）：用户可以选择FIFO或者FAIR方式进行调度。

2）权重（Weight）：这个参数控制在整个集群资源的分配上，这个调度池相对其他调度池优先级的高低。例如，如果用户配置一个指定的调度池权重为3，那么这个调度池将会获得相对于权重为1的调度池3倍的资源。

3）minShare：配置minShare参数（这个参数代表多少个CPU核），这个参数决定整体调度的调度池能给待调度的调度池分配多少资源就可以满足调度池的资源需求，剩余的资源还可以继续分配给其他调度池。

#### Stage和TaskSetManager调度方式

1.Stage的生成

Stage的调度是由DAGScheduler完成的。由RDD的有向无环图DAG切分出了Stage的有向无环图DAG。Stage的DAG通过最后执行的Stage为根进行广度优先遍历，遍历到最开始执行的Stage执行，如果提交的Stage仍有未完成的父母Stage，则Stage需要等待其父Stage执行完才能执行。同时DAGScheduler中还维持了几个重要的Key-Value集合结构，用来记录Stage的状态，这样能够避免过早执行和重复提交Stage。waitingStages中记录仍有未执行的父母Stage，防止过早执行。runningStages中保存正在执行的Stage，防止重复执行。failedStages中保存执行失败的Stage，需要重新执行，这里的设计是出于容错的考虑。

2.TaskSetManager的调度

结合上面介绍的Job的调度和Stage的调度方式，可以知道，每个Stage对应的一个TaskSetManager通过Stage回溯到最源头缺失的Stage提交到调度池pool中，在调度池中，这些TaskSetMananger又会根据Job ID排序，先提交的Job的TaskSetManager优先调度，然后一个Job内的TaskSetManager ID小的先调度，并且如果有未执行完的父母Stage的TaskSetManager，则是不会提交到调度池中。

#### Task调度

1.提交任务

在DAGScheduler中提交任务时，分配任务执行节点。

2.分配任务执行节点

1）如果是调用过cache()方法的RDD，数据已经缓存在内存，则读取内存缓存中分区的数据。

2）如果直接能获取到执行地点，则返回执行地点作为任务的执行地点，通常DAG中最源头的RDD或者每个Stage中最开始的RDD会有执行地点的信息。例如，HadoopRDD从HDFS读出的分区就是最好的执行地点。这里涉及Hadoop分区的数据本地性问题，感兴趣的读者可以查阅Hadoop的资料了解。

3）如果不是上面两种情况，将遍历RDD获取第一个窄依赖的父亲RDD对应分区的执行地点。

任务的locality由以下两种方式确定。

1）RDD DAG源头有HDFS等类型的分布式存储，它们内置的数据本地性决定（RDD中配置preferred location确定）数据存储位置和分区的选取。

2）每个其他非源头Stage由于都要进行Shuffle，所以地址以在resourceoffer中进行roundrobin来确定，初始提交Stage时，将prefer的位置设置为Nil。但在Stage调度过程中，内部是通过Narrow dep的祖先Stage确定最佳执行位置的。这样相当于每个RDD的分区都有prefer执行位置。

### Spark I/O机制

Spark的I/O由传统的I/O演化而来，但又有所不同。

□单机计算机系统中，数据集中化，结构化数据、半结构化数据、非结构化数据都只存储在一个主机中，而Spark中的数据分区是分散在多个计算机系统中的。

□传统计算机数据量小。Spark需要处理TB、PB级别的数据。

#### 序列化

序列化是将对象转换为字节流，本质上可以理解为将链表存储的非连续空间的数据存储转化为连续空间存储的数组中。这样就可以将数据进行流式传输或者块存储。相反，反序列化就是将字节流转化为对象。

序列化主要有以下两个目的。□进程间通信：不同节点之间进行数据传输。□数据持久化存储到磁盘：本地节点将对象写入磁盘。

Spark通过集中方式实现进程通信，包括Actor的消息模式、Java NIO和netty 的OIO。

在Spark中，序列化拥有重要地位。无论是内存或者磁盘中的RDD含有的对象存储，还是节点间的传输数据，都需要执行序列化的过程。序列化与反序列化的速度、序列化后的数据大小等都影响数据传输的速度，以致影响集群的计算效率。Spark可以使用Java的序列化库，也可以使用Kyro序列化库。Kyro具有紧凑、快速、轻量的优点，允许自定义序列化方法，且扩展性很好。下面用户可以通过表4-1参数进行序列化配置。

![image-20200513135804917](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513135804917.png)

#### 压缩

当大片连续区域进行数据存储并且存储区域中数据重复性高的状况下，数据适合进行压缩。数组或者对象序列化后的数据块可以考虑压缩。所以序列化后的数据可以压缩，使数据紧缩，减少空间开销。

**1.Spark对压缩方式的选择**

压缩采用了两种算法：Snappy和LZF，底层分别采用了两个第三方库实现，同时可以自定义其他压缩库对Spark进行扩展。Snappy提供了更高的压缩速度，LZF提供了更高的压缩比，用户可以根据具体需求选择压缩方式。

压缩格式及解编码器如下。

□LZF：org.apache.spark.io.LZFCompressionCodec。

□Snappy：org.apache.spark.io.SnappyCompressionCodec。

（1）Ning-Compress

Ning-compress是一个对数据进行LZF格式压缩和解压缩的库，这个库是TatuSaloranta(tatu.saloranta@iki.fi)书写的。用户可以在Github地址：https://github.com/ning/compress下载，进行学习和研究。

（2）snappy-java

Snappy算法的前身是Zippy，被Google用于MapReduce、BigTable等许多内部项目。snappy-java由谷歌开发，是以C++开发的Snappy压缩解压缩库的Java分支。Github地址为：https://github.com/xerial/snappy-java。

Snappy的目标是在合理的压缩量情况下，提供高压缩速度的库。因此Snappy的压缩比和LZF差不多，并不是很高。根据数据集的不同，压缩比能达到20%～100%。有兴趣的读者可以看一个压缩算法Benchmark，它对基于JVM运行语言的压缩库进行对比。这个Benchmark对snappy-java和其他压缩工具LZO-java/LZF/QuickLZ/Gzip/Bzip2进行了比较。地址为Github:https://github.com/ning/jvm-compressor-benchmark/wiki。这个Benchmark是由Tatu Saloranta @cotowncoder开发的。

**2.在Spark程序中使用压缩**

（1）在Spark-env.sh文件中配置

（2）在应用程序中配置

#### Spark块管理

RDD在逻辑上是按照Partition分块的，可以将RDD看成是一个分区作为数据项的分布式数组。这也是Spark在极力做到的一点，让编写分布式程序像编写单机程序一样简单。而物理上存储RDD是以Block为单位的，一个Partition对应一个Block，用Partition的ID通过元数据的映射到物理上的Block，而这个物理上的Block可以存储在内存，也可以存储在某个节点的Spark的硬盘临时目录，等等。

整体的I/O管理分为以下两个层次。

1）通信层：I/O模块也是采用Master-Slave结构来实现通信层的架构，Master和Slave之间传输控制信息、状态信息。

2）存储层：Spark的块数据需要存储到内存或者磁盘，有可能还需传输到远端机器，这些是由存储层完成的。

**1.实体和类**

（1）管理和接口

BlockManager：当其他模块要和storage模块进行交互时，storage模块提供了统一的操作类BlockManager，外部类与storage模块打交道都需要调用BlockManager相应接口来实现。

![image-20200513140355511](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513140355511.png)

（2）通信层

□BlockManagerMasterActor：从主节点创建，从节点通过这个Actor的引用向主节点传递消息和状态。

□BlockManagerSlaveActor：在从节点创建，主节点通过这个Actor的引用向从节点传递命令，控制从节点的块读写。

 □BlockManagerMaster：对Actor通信进行管理。

（3）数据读写层

□DiskStore：提供Block在磁盘上以文件形式读写的功能。

□MemoryStore：提供Block在内存中的Block读写功能。

□ConnectionManager：提供本地机器和远端节点进行网络传输Block的功能。

□BlockManagerWorker：对远端数据的异步传输进行管理。

**2.BlockManager中的通信**

主节点和从节点之间通过Actor传送消息来传递命令和状态。

![image-20200513140457170](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513140457170.png)

整体的数据存储通信仍相当于Master-Slave模型，节点之间传递消息和状态，Master节点负责总体控制，Slave节点接收命令、汇报状态。（补充介绍：Actor和ref是AKKA中两个不同的Actor引用。）

（1）Master端

BlockManagerMaster对象拥有BlockManagerMasterActor的actor引用以及所有BlockManagerSlaveActor的ref引用。

（2）Slave端

对于Slave，BlockManagerMaster对象拥有BlockManagerMasterActor对象的ref的引用和自身BlockManagerSlaveActor的actor的引用。BlockManagerMasterActor在ref和Actor之间通信，BlockManagerSlaveActor在ref和Actor之间通信。

**3.读写流程**

（1）数据写入

![image-20200513140541234](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513140541234.png)

数据写入的简要流程，读取流程和写入流程类似。数据写入流程主要分为以下几个步骤。

1）RDD调用compute()方法进行指定分区的写入。

2）CacheManager中调用BlockManater判断数据是否已经写入，如果未写则写入。

3）BlockManager中数据与其他节点同步。

4）BlockManager根据存储级别写入指定的存储层。

5）BlockManager向主节点汇报存储状态。

（2）数据读取

在RDD类中，通过compute方法调用iterator读写某个分区（Partition），作为数据读取的入口。分区是逻辑概念，在物理上是一个数据块（block）。

（3）读取逻辑

1）本地读取。

在本地同步读取数据块，首先看能否在内存读取数据块，如果不能读取，则看能否从Tachyon读取数据块，如果仍不能读取，则看能否从磁盘读取数据块。

2）远程读取。

远程获取调用路径，然后getRemote调用doGetRemote,通过BlockManagerWorker.syncGetBlock从远程获取数据。

**4.数据块读写管理**

数据块的读写，如果在本地内存存在所需数据块，则先从本地内存读取，如果不存在，则看本地的磁盘是否有数据，如果仍不存在，再看网络中其他节点上是否有数据，即数据有3个类别的读写来源。

（1）MemoryStore内存块读写

通过源码可以看到进行块读写是线程间同步的。通过entries.synchronized控制多线程并发读写，防止出现异常。

（2）DiskStore磁盘块写入

在DiskStore中，一个Block对应一个文件。在diskManager中，存储blockId和一个文件路径映射。数据块的读写入相当于读写文件流。

### Spark通信模块

Spark的Cluster Manager可以有Local、Standalone、Mesos、YARN等部署模式。为了研究Spark的通信机制，本节介绍Standalone模式，其他模式可以对照此模式进行类比，感兴趣的读者可以进一步通过源码了解。

下面介绍分布式通信的几种方式。

（1）RPC（Remote Produce Call）

RPC是远程过程调用协议，基于C/S模型调用。过程大致可以理解为本地分布式对象向本机发请求，不用自己编写底层通信本机。通过网络向服务器发送请求，服务器对象接收参数后，进行处理，再把处理后的结果发送回客户端。

（2）RMI（Remote Method Invocation）

RMI和RPC一样都是调用远程的方法，可以把RMI看做是用Java语言实现了RPC协议。RPC不支持对象通信，支持对象传输，这也是RMI相比于RPC的优越之处。

（3）JMS（Java Remote Service）

JMS是在各个Java类之间传递消息的标准。其支持P2P和pub/stub两种消息模型，即点对点和发布订阅两种模型。其优点在于：支持异步通信、消息生产者和消费者耦合度低。应用程序通过读写出入队列的消息（针对应用程序的数据）来通信，而无须专用连接来连接它们。

（4）EJB（Enterprise Java Bean）

EJB是Java EE 中的一个规范，该规范描述了分布式应用程序需要解决的事务处理，安全、日志、分布式等问题，与此同时，Sun公司也实现了自己定义的这一个标准，相当于自己颁布一个标准。EJB是一个特殊的Java类，按照Java服务器接口定义，放在容器里可以帮助该类管理事务、分布式、安全等，只有大型分布式系统才会用到EJB，一般小型程序不会用到。

（5）Web Service

Web Service是网络间跨语言、跨平台的分布式系统间的通信标准。传输XML、JSON等格式的数据，应用范围较广。

#### 通信框架AKKA

Spark在模块间通信使用的是AKKA框架。AKKA基于Scala开发，用于编写Actor应用。Actor模型在并发编程中是比较常见的一种模型。很多开发语言都提供了原生的Actor模型（Erlang、Scala）。Actors 是一些包含状态和行为的对象。它们通过显式传递消息来进行通信，这些消息会被发送到它们的收件箱中（消息队列）。从某种意义上来说，Actor是面向对象编程中最严格的实现形式。它们之间可以通过消息来通信。一个Actor收到其他Actor的信息后，可以根据需要做出各种响应。通过Scala的强大模式匹配功能可以让用户自定义多样化的消息。Actor建立一个消息队列，每次收到消息后，放入队列，而它每次也从队列中取出消息体来处理。通常情况下，这个过程是循环的。让Actor可以时刻接收处理发送来的消息。

AKKA Actor树形结构Actors以树形结构组织起来。一个Actor可能会把自己的任务划分成更多更小的、利于管理的子任务。为了达到这个目的，它会开启自己的子Actor，并负责监督这些子Actor。关于监督的具体细节就不在这里讨论了。我们只需知道一点，就是每一个Actor都会有一个监督者，即创建这些Actor的Actor。

AKKA的优势和特性如下。

1）并行和分布式：AKKA在设计时采用了异步通信和分布式架构。

2）可靠性：在本地/远程都有监控和恢复机制。

3）高性能：在单机环境中每秒可发送50000000个消息。1GB内存中可创建和保持2500000个Actor对象。

4）去中心：区别于Master-Slave 模式，采取无中心节点的架构。

5）可扩展性：可以在分布式环境下进行Scale out，线性扩充计算能力。

![image-20200513141507494](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513141507494.png)

#### Client、Master和Worker间的通信

在Standalone模式下，存在以下角色。

□Client：提交作业。

□Master：接收作业，启动Driver和Executor，管理Worker。

□Worker：管理节点资源，启动Driver和Executor。

1.模块间的主要消息

![image-20200513141543943](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513141543943.png)

（1）Client to Master

RegisterApplication：注册应用。

（2）Master to Client

□RegisteredApplication：注册应用后，回复给Client。

□ExecutorAdded：通知Client Worker已经启动了Executor，当向Worker发送Launch-Executor时，通知Client Actor。

□ExecutorUpdated：通知Client Executor状态已更新。

（3）Master to Worker

□LaunchExecutor：启动Executor。

□RegisteredWorker：Worker注册的回复。

□RegisterWorkerFailed：注册Worker失败的回复。

□KillExecutor：停止Executor进程。

（4）Worker to Master

□RegisterWorker：注册Worker。

□Heartbeat：周期性地Master发送心跳信息。

□ExecutorStateChanged：通知Master，Executor状态更新。

### 容错机制

在众多特性中，最难实现的是容错性。一般来说，分布式数据集的容错性有两种方式：数据检查点和记录数据的更新。面向大规模数据分析，数据检查点操作成本很高，需要通过数据中心的网络连接在机器之间复制庞大的数据集，而网络带宽往往比内存带宽低得多，同时还需要消耗更多的存储资源。因此，Spark选择记录更新的方式。但是，如果更新粒度太细太多，那么记录更新成本也不低。因此，RDD只支持粗粒度转换，即在大量记录上执行的单个操作。将创建RDD的一系列Lineage（即血统）记录下来，以便恢复丢失的分区。Lineage本质上很类似于数据库中的重做日志（Redo Log），只不过这个重做日志粒度很大，是对全局数据做同样的重做进而恢复数据。

#### Lineage机制

图4-16给出了3个算子的血统（lineage）关系图。在lines RDD上执行filter操作，得到errors，然后filter、map后得到新的RDD（filter、map和collect都是Spark中对RDD的函数操作）。Spark调度器以流水线的方式执行后三个转换，向拥有errors分区缓存的节点发送一组任务。此外，如果某个errors分区丢失，则Spark只在相应的lines分区上执行filter操作来重建该errors分区。

![image-20200513141736182](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513141736182.png)

1.Lineage简介

相比其他系统的细颗粒度的内存数据更新级别的备份或者LOG机制，RDD的Lineage记录的是粗颗粒度的特定数据Transformation操作（如filter、map、join等）行为。当这个RDD的部分分区数据丢失时，它可以通过Lineage获取足够的信息来重新运算和恢复丢失的数据分区。因为这种粗颗粒的数据模型，限制了Spark的运用场合，所以Spark并不适用于所有高性能要求的场景，但同时相比细颗粒度的数据模型，也带来了性能的提升。

2.两种依赖

RDD在Lineage依赖方面分为两种：Narrow Dependencies与Shuffle Dependencies，用来解决数据容错的高效性。Narrow Dependencies是指父RDD的每一个分区最多被一个子RDD的分区所用，表现为一个父RDD的分区对应于一个子RDD的分区或多个父RDD的分区对应于一个子RDD的分区，也就是说一个父RDD的一个分区不可能对应一个子RDD的多个分区。ShuffleDependencies是指子RDD的分区依赖于父RDD的多个分区或所有分区，即存在一个父RDD的一个分区对应一个子RDD的多个分区。

3.容错原理

在容错机制中，如果一个节点死机了，而且运算Narrow Dependency，则只要把丢失的父RDD分区重算即可，不依赖于其他节点。而Shuffle Dependency需要父RDD的所有分区都存在，重算就很昂贵了。可以这样理解开销的经济与否：在Narrow Dependency中，在子RDD的分区丢失、重算父RDD分区时，父RDD相应分区的所有数据都是子RDD分区的数据，并不存在冗余计算。在Shuffle Dependency情况下，丢失一个子RDD分区重算的每个父RDD的每个分区的所有数据并不是都给丢失的子RDD分区用的，会有一部分数据相当于对应的是未丢失的子RDD分区中需要的数据，这样就会产生冗余计算开销，这也是Shuffle Dependency开销更大的原因。因此如果使用Checkpoint算子来做检查点，不仅要考虑Lineage是否足够长，也要考虑是否有宽依赖，对Shuffle Dependency加Checkpoint是最物有所值的。下面结合图4-17进行分析。

![image-20200513141826915](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513141826915.png)

#### Checkpoint机制

通过上述分析可以看出在以下两种情况下，RDD需要加检查点。

1）DAG中的Lineage过长，如果重算，则开销太大（如在PageRank中）。

2）在Shuffle Dependency上做Checkpoint（检查点）获得的收益更大。

### Shuffle机制

Shuffle的本义是洗牌、混洗，即把一组有一定规则的数据打散重新组合转换成一组无规则随机数据分区。Spark中的Shuffle更像是洗牌的逆过程，把一组无规则的数据尽量转换成一组具有一定规则的数据，Spark中的Shuffle和MapReduce中的Shuffle思想相同，在实现细节和优化方式上不同，因此掌握Hadoop的Shuffle原理的用户很容易将原有知识迁移过来。

为什么Spark计算模型需要Shuffle过程？我们都知道，Spark计算模型是在分布式的环境下计算的，这就不可能在单进程空间中容纳所有的计算数据来进行计算，这样数据就按照Key进行分区，分配成一块一块的小分区，打散分布在集群的各个进程的内存空间中，并不是所有计算算子都满足于按照一种方式分区进行计算。例如，当需要对数据进行排序存储时，就有了重新按照一定的规则对数据重新分区的必要，Shuffle就是包裹在各种需要重分区的算子之下的一个对数据进行重新组合的过程。在逻辑上还可以这样理解[插图]：由于重新分区需要知道分区规则，而分区规则按照数据的Key通过映射函数（Hash或者Range等）进行划分，由数据确定出Key的过程就是Map过程，同时Map过程也可以做数据处理，例如，在Join算法中有一个很经典的算法叫Map Side Join，就是确定数据该放到哪个分区的逻辑定义阶段。Shuffle将数据进行收集分配到指定Reduce分区，Reduce阶段根据函数对相应的分区做Reduce所需的函数处理。

![image-20200513141930263](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513141930263.png)

图4-20中，整个Job分为Stage0～Stage3，4个Stage。

首先从最上端的Stage2、Stage3执行，每个Stage对每个分区执行变换（transformation）的流水线式的函数操作，执行到每个Stage最后阶段进行Shuffle Write，将数据重新根据下一个Stage分区数分成相应的Bucket，并将Bucket最后写入磁盘。这个过程就是Shuffle Write阶段。

执行完Stage2、Stage3之后，Stage1去存储有Shuffle数据节点的磁盘Fetch需要的数据，将数据Fetch到本地后进行用户定义的聚集函数操作。这个阶段叫Shuffle Fetch，Shuffle Fetch包含聚集阶段。这样一轮一轮的Stage之间就完成了Shuffle操作。

## Spark开发环境配置及流程

### Spark应用开发环境配置

1.配置开发环境

（1）安装JDK

（2）安装Scala

（3）安装Intellij IDEA

（4）在Intellij中安装Scala插件

2.配置Spark应用开发环境

1）在Intellij IDEA中创建Scala Project，名称为SparkTest。

2）选择菜单中的“File”→“project structure”→“Libraries”，然后选择“+”，导入spark-assembly_2.10-1.0.0-incubating-hadoop2.2.0.jar。

3）如果IDE无法识别Scala库，则需要以同样方式将Scala库的jar包导入，之后可以开始开发Scala程序，如图5-2所示。本例将Spark默认的示例程序SparkPi复制进文件。

3.运行Spark程序

（1）本地运行

编写完Scala程序后，可以直接在Intellij中以本地（local）模式运行（见图5-3），方法如下。

注意，设置Program arguments中参数为local。

![image-20200513143046033](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513143046033.png)

在Intellij中点击Run/Debug Configuration按钮，在其下拉列表选择Edit Configurations选项。在Run输入选择界面中，如图5-3所示，在输入框Program arguments中输入main函数的输入参数local，即为本地单机执行Spark应用。然后右键选择需要运行的类，点击Run运行Spark应用程序。

（2）在集群上运行Spark应用Jar包

如果想把程序打成Jar包，通过命令行的形式在Spark集群中运行，可以按照以下步骤操作。

1）选择“File”→“Project Structure”命令，然后选择“Artifact”，单击“+”按钮，选择“Jar”→“From Modules with dependencies”，如图5-4所示。选择Main函数，在弹出的对话框中选择输出Jar位置，并单击“OK”按钮。

在图5-4中点击From mudules with dependencies后将会出现如图5-5所示的输入框，在其中的输入框中选择需要执行的Main函数。

在图5-5所示的界面中单击OK按钮后，在图5-6所示的对话框中通过OutPut layout中的“+”选择依赖的Jar包。

![image-20200513143123746](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513143123746.png)

![image-20200513143133884](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513143133884.png)

![image-20200513143145863](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513143145863.png)

2）在主菜单中选择“Build”→“Build Artifact”命令，编译生成Jar包。

3）在集群的主节点，通过下面命令执行生成的Jar包SparkTest.jar。

### 远程调试Spark程序

1.编译Spark

2.在服务器端的配置

1）根据相应的Spark配置指定版本的Hadoop并启动Hadoop。

2）对编译好的Spark进行配置，在conf/spark-env.sh文件中进行如下配置。下面代码配置了Spark调试所需的Java参数。

3.启动Spark集群和应用程序

1）启动Spark集群。

2）启动需要调试的程序，以Spark中自带的HdfsWordCount为例。

4.配置本地IDE

### Spark编译

1.克隆Spark源码

2.编译Spark

3.增量编译

4.查看Spark源码依赖图

### 配置Spark源码阅读环境

1）克隆Spark源码。2）生成项目。3）在所需的软件安装好后，在Spark源代码根目录下输入以下代码。

## Spark编程实战

### WordCount

WordCount是大数据领域的经典范例，如同程序设计中的Hello World一样，是一个入门程序。本节主要从并行处理的角度出发，介绍设计Spark程序的过程。

1.实例描述

![image-20200513144611174](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144611174.png)

2.设计思路

![image-20200513144642695](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144642695.png)

3.代码示例

WordCount的主要功能是统计输入中所有单词出现的总次数，编写步骤如下。

（1）初始化

创建一个SparkContext对象，该对象有4个参数：Spark master位置、应用程序名称、Spark安装目录和Jar存放位置。

![image-20200513144712905](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144712905.png)

![image-20200513144723398](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144723398.png)

### Top K

Top K算法有两步，一是统计词频，二是找出词频最高的前K个词。

![image-20200513144749109](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144749109.png)

### 中位数

海量数据中通常有统计集合中位数的计算需求，读者可以通过以下示例了解Spark求中位数的方式。

![image-20200513144818302](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144818302.png)

### 倒排索引

倒排索引（inverted index）源于实际应用中需要根据属性的值来查找记录。在索引表中，每一项均包含一个属性值和一个具有该属性值的各记录的地址。由于记录的位置由属性值确定，而不是由记录确定，因而称为倒排索引。将带有倒排索引的文件称为倒排索引文件，简称倒排文件（inverted file）。其基本结构如图6-1所示。

![image-20200513144842996](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144842996.png)

搜索引擎的关键步骤是建立倒排索引。相当于为互联网上几千亿页网页做了一个索引，与书籍目录相似，用户想看与哪一个主题相关的章节，直接根据目录即可找到相关的页面。

![image-20200513144900865](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144900865.png)

### CountOnce

假设HDFS只存储一个标号为ID的Block，每份数据保存2个备份，这样就有2个机器存储了相同的数据。其中ID是小于10亿的整数。若有一个数据块丢失，则需要找到哪个是丢失的数据块。在某个时间，如果得到一个数据块ID的列表，能否快速地找到这个表中仅出现一次的ID？即快速找出出现故障的数据块的ID。

![image-20200513144928582](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144928582.png)

### 倾斜连接

并行计算中，我们总希望分配的每一个任务（task）都能以相似的粒度来切分，且完成时间相差不大。但是由于集群中的硬件和应用的类型不同、切分的数据大小不一，总会导致部分任务极大地拖慢了整个任务的完成时间。硬件不同暂且不论，下面举例说明不同应用类型的情况，如Page Rank 或者Data Mining中的一些计算，它的每条记录消耗的成本不太一样，这里只讨论关于关系型运算的Join连接的数据倾斜状况。

数据倾斜原因如下。1）业务数据本身的特性。2）Key分布不均匀。3）建表时考虑不周。4）某些SQL语句本身就有数据倾斜。数据倾斜表现如下。

任务进度长时间维持，查看任务监控页面，由于其处理的数据量与其他任务差异过大，会发现只有少量（1个或几个）任务未完成。

![image-20200513144957580](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513144957580.png)

### 股票趋势预测

本例将介绍如何使用Spark构建实时数据分析应用[插图]，以分析股票价格趋势。

本例假设已预先连接了Spark Streaming。读者可以阅读介绍BDAS的章节预先了解相关概念。第一步，需要获取数据流，本例使用JSON/WebSocket格式呈现6种实时市场金融信息。第二步，需要知道如何使用获取到的数据流，本例不涉及专业的金融知识，但可以在这个应用中通过价格改变规律，预测价格趋势。

1.实例描述

本例通过使用scalawebsocket库（Github网址为https://github.com/pbuda/scalawebsocket）访问WebSocket。scalawebsocket库只支持Scala 2.10。获取网上的金融数据流。

![image-20200513145039379](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513145039379.png)

2.设计思路

通过Spark Streaming的时间窗口，增加新数据，减少旧数据。本例中的reduce函数用于对所有价格改变求和（有正向的改变和负向的改变）。之后希望看到正向的价格改变数量是否大于负向的价格改变数量，这里通过改变正向数据将计数器加1，改变负向的数据将计数器减1进行统计，从而统计出股票的趋势。

## BDAS简介

提到Spark就不得不说伯克利大学AMPLab开发的BDAS（Berkeley Data Analytics Stack）数据分析的软件栈。其中用内存分布式大数据计算引擎Spark替代原有的MapReduce，上层通过Spark SQL/Shark替代Hive等数据仓库，Spark Streaming替换Storm等流式计算框架，GraphX替换Graph Lab等大规模图计算框架，MLlib替换Mahout等机器学习框架等，其整体框架基于内存计算解决了原来Hadoop的性能瓶颈问题。他们提出One Framework to RuleThem All的理念，用户可以利用Spark一站式构建自己的数据分析流水线。

### SQL on Spark

AMPLab将大数据分析负载分为三大类型：批量数据处理、交互式查询、实时流处理，而其中很重要的一环便是交互式查询。大数据分析栈中需要满足用户ad-hoc、reporting、iterative等类型的查询需求，需要提供SQL接口来兼容原有数据库用户的使用习惯，同时需要SQL能够重组关系模式。完成这些重要的SQL任务的便是Spark SQL和Shark这两个开源分布式大数据查询引擎，它们可以理解为轻量级Hive SQL在Spark上的实现，业界将该类技术统称为SQL onHadoop。

#### 使用Spark SQL的原因

由于Shark底层依赖于Hive，这个架构的优势是对传统Hive用户可以将Shark无缝集成进现有系统运行查询负载。但是我们也看到一些问题：随着版本的升级，查询优化器依赖于Hive，不方便添加新的优化策略，需要学习另一套系统和进行二次开发，学习成本很高。另一方面，MapReduce是进程级并行。例如，Hive在不同的进程空间会使用一些静态变量，当在同一进程空间并行执行多线程时，多线程同时写同名称的静态变量会产生一致性问题，因此Shark需要使用另外一套独立维护的Hive源码分支。为了解决这个问题，AMPLab和Databricks利用Catalyst开发了Spark SQL。在Spark 1.0版本中已经发布Spark SQL。

#### Spark SQL架构分析

Spark SQL与传统DBMS的查询优化器+执行器的架构较为类似，只不过其执行器是在分布式环境中实现，并采用Spark作为执行引擎。Spark SQL的查询优化是Catalyst，其基于Scala语言开发，可以灵活利用Scala原生的语言特性方便地扩展功能，奠定了Spark SQL的发展空间。Catalyst将SQL翻译成最终的执行计划，并在这个过程中进行查询优化。这里和传统不太一样的地方就在于，SQL经过查询优化器最终转换为可执行的查询计划，传统DB就可以执行这个查询计划了。而Spark SQL最后执行还是会在Spark内将执行计划转换为Spark的有向无环图DAG再执行。

### Spark Streaming

Spark Streaming是构建在Spark上的实时流计算框架，扩展了Spark流式大数据处理能力。Spark Streaming将数据流以时间片为单位分割形成RDD，使用RDD操作处理每一块数据，每块数据（也就是RDD）都会生成一个Spark Job进行处理，最终以批处理的方式处理每个时间片的数据，如图8-7所示。

![image-20200513145429943](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513145429943.png)

#### Spark Streaming架构

![image-20200513145904176](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513145904176.png)

组件介绍如下。

□Network Input Tracker：通过接收器接收流数据，并将流数据映射为输入DStream。

□Job Scheduler：周期性地查询DStream图，通过输入的流数据生成Spark Job，将Spark Job提交给Job Manager执行。

□JobManager：维护一个Job队列，将队列中的Job提交到Spark执行。

### GraphX

Graphx是Spark中的一个重要子项目，它利用Spark为计算引擎，实现了大规模图计算的功能，并提供了类似Pregel的编程接口。GraphX的出现，使Spark生态系统更加完善和丰富，同时其与Spark生态系统其他组件很好的融合，以及强大的图数据处理能力，使其在工业界得到了广泛的应用。本章主要介绍GraphX的架构、原理和使用方式。

###  MLlib

MLlib是构建在Spark上的分布式机器学习库，充分利用了Spark的内存计算和适合迭代型计算的优势，使性能大幅度提升，同时Spark算子丰富的表现力，让大规模机器学习的算法开发不再复杂。在正式介绍ML1ib之前，先介绍一个拓展知识——数据挖掘组件化思想。

1.模型或者模式结构

训练数据相当于数据挖掘算法的输入，而模型（model）或者模式（pattern）结构相当于数据挖掘算法的输出，如决策树模型、频繁序列模式。

2.数据挖掘任务

根据对全局集合的描述状况可以将数据挖掘任务分为模式挖掘、回归分类、描述建模。

（1）模式挖掘

模式是对某个数据集中部分结构的描述，可以是一个子集合、一个子序列、一个子树和一个子图。例如，交易数据中的啤酒、尿布就是频繁项集，是一个子集合。

（2）回归分类

回归分类根据现有数据建立模型，然后应用这个模型对未来数据进行预测。在预测模型中，一个变量表达为其他变量的函数。因此，可以把预测建模的过程看做是学习一种映射或函数Y=f（X）。相当于在整个数据集合看，数据集合分为现有数据和未来待处理数据，回归分类是对现有数据集合的描述，然后估计近似认为整个数据集的描述，待处理数据会根据历史数据得出模型进行处理。

（3）描述建模

描述建模要获取对现有数据集合的全局结构的描述。

3.评分函数

当确定了模型或者模式的结构之后，下一步就是需要确定结构中的参数，将结构拟合到数据。由于模型或者模式的结构是函数的一般形式，参数的空间很大，可选择的参数非常多，所以需要一个评分函数作为评价指标，确定哪个参数组合才能达到最优的结果。常用的评分函数有误差平方和、准确率召回率和ROC等。

4.搜索和优化方法

确定了评分函数，也就有了衡量数据和模型或者模式拟合程度的标准。搜索和优化方法就是为了确定模型模式结构的参数值。如果模型模式结构没有确定，则需要使用搜索方法（如贪婪法、深度优先遍历等），从模型模式的集合中发现最优的模型模式结构，还需要确定最优的结构参数。对于固定的模型模式结构，搜索就在参数空间进行，使用的是优化方法（如爬山法、期望最大化法等）。

5.数据管理策略

通常在大数据环境下，数据不能被单机容纳，需要分布式存储。这就需要设计好数据管理策略优化提升算法性能。针对大数据可以采用、近似、压缩、索引等技术提升机器学习算法效率。

## Spark性能调优

### 配置参数

1.如何进行参数配置性能调优

熟悉Hadoop开发的用户对配置项应该不陌生。根据不同问题，调整不同的配置项参数是比较基本的调优方案。配置项可以在脚本文件中添加，也可以在代码中添加。

![image-20200513150216944](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513150216944.png)

![image-20200513150227020](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200513150227020.png)

2.如何观察性能问题

可以通过下面的方式监测性能。1）Web UI。2）Driver程序控制台日志。3）logs文件夹下日志。4）work文件夹下日志。5）Profiler工具。

### 调优技巧

调度与分区优化

倾斜问题

并行度

DAG调度执行优化

内存存储优化