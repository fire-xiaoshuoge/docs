## Apache HBase

### HBase

HBase是一个分布式的、面向列的开源数据库，该技术来源于 Fay Chang 所撰写的Google论文“Bigtable：一个结构化数据的[分布式存储系统](https://baike.baidu.com/item/分布式存储系统/6608875)”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。

### Region

Region就是一段数据的集合。HBase中的表一般拥有一个到多个Region。Region有以下特性：Region不能跨服务器，一个RegionServer上有一个或者多个Region。数据量小的时候，一个Region足以存储所有数据；但是，当数据量大的时候，HBase会拆分Region。当HBase在进行负载均衡的时候，也有可能会从一台RegionServer上把Region移动到另一台RegionServer上。Region是基于HDFS的，它的所有数据存取操作都是调用了HDFS的客户端接口来实现的。

### RegionServer

RegionServer就是存放Region的容器，直观上说就是服务器上的一个服务。一般来说，一个服务器只会安装一个RegionServer服务，不过你实在想在一个服务器上装多个RegionServer服务也不是不可以。当客户端从ZooKeeper获取RegionServer的地址后，它会直接从RegionServer获取数据。

### rowkey

rowkey和MySQL、Oracle中的主键比起来简单多了。这个rowkey完全是由用户指定的一串不重复的字符串，规则随你定！不过，话虽如此，你定的rowkey可是会直接决定这个row的存储位置的。HBase中无法根据某个column来排序，系统永远是根据rowkey来排序的。因此，rowkey就是决定row存储顺序的唯一凭证。

### 列族（column family）

建表的时候是不需要制定列的，因为列是可变的，它非常灵活，唯一需要确定的就是列族。这就是为什么说一个表有几个列族是一开始就定好的。此外，表的很多属性，比如过期时间、数据块缓存以及是否压缩等都是定义在列族上，而不是定义在表上或者列上。这一点做法跟以往的数据库有很大的区别。同一个表里的不同列族可以有完全不同的属性配置，但是同一个列族内的所有列都会有相同的属性，因为他们都在一个列族里面，而属性都是定义在列族上的。

### HBase的表结构

![image-20200226153703497](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226153703497.png)

### Put方法概览

![image-20200226161645891](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226161645891.png)

### Append方法概览

![image-20200226161707693](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226161707693.png)

### Increment方法概览

![image-20200226161730472](C:\Users\小硕哥\Desktop\重要！！\image-20200226161730472.png)

### Delete的其他方法

![image-20200226161809453](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226161809453.png)

### HBase的宏观架构

![image-20200226161935250](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226161935250.png)

### RegionServer的内部架构图

![image-20200226162030066](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226162030066.png)

一个WAL：预写日志，WAL是Write-Ahead Log的缩写。从名字就可以看出它的用途，就是：预先写入。当操作到达Region的时候，HBase先不管三七二十一把操作写到WAL里面去。HBase会先把数据放到基于内存实现的Memstore里，等数据达到一定的数量时才刷写（flush）到最终存储的HFile内。而如果在这个过程中服务器宕机或者断电了，那么数据就丢失了。WAL是一个保险机制，数据在写到Memstore之前，先被写到WAL了。这样当故障恢复的时候可以从WAL中恢复数据。多个Region：Region相当于一个数据分片。每一个Region都有起始rowkey和结束rowkey，代表了它所存储的row范围。

### Region内部的结构

![image-20200226162122116](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226162122116.png)

一个Region包含有：多个Store：每一个Region内都包含有多个Store实例。一个Store对应一个列族的数据，如果一个表有两个列族，那么在一个Region里面就有两个Store。在最右边的单个Store的解剖图上，我们可以看到Store内部有MemStore和HFile这两个组成部分。

### Store内部的结构

![image-20200226162235208](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226162235208.png)

在Store中有两个重要组成部分：MemStore：每个Store中有一个MemStore实例。数据写入WAL之后就会被放入MemStore。MemStore是内存的存储对象，只有当MemStore满了的时候才会将数据刷写（flush）到HFile中。HFile：在Store中有多个HFile。当MemStore满了之后HBase就会在HDFS上生成一个新的HFile，然后把MemStore中的内容写到这个HFile中。HFile直接跟HDFS打交道，它是数据的存储实体。

### MemStore

数据被写入WAL之后就会被加载到MemStore中去。MemStore的大小增加到超过一定阀值的时候就会被刷写到HDFS上，以HFile的形式被持久化起来。

设计MemStore的原因有以下几点。

（1）由于HDFS上的文件不可修改，为了让数据顺序存储从而提高读取效率，HBase使用了LSM树结构来存储数据。数据会先在Memstore中整理成LSM树，最后再刷写到HFile上。

（2）优化数据的存储。比如一个数据添加后就马上删除了，这样在刷写的时候就可以直接不把这个数据写到HDFS上。

### HFile

必须要提到的是HFile（StoreFile）。HFile是数据存储的实际载体，我们创建的所有表、列等数据都存储在HFile里面。HFile类似Hadoop的TFile类，它模仿了BigTable的SSTable格式。

![image-20200226162430138](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226162430138.png)

Data：数据块。每个HFile有多个Data块。我们存储在HBase表中的数据就在这里。Data块其实是可选的，但是几乎很难看到不包含Data块的HFile。

Meta：元数据块。Meta块是可选的，Meta块只有在文件关闭的时候才会写入。Meta块存储了该HFile文件的元数据信息，在v2之前布隆过滤器（Bloom Filter）的信息直接放在Meta里面存储，v2之后分离出来单独存储。

FileInfo：文件信息，其实也是一种数据存储块。FileInfo是HFile的必要组成部分，是必选的。它只有在文件关闭的时候写入，存储的是这个文件的信息，比如最后一个Key（Last Key），平均的Key长度（Avg KeyLen）等。

DataIndex：存储Data块索引信息的块文件。索引的信息其实也就是Data块的偏移值（offset）。DataIndex也是可选的，有Data块才有DataIndex。

MetaIndex：存储Meta块索引信息的块文件。MetaIndex块也是可选的，有Meta块才有MetaIndex。

Trailer：必选的，它存储了FileInfo、DataIndex、MetaIndex块的偏移值。

### 数据单元层次图

![image-20200226162556231](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226162556231.png)

### 均衡器

HBase作为一个分布式系统是一定会遇到负载均衡的问题的，所以HBase提供了一个均衡器用于自动均衡各个RegionServer之间的压力。均衡的手段就是移动Region到不同的RegionServer上用以平摊压力。

### 规整器

规整器用于规整Region的尺寸。

规整器会进行以下步骤

（1）获取该表的所有Region。

（2）计算出该表的Region平均大小。

（3）如果某个Region大于平均大小的2倍，则需要被拆分。

（4）不断合并最小的两个Region，只要最小的两个Region大小之和小于Region平均大小，这两个Region就会被合并。

（5）空Region（大小小于1MB）并不参与规整过程。

### 目录管理器

所谓的目录指的就是hbase:meta表中存储的Region信息。当HBase在拆分（Split）或者合并（merge）的时候，为了确保数据不丢失都会保留原来的Region信息。等拆分或者合并过程结束后，再使用目录管理器（catalog janitor）来清理这些旧的Region信息。

### 集群状态（ClusterStatus）的方法

getServersSize：获取所有活着的RegionServer的数量。

getServers：获取当前活着的RegionServer的ServerName实例，通过该实例可以获取地址、端口、启动码等信息，我们在前面的“Region管理”小节中已经使用过它了。

getDeadServers：获取所有不可用的RegionServer数量。

getDeadServerNames：获取所有不可用的RegionServer的ServerName实例。

getAverageLoad：获取当前的平均Region数，即Region总数/RegionServer总数。

getRegionsCount：获取当前在线的Region总数。

getRequestsCount：获取集群的请求数。

getHBaseVersion：获取当前HBase版本。

getMaster：获取Master的ServerName实例。

getBackupMastersSize：获取备份Master的数量。

getBackupMasters：获取备份Master。

getLoad：获取服务器负载信息。getClusterId：获取集群ID（Cluster Id）。

getMasterCoprocessors：获取Master上的协处理器列表。

getLastMajorCompactionTsForTable：获取该表的最后一次MajorCompaction的时间。

getLastMajorCompactionTsForRegion：获取该Region的最后一次Major Compaction的时间。

isBalancerOn：均衡器是否打开了。

### 服务器负载的方法

getNumberOfRequests：hbase-site.xml中的参数hbase.regionserver.msginterval可以定义RegionServer向Master发送报告的时间周期，默认值是3000，单位是毫秒，即3秒。如果按照默认配置，每3秒为一个周期。每个周期后API请求的统计信息会被清零。

getNumberOfRequests即为获取该周期内的请求数量。

hasNumberOfRequests：是否有监听周期内请求数。

getTotalNumberOfRequests：获取集群启动后的总请求数。

hasTotalNumberOfRequests：是否监听总请求数。

getReadRequestsCount：获取读请求数。

getFilteredReadRequestsCount：获取过滤后的读请求个数。

getWriteRequestsCount：获取写请求个数。

getUsedHeapMB：已使用的堆内存大小。

hasUsedHeapMB：是否有监听已使用的堆内存大小。

getMaxHeapMB：最大允许的堆内存大小。

hasMaxHeapMB：是否有监听最大允许的堆内存大小。

getStores：获取该RegionServer上的Store数量。

getStorefiles：获取该RegionServer上的StoreFile数量。

getStoreUncompressedSizeMB：获取未压缩的Store大小。

getStorefileSizeInMB：获取StoreFile的文件大小。

getMemstoreSizeInMB：获取Memstore的大小。

getStorefileIndexSizeInMB：获取StoreFile索引的大小。

getRootIndexSizeKB：获取根索引的大小。

getTotalStaticIndexSizeKB：获取静态索引的总大小。

### Admin接口的方法

Admin接口的方法大概可以分为以下几类：

表管理

列族管理

Region管理

快照管理

维护工具管理

其他管理

### 朱丽叶暂停

随着内存的加大，有一个不容忽视的问题也出现了，那就是JVM的堆内存越大，Full GC的时间越久。Full GC有时候可以达到好几分钟。在Full GC的时候JVM会停止响应任何的请求，整个JVM的世界就像是停止了一样，所以这种暂停又被叫做Stop-The-World（STW）。当ZooKeeper像往常一样通过心跳来检测RegionServer节点是否存活的时候，发现已经很久没有接收到来自RegionServer的回应，会直接把这个RegionServer标记为已经宕机。等到这台RegionServer终于结束了FullGC后，去查看ZooKeeper的时候会发现原来自己已经“被宕机”了，为了防止脑裂问题的发生，它会自己停止自己。这种场景称为RegionServer自杀，它还有另一个美丽的名字叫朱丽叶暂停，而且这问题还挺常见的，早期一直困扰着HBase开发人员。所以我们一定要设定好GC回收策略，避免长时间的Full GC发生，或者是尽量减小Full GC的时间。

### Master和RegionServer的JVM调优

1 先调大堆内存

比如你现在有一台16GB的机器，上面有MapReduce服务、RegionServer和DataNode（这三位一般都是装在一起的），那么建议按照如下配置设置内存：2GB：留给系统进程。8GB：MapReduce服务。平均每1GB分配6个Map slots + 2个Reduceslots。4GB：HBase的RegionServer服务。1GB：TaskTracker。1GB：DataNode。

2 GC回收策略优化

ParallelGC和CMS的组合方案

G1GC方案

3 Memstore的专属JVM策略MSLAB

TLAB（Thread-Local allocation buffer）。当你使用TLAB的时候，每一个线程都会分配一个固定大小的内存空间，专门给这个线程使用，当线程用完这个空间后再新申请的空间还是这么大，这样下来就不会出现特别小的碎片空间，基本所有的对象都可以有地方放。缺点就是无论你的线程里面有没有对象都需要占用这么大的内存，其中有很大一部分空间是闲置的，内存空间利用率会降低。不过能避免Full GC，这些都是值得的。

HBase就自己实现了一套以Memstore为最小单元的内存管理机制，称为MSLAB（Memstore-Local Allocation Buffers）。这套机制完全沿袭了TLAB的实现思路，只不过内存空间是由Memstore来分配的。

MSLAB的具体实现如下：引入chunk的概念，所谓的chunk就是一块内存，大小默认为2MB。RegionServer中维护着一个全局的MemStoreChunkPool实例，从名字很容看出，是一个chunk池。每个MemStore实例里面有一个MemStoreLAB实例。当MemStore接收到KeyValue数据的时候先从ChunkPool中申请一个chunk，然后放到这个chunk里面。如果这个chunk放满了，就新申请一个chunk。如果MemStore因为刷写而释放内存，则按chunk来清空内存。由此可以看出堆内存被chunk区分为规则的空间，这样就消除了小碎片引起的无法插入数据问题，但是会降低内存利用率，因为就算你的chunk里面只放1KB的数据，这个chunk也要占2MB的大小。不过，为了不发生Full GC，这些都可以忍。

跟MSLAB相关的参数是：hbase.hregion.memstore.mslab.enabled：设置为true，即打开MSLAB，默认为true。hbase.hregion.memstore.mslab.chunksize：每个chunk的大小，默认为2048 * 1024 即2MB。hbase.hregion.memstore.mslab.max.allocation：能放入chunk的最大单元格大小，默认为256KB，已经很大了。hbase.hregion.memstore.chunkpool.maxsize：在整个memstore可以占用的堆内存中，chunkPool占用的比例。该值为一个百分比，取值范围为0.0~1.0。默认值为0.0。hbase.hregion.memstore.chunkpool.initialsize：在RegionServer启动的时候可以预分配一些空的chunk出来放到chunkPool里面待使用。该值就代表了预分配的chunk占总的chunkPool的比例。该值为一个百分比，取值范围为0.0~1.0，默认值为0.0。

### Region的拆分

ConstantSizeRegionSplitPolicy

早在0.94版本的时候HBase只有一种拆分策略（什么？你用的就是0.94？！赶快升级）。这个策略非常简单，从名字上就可以看出这个策略就是按照固定大小来拆分Region。

IncreasingToUpperBoundRegionSplitPolicy（默认）

这种策略从名字上就可以看出是限制不断增长的文件尺寸的策略。我们以前使用传统关系型数据库的时候或许有这样的经验，有的数据库的文件增长是翻倍增长的，比如第一个文件是64MB，第二个就是 128MB，第三个就是256MB。这种策略就是模仿此类情况来实现的。

KeyPrefixRegionSplitPolicy

KeyPrefixRegionSplitPolicy 是IncreasingToUpperBoundRegionSplitPolicy的子类，在前者的基础上增加了对拆分点（splitPoint，拆分点就是Region被拆分处的rowkey）的定义。它保证了有相同前缀的rowkey不会被拆分到两个不同的Region里面。

DelimitedKeyPrefixRegionSplitPolicy

该策略也是继承自IncreasingToUpperBoundRegionSplitPolicy，它也是根据你的rowkey前缀来进行切分的。唯一的不同就是：KeyPrefixRegionSplitPolicy是根据rowkey的固定前几位字符来进行判断，而DelimitedKeyPrefixRegionSplitPolicy是根据分隔符来判断的。

BusyRegionSplitPolicy

此前的拆分策略都没有考虑热点问题。所谓热点问题就是数据库中的Region被访问的频率并不一样，某些Region在短时间内被访问的很频繁，承载了很大的压力，这些Region就是热点Region。BusyRegionSplitPolicy就是为了解决这种场景而产生的。

DisabledRegionSplitPolicy

这种策略其实不是一种策略。如果你看这个策略的源码会发现就一个方法shouldSplit，并且永远返回false。聪明的你一定一下就猜到了，设置成这种策略就是Region永不自动拆分。

### Region的合并

Region可以被拆分，也可以被合并。不过Region的合并（merge）并不是为了性能考虑的，而更多地是出于维护的目的被创造出来的。啥时候才会用到合并呢?

比如你删了大量的数据，每个Region都变小了，这个时候分成这么多个Region就有点浪费了，可以把Region合并起来，然后可以减少一些RegionServer服务器来节省成本。

通过Merge类合并Region

hbase shell提供了一个命令叫online_merge，通过这个方法可以进行热合并（online_merge）。

### BlockCache的优化

BlockCache的工作原理就跟你们猜想的一样了：读请求到HBase之后先尝试查询BlockCache，如果获取不到就去HFile（StoreFile）和Memstore中去获取。如果获取到了则在返回数据的同时把Block块缓存到BlockCache中。BlockCache默认是开启的

LRUBlock Cache

![image-20200226165459786](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226165459786.png)

完全基于JVM Heap的缓存，势必带来一个后果：随着内存中对象越来越多，每隔一段时间都会引发一次Full GC。凡是做了几年Java的人听到FullGC都会浑身一颤。在Full GC的过程中，整个JVM完全处于停滞状态，有的时候长达几分钟。

SlabCache

既然LRUBlockCache会引发Full GC，那我们就要尝试一下堆外内存的解决方案了。SlabCache就是对堆外内存尝试的方案。

Bucket Cache

BucketCache借鉴了SlabCache的创意，也用上了堆外内存。不过它是这么用的：相比起只有2个区域的SlabeCache，BucketCache一上来就分配了14种区域。注意：我这里说的是14种区域，并不是14块区域。这14种区域分别放的是大小为4KB、8KB、16KB、32KB、40KB、48KB、56KB、64KB、96KB、128KB、192KB、256KB、384KB、512KB的Block。而且这个种类列表还是可以手动通过设置hbase.bucketcache.bucket.sizes属性来定义（种类之间用逗号分隔，想配几个配几个，不一定是14个！），这14种类型可以分配出很多个Bucket（我是可以翻译成桶， 但是听起来太怪了，还是用Bucket吧）。BucketCache的存储不一定要使用堆外内存，是可以自由在3种存储介质直接选择：堆（heap）、堆外（offheap）、文件（file）。通过设置hbase.bucketcache.ioengine为heap、offfheap或者file来配置。每个Bucket的大小上限为最大尺寸的block * 4，比如可以容纳的最大的Block类型是512KB，那么每个Bucket的大小就是512KB * 4 =2048KB。系统一启动BucketCache就会把可用的存储空间按照每个Bucket的大小上限均分为多个Bucket。如果划分完的数量比你的种类还少，比如比14（默认的种类数量）少，就会直接报错，因为每一种类型的Bucket至少要有一个Bucket。

### HFile的合并策略

除了Region会合并（merge）和拆分（split），在Region中的单个Store中也会发生合并（compaction），不过这个合并的英文单词跟Region的合并不太一样，是compaction。首先我们要知道HFile（StoreFile是HFile的Java抽象对象）是会经常被合并和拆分的。为什么要合并？每次memstore的刷写都会产生一个新的HFile，而HFile毕竟是存储在硬盘上的东西，凡是读取存储在硬盘上的东西都涉及一个操作：寻址，如果是传统硬盘那就是磁头的移动寻址，这是一个很慢的动作。当HFile一多，你每次读取数据的时候寻址的动作就多了，效率就低了。所以为了防止寻址的动作过多，我们要适当地减少碎片文件，所以需要继续合并操作。

1 Minor Compaction和Major Compaction

Minor Compaction：将Store中多个HFile合并为一个HFile。在这个过程中达到TTL的数据会被移除，但是被手动删除的数据不会被移除。这种合并触发频率较高。

Major Compaction：合并Store中的所有HFile为一个HFile。在这个过程中被手动删除的数据会被真正地移除。同时被删除的还有单元格内超过MaxVersions的版本数据。这种合并触发频率较低，默认为7天一次。不过由于Major Compaction消耗的性能较大，你不会想让它发生在业务高峰期，建议手动控制Major Compaction的时机。

2 0.96之前的合并策略

0.96版本之前的合并策略为RatioBasedCompactionPolicy

3 0.96之后的合并策略

0.96版本之后提出了ExploringCompactionPolicy算法，并且把该算法作为了默认算法。该算法有以下的更新：修改待合并文件挑选条件不再武断地认为，某个文件满足条件就把更新的文件全部合并进去。确切地说，现在的遍历不强调顺序性了，是把所有的文件都遍历一遍之后每一个文件都去考虑。

4 FIFOCompactionPolicy

这个合并算法其实是最简单的合并算法。严格地说它都不算是一种合并算法，是一种删除策略。在HBase中Minor Compaction和MajorCompaction是一定会发生的，只是策略和频率不同而已。

5 DateTieredCompactionPolicy

DateTieredCompactionPolicy解决的是一个基本的问题：最新的数据最有可能被读到.

6 StripeCompactionPolicy

这个策略最早是借鉴自levelDB的compaction策略。levelDB的策略简单地说就是把一个Store里面的数据分为很多层。数据从Memstore刷写到HFile上后先落在level 0，然后随着时间的推移，当level 0大小超过一定的阀值的时候就会引发一次合并。

