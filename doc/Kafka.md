

## Kafka

**Kafka**是由[Apache软件基金会](https://baike.baidu.com/item/Apache软件基金会)开发的一个开源流处理平台，由[Scala](https://baike.baidu.com/item/Scala)和[Java](https://baike.baidu.com/item/Java/85979)编写。Kafka是一种高吞吐量的[分布式](https://baike.baidu.com/item/分布式/19276232)发布订阅消息系统，它可以处理消费者在网站中的所有动作流数据。 这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像[Hadoop](https://baike.baidu.com/item/Hadoop)一样的[日志](https://baike.baidu.com/item/日志/2769135)数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。Kafka的目的是通过[Hadoop](https://baike.baidu.com/item/Hadoop)的并行加载机制来统一线上和离线的消息处理，也是为了通过[集群](https://baike.baidu.com/item/集群/5486962)来提供实时的消息。

### 基本结构

![image-20200226194459219](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226194459219.png)

### 主题

Kafka将一组消息抽象归纳为一个主题（Topic），也就是说，一个主题就是对消息的一个分类。生产者将消息发送到特定主题，消费者订阅主题或主题的某些分区进行消费。

### 消息

消息是Kafka通信的基本单位，由一个固定长度的消息头和一个可变长度的消息体构成。在老版本中，每一条消息称为Message；在由Java重新实现的客户端中，每一条消息称为Record。

### 分区和副本

Kafka将一组消息归纳为一个主题，而每个主题又被分成一个或多个分区（Partition）。每个分区由一系列有序、不可变的消息组成，是一个有序队列。

Kafka只能保证一个分区之内消息的有序性，并不能保证跨分区消息的有序性。每条消息被追加到相应的分区中，是顺序写磁盘，因此效率非常高，这是Kafka高吞吐率的一个重要保证。

### Leader副本和Follower副本

由于Kafka副本的存在，就需要保证一个分区的多个副本之间数据的一致性，Kafka会选择该分区的一个副本作为Leader副本，而该分区其他副本即为Follower副本，只有Leader副本才负责处理客户端读/写请求，Follower副本从Leader副本同步数据。如果没有Leader副本，那就需要所有的副本都同时负责读/写请求处理，同时还得保证这些副本之间数据的一致性，假设有n个副本则需要有n×n条通路来同步数据，这样数据的一致性和有序性就很难保证。

引入Leader副本后客户端只需与Leader副本进行交互，这样数据一致性及顺序性就有了保证。Follower副本从Leader副本同步消息，对于n个副本只需n-1条通路即可，这样就使得系统更加简单而高效。副本Follower与Leader的角色并不是固定不变的，如果Leader失效，通过相应的选举算法将从其他Follower副本中选出新的Leader副本。

### 偏移量

任何发布到分区的消息会被直接追加到日志文件（分区目录下以“.log”为文件名后缀的数据文件）的尾部，而每条消息在日志文件中的位置都会对应一个按序递增的偏移量。偏移量是一个分区下严格有序的逻辑值，它并不表示消息在磁盘上的物理位置。由于Kafka几乎不允许对消息进行随机读写，因此Kafka并没有提供额外索引机制到存储偏移量，也就是说并不会给偏移量再提供索引。消费者可以通过控制消息偏移量来对消息进行消费，如消费者可以指定消费的起始偏移量。为了保证消息被顺序消费，消费者已消费的消息对应的偏移量也需要保存。需要说明的是，消费者对消息偏移量的操作并不会影响消息本身的偏移量。旧版消费者将消费偏移量保存到ZooKeeper当中，而新版消费者是将消费偏移量保存到Kafka内部一个主题当中。当然，消费者也可以自己在外部系统保存消费偏移量，而无需保存到Kafka中。

### 日志段

一个日志又被划分为多个日志段（LogSegment），日志段是Kafka日志对象分片的最小单位。与日志对象一样，日志段也是一个逻辑概念，一个日志段对应磁盘上一个具体日志文件和两个索引文件。日志文件是以“.log”为文件名后缀的数据文件，用于保存消息实际数据。两个索引文件分别以“.index”和“.timeindex”作为文件名后缀，分别表示消息偏移量索引文件和消息时间戳索引文件。

### 代理

Kafka集群就是由一个或多个Kafka实例构成，我们将每一个Kafka实例称为代理（Broker），通常也称代理为Kafka服务器（KafkaServer）。在生产环境中Kafka集群一般包括一台或多台服务器，我们可以在一台服务器上配置一个或多个代理。每一个代理都有唯一的标识id，这个id是一个非负整数。在一个Kafka集群中，每增加一个代理就需要为这个代理配置一个与该集群中其他代理不同的id, id值可以选择任意非负整数即可，只要保证它在整个Kafka集群中唯一，这个id就是代理的名字，也就是在启动代理时配置的broker.id对应的值，因此在本书中有时我们也称为brokerId。

### 生产者

生产者（Producer）负责将消息发送给代理，也就是向Kafka代理发送消息的客户端。

### 消费者和消费组

消费者（Comsumer）以拉取（pull）方式拉取数据，它是消费的客户端。在Kafka中每一个消费者都属于一个特定消费组（ConsumerGroup），我们可以为每个消费者指定一个消费组，以groupId代表消费组名称，通过group.id配置设置。如果不指定消费组，则该消费者属于默认消费组test-consumer-group。同时，每个消费者也有一个全局唯一的id，通过配置项client.id指定，如果客户端没有指定消费者的id, Kafka会自动为该消费者生成一个全局唯一的id，格式为${groupId}-${hostName}-${timestamp}-${UUID前8位字符}。同一个主题的一条消息只能被同一个消费组下某一个消费者消费，但不同消费组的消费者可同时消费该消息。消费组是Kafka用来实现对一个主题消息进行广播和单播的手段，实现消息广播只需指定各消费者均属于不同的消费组，消息单播则只需让各消费者属于同一个消费组。

### ISR

Kafka在ZooKeeper中动态维护了一个ISR（In-sync Replica），即保存同步的副本列表，该列表中保存的是与Leader副本保持消息同步的所有副本对应的代理节点id。如果一个Follower副本宕机（本书用宕机来特指某个代理失效的情景，包括但不限于代理被关闭，如代理被人为关闭或是发生物理故障、心跳检测过期、网络延迟、进程崩溃等）或是落后太多，则该Follower副本节点将从ISR列表中移除。

### Kafka特性

1．消息持久化

消息系统数据持久化一般采用为每个消费者队列提供一个B树或其他通用的随机访问数据结构来维护消息的元数据，B树操作的时间复杂度为O(log n), O(log n)的时间复杂度可以看成是一个常量时间，而且B树可以支持各种各样的事务性和非事务性语义消息的传递。尽管B树具有这些优点，但这并不适合磁盘操作。目前的磁盘寻道时间一般在10ms以内，对一块磁盘来说，在同一时刻只能有一个磁头来读写磁盘，这样在并发IO能力上就有问题。同时，对树结构性能的观察结果表明：其性能会随着数据的增长而线性下降。鉴于消息系统本身的作用考虑，数据的持久化队列可以建立在简单地对文件进行追加的实现方案上。因为是顺序追加，所以Kafka在设计上是采用时间复杂度O(1)的磁盘结构，它提供了常量时间的性能，即使是存储海量的信息（TB级）也如此，性能和数据的大小关系也不大，同时Kafka将数据持久化到磁盘上，这样只要磁盘空间足够大数据就可以一直追加，而不会像一般的消息系统在消息被消费后就删除掉，Kafka提供了相关配置让用户自己决定消息要保存多久，这样为消费者提供了更灵活的处理方式，因此Kafka能够在没有性能损失的情况下提供一般消息系统不具备的特性。

2．高吞吐量

高吞吐量是Kafka设计的主要目标，Kafka将数据写到磁盘，充分利用磁盘的顺序读写。同时，Kafka在数据写入及数据同步采用了零拷贝（zero-copy）技术，采用sendFile()函数调用，sendFile()函数是在两个文件描述符之间直接传递数据，完全在内核中操作，从而避免了内核缓冲区与用户缓冲区之间数据的拷贝，操作效率极高。Kafka还支持数据压缩及批量发送，同时Kafka将每个主题划分为多个分区，这一系列的优化及实现方法使得Kafka具有很高的吞吐量。经大多数公司对Kafka应用的验证，Kafka支持每秒数百万级别的消息。

3．扩展性

Kafka要支持对大规模数据的处理，就必须能够对集群进行扩展，分布式必须是其特性之一，这样就可以将多台廉价的PC服务器搭建成一个大规模的消息系统。Kafka依赖ZooKeeper来对集群进行协调管理，这样使得Kafka更加容易进行水平扩展，生产者、消费者和代理都为分布式，可配置多个。同时在机器扩展时无需将整个集群停机，集群能够自动感知，重新进行负责均衡及数据复制。

4．多客户端支持

Kafka核心模块用Scala语言开发，但Kafka支持不同语言开发生产者和消费者客户端应用程序。0.8.2之后的版本增加了Java版本的客户端实现，0.10之后的版本已废弃Scala语言实现的Producer及Consumer，默认使用Java版本的客户端。Kafka提供了多种开发语言的接入，如Java、Scala、C、C++、Python、Go、Erlang、Ruby、Node.js等。

5.Kafka Streams

Kafka在0.10之后版本中引入Kafak Streams。Kafka Streams是一个用Java语言实现的用于流处理的jar文件。

6．安全机制

当前版本的Kafka支持以下几种安全措施：● 通过SSL和SASL(Kerberos), SASL/PLAIN验证机制支持生产者、消费者与代理连接时的身份认证；● 支持代理与ZooKeeper连接身份验证；● 通信时数据加密；● 客户端读、写权限认证；● Kafka支持与外部其他认证授权服务的集成。

7．数据备份

Kafka可以为每个主题指定副本数，对数据进行持久化备份，这可以一定程度上防止数据丢失，提高可用性。

8．轻量级

Kafka的代理是无状态的，即代理不记录消息是否被消费，消费偏移量的管理交由消费者自己或组协调器来维护。同时集群本身几乎不需要生产者和消费者的状态信息，这就使得Kafka非常轻量级，同时生产者和消费者客户端实现也非常轻量级。

9．消息压缩

Kafka支持Gzip、Snappy、LZ4这3种压缩方式，通常把多条消息放在一起组成MessageSet，然后再把MessageSet放到一条消息里面去，从而提高压缩比率进而提高吞吐量。

### Kafka应用场景

消息系统或是说消息队列中间件是当前处理大数据一个非常重要的组件，用来解决应用解耦、异步通信、流量控制等问题，从而构建一个高效、灵活、消息同步和异步传输处理、存储转发、可伸缩和最终一致性的稳定系统。当前比较流行的消息中间件有Kafka、RocketMQ、RabbitMQ、ZeroMQ、ActiveMQ、MetaMQ、Redis等，这些消息中间件在性能及功能上各有所长。如何选择一个消息中间件取决于我们的业务场景、系统运行环境、开发及运维人员对消息中件间掌握的情况等。我认为在下面这些场景中，Kafka是一个不错的选择。

（1）消息系统。Kafka作为一款优秀的消息系统，具有高吞吐量、内置的分区、备份冗余分布式等特点，为大规模消息处理提供了一种很好的解决方案。

（2）应用监控。利用Kafka采集应用程序和服务器健康相关的指标，如CPU占用率、IO、内存、连接数、TPS、QPS等，然后将指标信息进行处理，从而构建一个具有监控仪表盘、曲线图等可视化监控系统。例如，很多公司采用Kafka与ELK（ElasticSearch、Logstash和Kibana）整合构建应用服务监控系统。

（3）网站用户行为追踪。为了更好地了解用户行为、操作习惯，改善用户体验，进而对产品升级改进，将用户操作轨迹、内容等信息发送到Kafka集群上，通过Hadoop、Spark或Strom等进行数据分析处理，生成相应的统计报告，为推荐系统推荐对象建模提供数据源，进而为每个用户进行个性化推荐。

（4）流处理。需要将已收集的流数据提供给其他流式计算框架进行处理，用Kafka收集流数据是一个不错的选择，而且当前版本的Kafka提供了Kafka Streams支持对流数据的处理。

（5）持久性日志。Kafka可以为外部系统提供一种持久性日志的分布式系统。日志可以在多个节点间进行备份，Kafka为故障节点数据恢复提供了一种重新同步的机制。同时，Kafka很方便与HDFS和Flume进行整合，这样就方便将Kafka采集的数据持久化到其他外部系统。

## Kafka核心组件

### 延迟操作组件

本节先简要介绍Kafka延迟操作的组件，该组件可以辅助Kafka其他组件完成相应的功能，如协助客户端处理创建主题操作、协助组协调器（GroupCoordinator）处理JoinGroupRequest和HeartbeatRequest请求、协助副本管理器（ReplicaManager）处理ProduceRequest和FetchRequest请求。

#### DelayedOperation

DelayedOperation只有一个AtomicBoolean类型的completed属性，用来控制某个延迟操作。在延迟时间（delayMs）内，onComplete()方法只被调用一次。DelayedOperation主要方法如下。

● tryComplete()方法：一个抽象方法，由子类来实现，负责检测执行条件是否满足。若满足执行条件，则调用forceComplete()方法完成延迟操作。

● forceCompete()方法：该方法在条件满足时，检测延迟任务是否未被执行。若未被执行，则先调用TimerTask.cancel()方法解除该延迟操作与TimerTaskEntry的绑定，将该延迟操作从TimerTaskList链表中移除，然后调用onComplete()方法让延迟操作执行完成。通过completed的CAS原子操作（completed.compareAndSet），可以保证并发操作时只有第一个调用该方法的线程能够顺利调用onComplete()完成延迟操作，其他线程获取的completed属性为false，即不会调用onComplete()方法，这就保证了onComplete()只会被调用一次。

● onComplete()方法：是一个抽象方法，由子类来实现，执行延迟操作满足执行条件后需要执行的实际业务逻辑。例如，DelayedProduce和DelayedFetch都是在该方法内调用responseCallback向客户端做出响应。

● safeTryComplete()方法：以synchronized同步锁调用onComplete()方法，供外部调用。

● onExpiration()方法：也是一个抽象方法，由子类来实现当延迟操作已达失效时间的相应逻辑处理。Kafka通过SystemTimer来定期检测请求是否超时。SystemTimer是Kafka实现的底层基于层级时间轮和DelayQueue定时器，维护了一个newFixedThreadPool线程池，用于提交相应的线程执行。

Kafka将一些不立即执行而要等待满足一定条件之后才触发完成的操作称为延迟操作，并将这类操作定义为一个抽象类DelayedOperation, DelayedOperation是一个基于事件启动有失效时间的TimerTask。TimerTask实现了Runnable接口，维护了一个TimerTaskEntry对象，TimerTaskEntry绑定了一个TimerTask, TimerTaskEntry被添加到TimerTaskList中，TimerTaskList是一个环形双向链表，按失效时间排序。

#### DelayedOperationPurgatory

DelayedOperationPurgatory是一个对DelayedOperation管理的辅助类，为了书写简便，我们将其简称为Purgatory。Purgatory以泛型的形式将一个DelayedOperation添加到其内部维护的Pool[Any, Watchers]类型watchersForKey对象中，同时将DelayedOperation添加到SystemTimer中。

其中，Watchers是Purgatory的内部类，底层是一个ConcurrentLinkedQueue，该类定义了一个ConcurrentLinkedQueue类型的operations属性，用于保存DelayedOperation。从Watchers类名可以看出，该类的作用就是对DelayedOperation进行监视。Watchers提供了以下3个对DelayedOperation操作的方法。

● watch()方法：用于将DelayedOperation添加到operations集合中。

● tryCompleteWatched()方法：用于迭代operations集合中的DelayedOperation，通过DelayedOperation.isCompleted检测该DelayedOperation是否已执行完成。若已执行完成，则从operations集合中移除该DelayedOperation。否则调用DelayedOperation.safeTryComplete()方法尝试让该DelayedOperation执行完成，若执行完成，即safeTryComplete()方法返回true，则将该DelayedOperation从operations集合中移除。最后检测operations集合是否为空，如果operations为空，则表示该operations所关联的DelayedOperation已全部执行完成，因此将该Watchers从Purgatory的Pool中移除。

● purgeCompleted()方法：与tryCompleteWatched()方法基本功能类似，区别在于purgeCompleted()方法只单纯地将该operations集合中已完成的DelayedOperation移除，对未完成的DelayedOperation并不尝试将其执行完成。

####  DelayedProduce

DelayedProduce是协助ReplicaManager完成相应延迟操作的，而ReplicaManager的主要功能是负责将生产者发送的消息写入Leader副本、管理Follower副本与Leader副本之间的同步以及副本角色之间的转换，DelayedProduce显然是与生产者发送消息相关的延迟操作，因此只可能在消息写入Leader副本时需要DelayedProduce的协助。

#### DelayedFetch

DelayedProduce是在ProduceRequest处理中对生产者发送消息的延迟操作，自然DelayedFetch就是在FetchRequest处理时进行的延迟操作。在Kafka中只有消费者或是Follower副本会发起FetchReuqest请求。FecthRequest是由KafkaApis.handleFetchRequest()方法处理的，在该方法中会调用ReplicaManager.fetchMessages()方法从相应分区的Leader副本拉取消息。在ReplicaManager.fetchMessages()方法中会创建DelayedFetch延迟操作。

DelayedFetch构造方法有一个fetchMetadata参数，该参数是一个FetchMetadata对象，该对象包括指定本次拉取操作获取数据的最小及最大字节数字段、是否只从Leader副本读取以及是否只读HW之前的数据的标志字段、一个用来标识是消费者还是Follower副本的replicaId字段、用来记录本次从每个分区拉取结果的fetchPartitionsStatus字段。从FetchMetadata对象的字段也可以看出之所以在拉取消息时需要延迟操作，是为了让本次拉取消息获取到足够的数据。

DelayedFetch若满足以下条件之一则表示可完成延迟操作执行。（1）发生异常，Leader副本发生了迁移，当前的代理不再是Leader副本。

（2）发生异常，拉取消息的分区不存在。

（3）日志段发生了切割，请求拉取的消息偏移量已不在活跃段内，同时Leader副本没有处在限流处理的状态。

（4）累积拉取的消息数已超过了最小字节数限制。

#### DelayedJoin

DelayedJoin是协助组协调器在消费组准备平衡操作时进行相应的处理。当消费组的状态转换为PreparingRebalance时，即准备进行平衡操作，在组协调器的prepareRebalance()方法中会创建一个DelayedJoin对象，并交由DelayedOperationPurgatory负责监视管理。

在消费组进行平衡操作时之所以需要DelayedJoin处理，是为了让组协调器等待当前消费组下所有的消费者都请求加入消费组，即发起了JoinGroupRequest请求。每次组协调器处理完JoinGroupRequest时都会检测DelayedJoin是否满足了完成执行的条件。

#### DelayedHeartbeat

DelayedHeartbeat用于协助消费者与组协调器心跳检测相关的延迟操作，DelayedHeartbeat相关功能的实现是调用GroupCoordinator的相应方法来完成的。

DelayedHeartbeat.tryComplete()方法调用GroupCoordinator.tryCompleteHeartbeat()方法来检测是否满足执行条件，若满足以下条件之一则可触发执行。

（1）member.awaitingJoinCallback不为空。其中member是指MemberMetadata, Kafka将一个组协调器管理的成员元数据信息封装为一个MemberMetadata对象，成员的元数据信息包括心跳Session超时时间、上一次更新心跳的时间戳、成员所支持的协议（对于消费者是指分区分配策略），同时还包括组的状态信息等。awaitingJoinCallback不为空，则表示消费者已发出了JoinGroupRequest，现在正在等待组协调器返回JoinGroupResponse。

（2）member.awaitingSyncCallback不为空，表示正在进行SyncGroupRequest处理。

（3）上一次更新心跳的时间戳与member.sessionTimeoutMs之和大于heartbeatDeadline。

（4）消费者已离开消费组。

#### DelayedCreateTopics

在创建主题时，需要为主题的每个分区分配到Leader之后，才调用回调函数将创建主题结果返回给客户端。DelayedCreateTopics延迟操作等待该主题的所有分区副本分配到Leader或是等待超时后调用回调函数返回给客户端。

● DelayedCreateTopics.tryComplete()方法用于检测延迟操作是否已满足执行条件。当检测到该主题的所有分区副本都分配到Leader后，LeaderDelayedCreateTopics即满足了执行条件。

● DelayedCreateTopics.onComplete()方法构造该主题与错误码映射关系，调用回调函数返回给客户端。

● DelayedCreateTopics.onExpiration()方法也是一个空实现，没有进行任何处理。

### 控制器

在启动Kafka集群时，每一个代理都会实例化并启动一个KafkaController，并将该代理的brokerId注册到ZooKeeper的相应节点当中。Kafka集群中各代理会根据选举机制选出其中一个代理作为Leader，即Leader控制器（本书简称之为控制器，在没有特殊说明情况下，控制器均指Leader控制器）。当控制器发生宕机后其他代理再次竞选出新的控制器。控制器负责主题的创建与删除、分区和副本的管理以及代理故障转移处理等。当一个代理被选举成为控制器时，该代理对应的KafkaController就会注册（Register）控制器相应的操作权限，同时标记自己是Leader。当代理不再成为控制器时，就要注销掉（DeRegister）相应的权限。实现这些功能的程序入口是在Kafka核心core工程下的kafka.controller.KafkaController类。在讲解控制器之前，有必要先介绍如下字段、数据结构和术语。

● controller_epoch：用于记录控制器发生变更次数，即记录当前的控制器是第几代控制器（本书中我们称之为控制器轮值次数）。初始值为0，当控制器发生变更时，每选出一个新的控制器需将该字段加1，每个向控制器发送的请求都会带上该字段，如果请求的controller_epoch的值小于内存中controller_epoch的值，则认为这个请求是向已过期的控制器发送的请求，那么本次请求就是一个无效的请求。若该值大于内存中controller_epoch的值，则说明已有新的控制器当选了。通过该值来保证集群控制器的唯一性，进而保证相关操作一致性。该字段对应ZooKeeper的controller_epoch节点，通过登录ZooKeeper客户端执行get/controller_epoch命令，可以查看该字段对应的值。

● zkVersion：作用类似数据库乐观锁，用于更新ZooKeeper路径下相应元数据信息，如controller epoch, ISR信息等。

● leader_epoch：分区Leader更新次数。controller_epoch是相对代理而言的，而leader_epoch是相对于分区来说的。由于各请求达到顺序不同，控制器通过controller_epoch和leader_epoch来确定具体应该执行哪个命令操作。

● 已分配副本（assigned replica）：每个分区的所有副本集合被称作已分配副本，简写为AR，本书中所有AR均表示此含义，而ISR是与分区Leader保持同步的副本列表。

● LeaderAndIsr:Kafka将Leader对应的brokerId和ISR列表封装成一个LeaderAndIsr类。以JSON串表示为{"leader" :Leader的brokerId, "leader_epoch" :leader更新次数，"isr":ISR列表}。

● 优先副本（preferred replica）：在AR中，第一个副本称为preferred replica，也就是我们说的优先副本。理想情况下，优先副本即是该分区的Leader, Kafka要确保所有主题的优先副本在Kafka集群中均衡分布，这样就保证了所有分区的Leader均衡分布。保证Leader在集群中均衡分布很重要，因为所有的读写请求都由分区Leader副本进行处理，如果Leader分布过于集中，就会造成集群负载不均衡。为了保证优先副本的均衡分布，Kafka提供了5种分区选举器（PartitionLeaderSelector），当分区数发生变化或是分区Leader宕机时就会通过分区选举器及时选出分区新的Leader。

### 控制器初始化

每个代理在启动时会实例化并启动一个KafkaController。KafkaController实例化时主要完成以下工作。

（1）创建一个ControllerContext实例对象，该对象很重要的一个作用是用于缓存控制器各种处理操作所需要的数据结构。ControllerContext实例化时会初始化用于记录控制器选举次数的epoch及与之对应的zkVersion字段的值，初始时都为0，同时设置当前正常运行的代理列表、主题列表、各主题对应分区与副本的AR列表等。声明控制器与其他代理通信的ControllerChannelManager对象，ControllerChannelManager在这里只是声明并没有创建和启动。实例化代理选举控制器操作的ReentrantLock。

（2）实例化用于维护和管理分区状态的状态机（PartitionStateMachine）。Kafka分区定义了4个状态，分区状态及描述如表3-1所示。

![image-20200512111008516](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111008516.png)

（3）实例化一个对副本状态管理的状态机ReplicaStateMachine。Kafka对副本定义了7种状态，各副本状态及描述如表3-2所示。

![image-20200512111031324](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111031324.png)

（4）创建用于将当前代理选举为控制器的ZooKeeperLeaderElector选举器对象，实例化该对象时需要传递两个回调函数：完成控制器相应初始化操作的onControllerFailover()方法，以及当新的控制器当选时让先前的控制器注销控制器权限的onControllerResignation()方法。Kafka控制器选举策略是在ZooKeeper的/controller路径下创建一个临时节点，并注册一个LeaderChangeListener，通过该监听器来监听该临时节点，当该临时节点信息发生变更时，就会触发该监听器。当新当选的控制器信息被保存时，就会触发该监听器的handleDataChange()方法进行相应处理；当监听器监听到/controller路径下控制器信息被删除时，将触发onControllerResignation()回调方法，同时触发重新选举机制。关于控制器的选举过程在后面小节会有详细讲解，这里不再赘述。

（5）创建一个独立定时任务KafkaScheduler，该定时任务用于控制器进行平衡操作，其生命周期只在代理成为Leader控制器期间有效，当代理不再是Leader控制器时，即调用onControllerResignation()方法时该定时任务就会被关闭。

（6）声明一个对主题操作管理的TopicDeletionManager对象。该对象在3.2.5节将会详细阐述。

（7）创建一个用于在分区状态发生变化时为分区选举出Leader副本的分区选举器PartitionLeaderSelector。Kafka提供了5种分区选举器，这些选举器会被分区状态机调用，当分区的状态发生变化时，根据分区所处状态选用不同的选举器为分区选出Leader副本。分区选举器只定义了一个selectLeader()方法，该方法接受两个对象，一个表示要被选举为Leader的分区对象TopicAndPartition，另一个表示该分区当前的Leader和ISR对象LeaderAndIsr。该方法返回一个元组，元组包括新当选的Leader, ISR对象以及由一组副本构成的LeaderAndIsrRequest对象。分区选举器的类层次结构如图3-5所示。

![image-20200512111100766](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111100766.png)

各选举器的功能及选举策略如下。

● OfflinePartitionLeaderSelector：分区状态机启动、新创建一个分区或是将一个分区状态由NewPartition状态、OfflinePartition状态转换到OnlinePartition状态时会调用该选举器，为分区选出Leader，得到分区的LeaderAndIsr。

● ReassignedPartitionLeaderSelector：当分区进行重分配时会调用该选举器。该选举器的选举策略是从AR列表中找出存活副本列表，若有存活的副本则取存活副本列表的第一个副本作为Leader，将当前ISR作为新的ISR，将AR作为接受LeaderAndIsr请求的副本集合。若没有候选的副本，则抛出NoReplicaOnlineException异常。

● PreferredReplicaPartitionLeaderSelector：该选举器直接将优先副本设置为分区的Leader。该选举器首先根据当前Leader是不是由优先副本担任来决定是否需要选举。若当前Leader由优先副本担任则无需设置，仅抛出LeaderElectionNotNeededException异常进行提示；若优先副本不是Leader但在该分区的ISR列表中，则将优先副本选为Leader，将AR作为接受LeaderAndIsr请求的副本集合；否则抛出StateChangeFailedException异常。

● ControlledShutdownLeaderSelector：该选举器将从ISR中剔除已关闭的节点，将剔除已关闭节点后的ISR作为新的ISR，同时从新的ISR中选取第一个作为Leader副本。将AR中剔除已关闭节后的副本节点作为接受LeaderAndIsr请求的副本集合。

● NoOpLeaderSelector：该选举器只返回当前分区的Leader和ISR。

（8）实例化ControllerBrokerRequestBatch。在前面实例化了分区状态机和副本状态机，这两个状态机在相应状态发生变化时相应监听器都会调用各自的handleStateChange()方法进行处理，而ControllerBrokerRequestBatch封装了leaderAndIsrRequestMap、stopReplicaRequestMap和updateMetadataRequestMap这3个集合，用来记录和缓存handleStateChange()方法中产生的request，控制器将这些request交由ControllerBrokerRequestBatch.sendRequestsToBrokers()方法批量发送出去，交由KafkaApis调用相应的handle方法进行处理。

（9）实例化3个监听器，即用于监听分区重分配的PartitionsReassignedListener，用于监听当分区状态变化时触发PreferredReplicaPartitionLeaderSelector选举器将优先副本选举为Leader的PreferredReplicaElectionListener、用于监听当ISR发生变化时将ISR变化通知给ZooKeeper进行更新操作，同时向所有的代理节点发送元数据修改请求的IsrChangeNotificationListener。

### 控制器选举过程

每个代理启动时会创建一个KafkaController实例，当KafkaController启动后就会从所有代理中选择一个代理作为控制器，控制器是所有代理的Leader，因此这里也称之为Leader选举。除了在启动时会导致选举外，当控制器所在代理发生故障或ZooKeeper通过心跳机制感知控制器与自己的连接Session已过期时，也会再次从所有代理中选出一个节点作为集群的控制器。

![image-20200512111254502](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111254502.png)

### 故障转移

触发控制器进行选举有3种情况：一是在控制器启动的时候，二是当控制器发生故障转移的时候，三是当心跳检测超时的时候。因此，我们说控制器故障转移的本质是控制权的转移，而控制权的转移也就是重新选出新的控制器。在控制器实例化时创建了一个ZooKeeperLeaderElector对象，实例化该对象时需要两个回调函数，分别用于代理当选为控制器时注册相应权限的onControllerFailover()方法和不再是Leader控制器时注销相应权限的onControllerResignation()方法。对故障转移的讲解，我们也是主要介绍这两个方法具体的实现逻辑。

1.onControllerFailover操作

KafkaController的onControllerFailover()方法的作用就是完成控制器相应的初始化工作，如果当前的控制器正常运行，即在控制器启动时的标志位isRunning为true，则执行以下逻辑完成控制器的初始化，否则表示当前的控制器已关闭，将终止相应的初始化处理。

2.onControllerResignation操作

当一个代理不再是控制器时，需要注销控制器相应的权限及修改相应元数据的初始化信息。KafkaController通过调用KafkaController.onControllerResignation()方法实现一个代理从控制器到普通代理的转变操作。

###  代理上线与下线

1．代理上线

当有新的代理上线时，在代理启动时会向ZooKeeper的/brokers/ids节点下注册该代理的brokerId，此时会被副本状态机在ZooKeeper所注册的BrokerChangerListener监听器监听到该节点信息的变化，通过ZooKeeper中记录的节点信息及ControllerContext缓存的节点信息，计算出新上线的节点集合，对新上线的代理节点调用ControllerChannelManager.addBroker()方法完成新上线代理网络层相关初始化处理。然后调用KafkaController.onBrokerStartup()方法进行处理，该方法处理逻辑如下。

首先，向集群当前所有的代理发送UpdateMetadataRequest请求，这样所有的代理通过这种方式就会知道有新的代理加入。

接着，查找出被分配到新上线节点上的副本集合，通过副本状态机对副本状态进行相应变迁处理，将这些副本的状态更新为OnlineReplica，并通过分区状态机对分区状态为NewPartition和OfflinePartition的分区进行处理，将其状态扭转至OnlinePartition状态，并触发一次分区Leader选举，以确认新增加的代理是否是分区的Leader。

然后，轮询被分配到新上线代理的副本，调用KafkaController.onPartitionReassignment()方法执行服务端分区副本分配操作。

最后，恢复由于新代理上线而被暂停的删除主题操作的线程，让其继续完成服务端删除主题的操作。

2．代理下线

当代理下线时，该代理在ZooKeeper的/brokers/ids节点注册的与该代理对应的节点将被删除，此时BrokerChangeListener的handleChildChange()方法将被触发。

与新代理上线操作类似，首先要查找下线节点的集合，然后轮询下线节点集合，调用ControllerChannelManager.removeBroker()方法，关闭每个下线节点的网络连接，清空下线节点的消息队列，关闭下线节点发送Request请求的线程等。最后调用KafkaController的onBrokerFailure()方法进行处理，该方法处理逻辑如下。

首先，查找Leader副本在下线节点上的分区，将这些分区的状态设置为OfflinePartition，并处理相应状态变迁，然后调用分区状态机的triggerOnlinePartitionStateChange()方法将处于OfflinePartition状态的分区状态转换为OnlinePartition状态，这个过程会通过OfflinePartitionLeaderSelector分区选举器为分区选出Leader，并将Leader和ISR信息写入ZooKeeper中，同时发送UpdateMetadataRequest请求更新元数据信息。

然后，查找所有在下线节点上的副本集合，将该集合分成两部分，一部分是待删除主题的副本，将这些副本的状态转换为ReplicaDeletionIneligible，标记该副本对应的主题暂时不可被删除。另一部分副本即是当前处于正常使用的主题的副本，因此需要对这些副本进行下线相应的处理，将副本状态由OnlineReplica转化为OfflineReplica，此时会将该副本节点从分区的ISR集合中删除，并发送StopReplicaRequest请求，停止该副本从Leader副本同步消息的操作，发送LeaderAndIsrRequest请求，该分区Leader副本和Follower副本根据角色不同分别进行相应处理，同时发送UpdateMetadataRequest请求，更新当前所有存活代理的缓存的元数据信息。

最后，若分区Leader副本分配在下线节点上的所有分区状态转换操作执行完成，则向集群所有存活的代理发送更新元数据的UpdateMetadataRequest请求，执行元数据更新操作。

### 主题管理

1．创建主题

当创建一个主题时会在ZooKeeper的/brokers/topics目录下创建一个与主题同名的节点，在该节点下会记录该主题的分区副本分配方案。

2．删除主题

客户端执行删除主题操作时仅是在ZooKeeper的/admin/delete_topics路径下创建一个与待删除主题同名的节点，返回该主题被标记为删除，保证本步操作成功执行的前提是配置项delete.topic.enable值被设置为true。例如，删除主题“topic-foo”，则客户端执行删除操作时会在/admin/delete_topics路径下创建一个名为“topic-foo”的节点。而实际删除主题的逻辑是异步交由Kafka控制器负责执行的，本小节将介绍控制器在删除主题时的具体实现。

### 分区管理

Kafka控制器对分区的管理包括对分区创建及删除的管理，分区Leader选举的管理，分区自动平衡、分区副本重分配的管理等。控制器对分区创建及删除的管理在3.2.5节有相应介绍，而分区的Leader选举根据分区的不同状态选择不同的分区选举器为分区选出Leader副本，分区Leader的选举过程我们已在相应章节穿插进行阐述，本小节主要介绍控制器如何管理分区自动平衡及分区副本重分配。

1．分区平衡

在onControllerFailover操作时会启动一个分区自动平衡的定时任务，该定时任务会定期检查集群上各代理分区分布是否失去平衡。该过程是调用控制器的checkAndTriggerPartitionRebalance()方法完成。

2．分区重分配

当客户端执行分区重分配操作后（客户端分区重分配相关操作在5.6.2节有详细介绍），会在ZooKeeper的/admin节点下创建一个临时子节点reassign_partitions，将分区副本重分配的分配方案写入该节点中。

### 协调器

Kafka提供了消费者协调器（ConsumerCoordinator）、组协调器（GroupCoordinator）和任务管理协调器（WorkCoordinator）3种协调器（coordinator）。其中任务管理协调器被Kafka Connect用于对works的管理，本书不进行介绍，我们重点关注的是消费者协调器和组协调器，这两种协调器与消费者密切相关。

Kafka的高级消费者即通过ZooKeeperConsumerConnector实现的消费者是强依赖于ZooKeeper的，每一个消费者启动时都会在ZooKeeper的/consumers/${group.id}/ids上注册消费者的客户端id，即${client.id}，会在该路径以及/brokers/ids路径下注册监听器，用于当代理或是消费者发生变化时，消费者进行平衡操作。由于这种方式是每一个消费者对ZooKeeper路径分别进行监听，当发生平衡操作时，一个消费组下的所有消费者同时会触发平衡操作，而消费者之间并不知道其他消费者平衡操作的结果，这样就可能导致Kafka工作在一个不正确的状态。同时这种方式完全依赖于ZooKeeper，以监听的方式来管理消费者，存在以下两个缺陷。

● 羊群效应（herd effect）：任何代理或是消费者的增、减都会触发所有的消费者同时进行平衡操作，每个消费者都对ZooKeeper同一个路径进行操作，这样就有可能发生类似死锁的情况，从而导致平衡操作失败。

● 脑裂问题（split brain）：消费者进行平衡操作时每个消费者都与ZooKeeper进行通信，以判断消费者或是代理变化情况，由于ZooKeeper本身的特性可能导致在同一时候各消费者所获取的状态不一致，这样就会导致Kafka运行在一个不正确状态之下。

### 消费者协调器

消费者协调器（ConsumerCoordinator）是KafkaConsumer的一个成员变量，该KafkaConsumer通过消费者协调器与服务端的组协调器进行通信。由于消费者协调器是KafkaConsumer私有的，因此消费者协调器中存储的信息也只有与之对应的消费者可见，不同消费者之间是看不到彼此的消费者协调器中的信息的。

### 组协调器

组协调器（GroupCoordinator）负责对其管理的组员提交的相关请求进行处理，这里的组员即消费者。它负责管理与消费者之间建立连接，并从与之连接的消费者之中选出一个消费者作为Leader消费者，Leader消费者负责消费者分区的分配，在SyncGroupRequest请求时发送给组协调器，组协调器会在请求处理后返回响应时下发给其管理的所有消费者。同时，组协调器还管理与之连接的消费者的消费偏移量的提交，将每个消费者消费偏移量保存到Kafka的内部主题当中，并通过心跳检测来检测消费者与自己的连接状态。

### 网络通信服务

在KafkaServer启动时，初始化并启动了一个SocketServer服务，用于接受客户端的连接、处理客户端请求、发送响应等，同时创建一个KafkaRequestHandlerPool用于管理KafkaRequestHandler。SocketServer是基于Java NIO实现的网络通信组件，其线程模型为：一个Acceptor线程负责接受客户端所有的连接；N（${num.network.threads}）个Processor线程，每个Processor有多个Selector，负责从每个连接中读取请求；M（${num.io.threads}）个Handler（KafkaRequestHandler）线程处理请求，并将产生的请求返回给Processor线程。而Handler是由KafkaRequestHandlerPool管理，在Processor和Handler之间通过RequestChannel来缓冲请求，每个Handler从RequestChannel.requestQueue接受RequestChannel.Request，并把Request交由KafkaApis的handle()方法处理，经处理后把对应的Response存进RequestChannel.responseQueues队列。Kafka网络层线程模型如图3-15所示。

![image-20200512111758299](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111758299.png)

### 日志管理器

日志管理器（LogManager）是Kafka用来管理所有日志的，也称为日志管理子系统（LogManagement Subsystem）。它负责管理日志的创建与删除、日志检索、日志加载和恢复、检查点及日志文件刷写磁盘以及日志清理等。

### Kafka日志结构

Kafka消息是以主题为基本单位进行组织的，各个主题之间相互独立。每个主题在逻辑结构上又由一个或多个分区构成，分区数可以在创建主题时指定，也可以在主题创建后再修改。可以通过Kafka自带的用于主题管理操作的脚本kafka-topics.sh来修改某个主题的分区数，但只能增加一个主题的分区数而不能减少其分区数。每个分区可以有一个或多个副本，从副本中会选出一个副本作为Leader, Leader负责与客户端进行读写操作，其他副本作为Follower。生产者将消息发送到Leader副本的代理节点，而Follower副本从Leader副本同步数据。

在存储结构上分区的每个副本在逻辑上对应一个Log对象，每个Log又划分为多个LogSegment，每个LogSegment包括一个日志文件和两个索引文件，其中两个索引文件分别为偏移量索引文件和时间戳索引文件。Log负责对LogSegment的管理，在Log对象中维护了一个ConcurrentSkipListMap，其底层是一个跳跃表，保存该主题所有分区对应的所有LogSegment。Kafka将日志文件封装为一个FileMessageSet对象，将两个索引文件封装为OffsetIndex和TimeIndex对象。Log和LogSegment都是逻辑上的概念，Log是对副本在代理上存储文件的逻辑抽象，LogSegmnent是对副本存储文件下每个日志片段的抽象，日志文件和索引文件才与磁盘上的物理存储相对应。假设有一个名为“log-format”的主题，该主题有3个分区，每个分区对应一个副本，则在存储结构中各对象映射关系如图3-19所示。

![image-20200512111851109](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512111851109.png)

### 副本管理器

在Kafka 0.8版本中引入了副本机制，引入副本机制使得Kafka能够在整个集群中只要保证至少有一个代理存活就不会影响整个集群的工作，从而大大提高了Kafka集群的可靠性和稳定性。这里提到代理存活的概念，同其他分布式系统一样，Kafka对代理是否存活（alive）也有明确的定义，Kafka存活要满足两个条件。

（1）一个存活的节点必须与ZooKeeper保持连接，维护与ZooKeeper的Session（这是通过ZooKeeper的心跳机制来实现的）

（2）如果一个节点作为Follower副本，该节点必须能及时与分区的Leader副本保持消息同步，不能落后太久。

### 动态配置管理器

动态配置管理器（DynamicConfigManager）主要用来对相关配置的变化进行处理，Kafka将可以通过ZooKeeper进行管理的配置划分为4个类型，称为配置类型（ConfigType）或配置级别，每个配置类型称为一个实体（entity），这4个类型分别为Topic（主题级别）、Client（客户端级别）、User（用户级别）和Broker（代理级别）。用entity-type来指定所属级别，4个级别的entity-type依次为topics, clients, users和brokers。

### 代理健康检测

Kafka集群依赖于ZooKeeper进行管理，每个代理启动时都向ZooKeeper进行一系列元数据的注册，即在ZooKeeper相应目录下创建一个临时节点，当代理与ZooKeeper连接断开后相应的临时节点也会被删除。Kafka对代理健康状态检测实现方案较简单，每个代理启动时会在ZooKeeper的/brokers/ids/路径下注册自己的brokerId，并注册一个SessionExpireListener监听器，该监听器用来监听代理与ZooKeeper连接是否会话Session超时，若发生Session超时，则与该代理相关的临时节点也会被清除，此时ZooKeeper会与当前代理重新创建一条连接，Kafka健康检测机制就需要重新在ZooKeeper上进行注册，创建临时节点并写入相应的元数据信息。

Kafka健康检测机制实现类是KafkaHealthcheck，该类实例化时会创建一个SessionExpireListener监听器，该监听器实现了IZkStateListener接口，在handleNewSession()方法中调用KafkaHealthcheck. register()方法。KafkaHealthCheck.startup()方法首先向ZooKeeper注册SessionExpireListener监听器，然后调用KafkaHealthcheck.register()方法，register()方法将代理节点的连接信息写入到该节点在ZooKeeper对应的节点中。

## Kafka核心流程

### KafkaServer启动流程

KafkaServer实例化成功后，调用startup()方法来完成KafkaServer启动操作，具体过程如下。

（1）首先实例化用于限流的QuotaManagers，这些Quota会在后续其他组件实例化时作为入参注入当中，接着设置代理状态为Starting，即开始启动代理。代理状态机提供了6种状态，如表4-1所示。

![image-20200512132007939](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200512132007939.png)

（2）启动任务调度器（KafkaScheduler）, KafkaScheduler是基于Java.util.concurrent.ScheduledThreadPoolExecutor来实现的，在KafkaServer启动时会构造一个线程总数为${background.threads}的线程池，该配置项默认值为10，每个线程的线程名以“kafka-scheduler-”为前缀，后面连接递增的序列号，这些线程作为守护线程在KafkaServer启动时开始运行，负责副本管理及日志管理调度等。

（3）创建与ZooKeeper的连接，检查并在ZooKeeper中创建存储元数据的目录节点，若目录不存在则创建相应目录。KafkaServer启动时在ZooKeeper中要保证如图4-4所示文件目录树被成功创建。

（4）通过UUID.randomUUID()生成一个uuid值，然后经过base64处理得到的值作为Cluster的id，调用Kafka实现的org.apache.kafka.common.ClusterResourceListener通知集群元数据信息发生变更操作。此时生成的Cluster的id信息会写入ZooKeeper的/cluster/id节点中，在ZooKeeper客户端通过get命令可以查看该Cluster的id信息。

（5）实例化并启动日志管理器（LogManager）。LogManager负责日志的创建、读写、检索、清理等操作。

（6）实例化并启动SocketServer服务。SocketServer启动过程在3.4节已有详细介绍，这里不再赘述。

（7）实例化并启动副本管理器（ReplicaManager）。副本管理器负责管理分区副本，它依赖于任务调度器与日志管理器，处理副本消息的添加与读取的操作以及副本数据同步等操作。

（8）实例化并启动控制器。每个代理对应一个KafkaController实例，KafkaController在实例化时会同时实例化分区状态机、副本状态机和控制器选举器ZooKeeperLeaderElector，实例化4种用于分区选举Leader的PartitionLeaderSelector对象。在KafkaController启动后，会从KafkaController中选出一个节点作为Leader控制器。Leader控制器主要负责分区和副本状态的管理、分区重分配、当新创建主题时调用相关方法创建分区等。

（9）实例化并启动组协调器GroupCoordinator。Kafka会从代理中选出一个组协调器，对消费者进行管理，当消费者或者订阅的分区主题发生变化时进行平衡操作。

（10）实例权限认证组件以及Handler线程池（KafkaRequestHandlerPool）。在KafkaRequest HandlerPool中主要是创建${ num.io.threads }个KafkaRequestHandler,Handler循环从Request Channel中取出Request并交给kafka.server.KafkaApis来处理具体的业务逻辑。在实例化KafkaRequestHandlerPool之前先要实例化KafkaApis, Kafka将所有请求的requestId封装成一个枚举类ApiKeys。当前版本的Kafka支持21种类型的请求。

（11）实例化动态配置管理器。注册监听ZooKeeper的/config路径下各子节点信息变化。

（12）实例化并启动Kafka健康状态检查（KafkaHealthcheck）。Kafka健康检查机制主要是在ZooKeeper的/brokers/ids路径下创建一个与当前代理的id同名的节点，该节点也是一个临时节点。当代理离线时，该节点会被删除，其他代理或者消费者通过判断/brokers/ids路径下是否有某个代理的brokerId来确定该代理的健康状态。

（13）向meta.properties文件中写入当前代理的id以及固定版本号为0的version信息。

（14）注册Kafka的metrics信息，在KafkaServer启动时将一些动态的JMX Beans进行注册，以便于对Kafka进行跟踪监控。

### 客户端创建主题

![image-20200524154224570](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154224570.png)

![image-20200524154244709](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154244709.png)

### 生产者

首先要介绍的是acks配置项，Kafka为生产者提供3种消息确认机制（acks），用于配置代理接收到消息后向生产者发送确认信号，以便生产者根据acks进行相应处理，该机制通过属性request.required.acks设置，取值可为0、-1、1中之一，默认取1。

（1）当acks=0时，生产者不用等待代理返回确认信息，而连续发送消息。显然这种方式加快了消息投递的速度，然而无法保证消息是否已被代理接受，有可能存在丢失数据的风险。

（2）当acsk=1时，生产者需要等待Leader副本已成功将消息写入日志文件中。这种方式在一定程度上降低了数据丢失的可能性，但仍无法保证数据一定不会丢失。如果在Leader副本成功存储数据后，Follower副本还没有来得及进行同步，而此时Leader宕机了，那么此时虽然数据已进行了存储，由于原来的Leader已不可用而会从集群中下线，同时存活的代理又再也不会有从原来的Leader副本存储的数据，此时数据就会丢失。

（3）当acks=-1时，Leader副本和所有ISR列表中的副本都完成数据存储时才会向生产者发送确认信息，这种策略保证只要Leader副本和Follower副本中至少有一个节点存活，数据就不会丢失。为了保证数据不丢失，需要保证同步的副本至少大于1，通过参数min.insync.replicas设置，当同步副本数不足此配置值时，生产者会抛出异常，但这种方式同时也影响了生产者发送消息的速度以及吞吐量。

其次介绍的是batch.num.messages配置项，Kafka支持消息批量（Batch）向代理特定分区发送消息，批量大小由属性batch.num.messages设置，表示每次批量发送消息的最大消息数，当生产者采用同步模式发送时该配置项将失效。

![image-20200524154407249](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154407249.png)

![image-20200524154720960](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154720960.png)

![image-20200524154752812](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154752812.png)

### 消费者

KafkaProducer是线程安全的，然而KafkaConsumer是非线程安全的。KafkaConsumer定义了一个acquire()方法用来检测每个方法的调用是否只有一个线程在操作，在KafkaConsumer底层实现时我们可以看到每个方法的第一步就是检测当前方法是否有其他线程正在执行，若有其他线程正在操作即发生并发操作，则抛出ConcurrentModificationException异常。需要注意的是，KafkaConsumer只是通过acquire()方法来检测是否有多线程并发操作，一经发现多线程并发操作就抛出异常，这显然与我们说的同步方法或者锁不同，它并不会因此而阻塞等待，我们可以理解成KafkaConsumer相关的操作是在“轻量级锁”的控制下完成。之所以称为轻量级锁，是因为KafkaConsumer实现了一套思想与锁类似但不等同锁的实现方式，仅通过线程操作记数标记的方式来检测线程是否发生并发操作，以此保证只有一个线程操作。另外，acqurie()方法和release()成对出现与锁的lock和unlock用法类似。

KafkaConsumer定义了以下3个Atomic类型的变量用来管理对KafkaConsumer的操作。

● CONSUMER_CLIENT_ID_SEQUENCE：当客户端没有指定消费者的clientId时，Kafka自动为该消费者线程生成一个clientId，该clientId以“consumer-”为前缀，之后为以CONSUMER_CLIENT_ID_SEQUENCE生成的自增整数组合构成的字符串。

● currentThread：记录当前操作KafkaConsumer的线程Id，该字段起始值为-1（NO_CURR ENT_THREAD）。在acquire()方法中通过检测该字段是否等于-1。若不等于-1则表示已有线程在操作KafkaConsumer，此时抛出ConcurrentModificationException；若该字段值等于-1则表示目前还没有线程在操作，此时调用acquire()方法检测的线程将获得KafkaConsumer的使用权。

● refcount：用于记录当前操作KafkaConsumer的线程数，初始值为0。在acquire()方法中若检测到当前线程具有对KafkaConsumer的使用权后，refcount值加1操作（incrementAndGet），即记录当前已有一个线程在使用KafkaConsumer。在release()方法中，若refcount减1操作（decrementAndGet）之后的值等于0，则将currentThread的值重置为-1，这样新的线程就可以请求使用KafkaConsumer了。

![image-20200524154856979](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524154856979.png)

### 消费订阅

KafkaConsumer提供了两种订阅消息的方法，一种是通过KafkaConsumer.subscribe()方法指定消息对应的主题，支持以正则表达式方式指定主题，另一种是通过KafkaConsumer.assign()方法指定需要消费的分区。第一种订阅方式由同一个消费组的Leader消费者根据各消费者都支持的分区分配策略为消费者分配分区。同时在订阅主题时可以指定一个ConsumerRebalancListener，在消费者发生平衡操作时回调处理。第二种订阅方式客户端直接指定了消费者与分区的对应关系。

### 消费消息

KafkaConsumer提供了一个poll()方法用于从服务端拉取消息，该方法通过Fetcher类来完成消息的拉取及更新消费偏移量，因此对KafkaConsumer消费消息的讲解，首先必须讲解Fetcher拉取消息的过程。

Fetcher主要功能是负责构造拉取消息的FetchRequest请求，然后通过ConsumerNetworkClient发送FetchRequest请求，最后对返回的结果进行处理并更新缓存中记录的消费位置。

![image-20200524155008000](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155008000.png)

### 心跳探测

KafkaConsumer启动后会定期向服务端组协调器GroupCoordinator发送心跳探测HeartbeatRequest请求，通过心跳探测通信双方相互感知对方是否存在并进行相应处理。实现心跳探测功能的核心组件为HeartbeatThread线程，在ConsumerCoordinator实例化时会创建一个守护线程HeartbeatThread，该线程通过计算当前时间与上一次发送心跳时间之差进行相应判断以决定是否要发送心跳探测请求。Kafka封装了一个Heartbeat类，该类定义了一些字段和方法用于HeartbeartThread线程进行心跳探测处理。对消费者心跳探测的分析主要是对该线程的run()方法的执行逻辑进行简要介绍，该方法主要是对以下几种情况进行检测，若满足某个检测条件则进行相应处理，然后结束本次心跳探测处理。

● 检测该消费者是否已找到GroupCoordinator，并已加入该组协调器管理之中，若检测到没有对应的GroupCoordinator，则先发送GroupCoordinatorRequest请求查找GroupCoordinator。

● 检测与GroupCoordinator之间的会话超时时间sessionTimeout是否已过期，若sessionTimeout已过期，说明HeartbeatRequest发送后迟迟未收到GroupCoordinator返回的响应，则认为GroupCoordinator不可达已处于“Dead”状态，就会调用coordinatorDead()方法进行处理，清空该GroupCoordinator对应的unsent队列，并将该消费者对应的GroupCoordinator设置为null，这样就会引起重新为该消费者分配一个GroupCoordinator的操作。

● 检查消费者距离上一次poll()操作时间间隔是否已超过最大空闲时间${max.pol l.interval.ms}，若超过该时间则认为该消费者已离开了该组协调器管理，则调用maybeLeaveGroup()方法进行处理，发送LeaveGroupRequest请求，并重置generation和memberId值，以准备进行消费者平衡操作。

● 检查消费者距离上一次poll()操作时间间隔是否已超过最大空闲时间${max.pol l.interval.ms}，若超过该时间则认为该消费者已离开了该组协调器管理，则调用maybeLeaveGroup()方法进行处理，发送LeaveGroupRequest请求，并重置generation和memberId值，以准备进行消费者平衡操作。

● 检测是否已达到发送心跳探测时间，若还未到发送心跳探测的时间则继续等待。否则表示要发送心跳探测，则先调用Heartbeat相关方法设置相应字段，为下一次心跳探测进行准备，然后发送HeartbeatRequest请求，并添加一个RequestFutureListener监听器。在监听器中对心跳探测成功与失败分别进行处理，若心跳探测成功则更新Heartbeat.lastHeartbeatReceive字段，若心跳探测失败即发送异常时，则视不同异常进行相应处理，若是RebalanceInProgressException异常则表示正在进行平衡操作，则依然更新Heartbeat.lastHeartbeatReceive字段，否则设置Heartbeat.heartbeatFailed字段为true，以标记心跳探测失败，同时唤醒被wait()处理的心跳探测线程。

### 分区数与消费者线程的关系

在介绍消费者线程总数与分区总数关系之前，首先简要介绍Kafka分配线程与分区的分配策略。Kafka提供了配置项partition.assignment.strategy用来设置消费者线程与分区映射关系，Kafka提供了range和round-robin两种分配策略，默认是range分配的策略。

**1.round-robin分配策略**

round-robin策略较简单，首先将订阅的主题分区以及消费者线程进行排序，然后通过轮询方式逐个将分区依次分给消费者线程。

**2.range分配策略**

range策略即按照线程总数与分区总数进行整除运算计算一个跨度，然后将分区按跨度进行平均分配，以保证分区尽可能均衡地分配给所有消费者线程。该策略具体实现逻辑如下：首先对线程集合按照字典顺序进行排序，然后通过分区总数与消费者线程总数进行整除运算计算每个线程平均分配的分区数numPartitionsPerConsumer，即一个平均跨度，通过分区总数与消费者线程总数取余计算平均之后多余的分区数consumersWithExtraPartition，最后遍历线程集合为每个线程分配分区，从起始分区开始分配，依次为每个线程分配num PartitionsPerConsumer个分区，如果consumers WithExtra Partition不为0，那么在迭代线程集合时，若迭代次数小于consumers WithExtra Partition对应的线程就会分配到num Partitions PerConsumer+1个分区。

### 消费者平衡过程

Kafka消费者平衡是指消费者重新加入消费组，并重新分配分区给消费者的过程。在以下几种情况下会引起消费者平衡操作。

● 新的消费者加入消费组。

● 当前消费者从消费组退出。这里的退出包括异常退出和消费者正常关闭。

● 消费者取消对某个主题的订阅。

● 订阅主题的分区增加。

● 代理宕机新的协调器当选。

● 当消费者在${session.timeout.ms}毫秒内还没发送心跳请求，组协调器认为消费者已退出。

##  Kafka基本操作实战

### KafkaServer管理

![image-20200524155307833](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155307833.png)

### 启动Kafka单个节点

Kafka提供了直接启动本地KafkaServer的执行脚本kafka-server-start.sh，该脚本在调用执行时需要传入server.properties文件路径，当然该文件名可以随意指定，在启动时KafkaServer读取并解析该文件中相关配置信息以完成KafkaServer的实例化。

![image-20200524155411574](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155411574.png)

###  启动Kafka集群

Kafka并没有提供同时启动集群中所有节点的执行脚本，在生产中一个Kafka集群往往会有多个节点，若逐个节点启动稍微有些麻烦，在这里自定义一个脚本用来启动集群中所有节点，脚本名为kafka-cluster-start.sh，内容如代码清单5-1所示。

![image-20200524155450316](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155450316.png)

下面简要介绍代码清单5-1所示的启动Kafka集群的脚本代码。

首先定义了变量brokers用来保存集群中各节点的机器域名，brokers="server-1 server-2server-3"表示机器名分别为server-1、server-2和server-3的3个节点（机器域名在/etc/host中已进行配置）。若增加或减少节点时只需修改brokers变量的值。

然后遍历brokers指定的代理列表取出每个节点，通过SSH方式登录该节点，执行${KAFKA_HOME}/bin/kafka-server-start.sh脚本，启动Kafka。为了保证该步骤执行成功，要确保已安装配置SSH。

![image-20200524155534625](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155534625.png)

### 主题管理

![image-20200524155602360](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524155602360.png)

### 创建主题

Kafka提供以下两种方式来创建一个主题。

（1）若代理设置了auto.create.topics.enable=true，该配置默认值为true，这样当生产者向一个还未创建的主题发送消息时，会自动创建一个拥有${num.partitions}个分区和${ default.replication.factor}个副本的主题。

（2）客户端通过执行kafka-topics.sh脚本创建一个主题。

### 删除主题

删除Kafka主题，一般有以下两种方式。

（1）手动删除各节点${log.dir}目录下该主题分区文件夹，同时登录ZooKeeper客户端删除待删除主题对应的节点，主题元数据保存在/brokers/topics和/config/topics目录下。

（2）执行kafka-topics.sh脚本进行删除，若希望通过该脚本彻底删除主题，则需要保证在启动Kafka时所加载的server.properties文件中配置delete.topic.enable=true，该配置默认为false。否则执行该脚本并未真正删除主题，而是在ZooKeeper的/admin/delete_topics目录下创建一个与待删除主题同名的节点，将该主题标记为删除状态。

### 查看主题

Kafka提供了list和describe两个命令方便查看主题信息，其中list参数列出Kafka所有的主题名，describe参数可以查看所有主题或某个特定主题的信息。

### 修改主题

当创建一个主题之后，可以通过alter命令对主题进行修改，包括修改主题级别的配置、增加主题分区、修改副本分配方案、修改主题Offset等。下面详细讲解如何通过Kafka的shell脚本对主题进行修改。

### 生产者基本操作

Kafka自带了一个在终端演示生产者发布消息的脚本kafka-console-producer.sh，熟练掌握该脚本用法，更能直观帮助我们理解生产者发送消息的过程。运行该脚本启动一个生产者进程，在运行该脚本时可以传递相应配置以覆盖默认配置，该脚本提供3个命令参数用于设置配置项的方式。

● 参数producer.config，用于加载一个生产者级别相关配置的配置文件，如producer.properties。

● 参数producer-property，通过该命令参数可以直接在启动生产者命令行中设置生产者级别的配置，在命令行中设置的参数将会覆盖所加载配置文件中的参数设置。

● 参数property，通过该命令可以设置消息消费者相关的配置。

### Kafka安全机制

在0.9版本之后，Kafka增加了身份认证与权限控制两种安全机制。

身份认证是指客户端与服务端连接进行身份认证，包括客户端与Kafka代理之间的连接认证、代理之间的连接认证、代理与ZooKeeper之间的连接认证。目前支持SSL、SASL/Kerberos、SASL/PLAIN这3种认证机制。

权限控制是指对客户端的读写操作进行权限控制，包括对于消息或Kafka集群操作权限控制。权限控制是可插拔的，并且支持与外部的授权服务进行集成，Kafka自带了简单的授权实现类SimpleAclAuthorizer，可以在server.properties文件中通过配置项authorizer.class.name指定，如设置authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer。Kafka权限类型如表5-11所示。

![image-20200524160134401](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200524160134401.png)

Kafka将权限控制列表ACL存储在ZooKeeper中，当添加权限控制之后，会在ZooKeeper中创建两个节点：即存储ACL信息的kafka-acl节点和存储ACL变更信息的kafka-acl-changes节点。

## Kafka API

Kafka提供了以下4类核心API。

● Producer API。Producer API提供生产消息相关的接口，我们可以通过实现ProducerAPI提供的接口来自定义Producer、自定义分区分配策略等。

● Consumer API。Consumer API提供消费消息相关接口，包括创建消费者、消费偏移量管理等。

● Streams API。Streams API是Kafka提供的一系列用来构建流处理程序的接口，通过Streams API让流处理相关的应用场景变得更加简单。

● Connect API。Kafka在0.9.0版本后提供了一种方便Kafka与外部系统进行数据流连接的连接器（Connect），实现将数据导入到Kafka或从Kafka中导出到外部系统。ConnectAPI提供了相关实现的接口，不过很多时候并不需要编码来实现Connect的功能，而只需要简单的几个配置就可以应用Kafka Connect与外部系统进行数据交互，因此我们对ConnectAPI不进行介绍。