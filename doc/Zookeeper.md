## Apache Zookeeper

### Zookeeper

ZooKeeper是一个开放源码的分布式协调服务，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

分布式应用程序可以基于Zookeeper实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master选举、分布式锁和分布式队列等功能。

Zookeeper保证了如下分布式一致性特性：

- 顺序一致性
- 原子性
- 单一视图
- 可靠性
- 实时性（最终一致性）

### ZooKeeper架构

| 部分             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| Client（客户端） | 客户端，我们的分布式应用集群中的一个节点，从服务器访问信息。对于特定的时间间隔，每个客户端向服务器发送消息以使服务器知道客户端是活跃的。类似地，当客户端连接时，服务器发送确认码。如果连接的服务器没有响应，客户端会自动将消息重定向到另一个服务器。 |
| Server（服务器） | 服务器，我们的ZooKeeper总体中的一个节点，为客户端提供所有的服务。向客户端发送确认码以告知服务器是活跃的。 |
| Ensemble         | ZooKeeper服务器组。形成ensemble所需的最小节点数为3。         |
| Leader           | 服务器节点，如果任何连接的节点失败，则执行自动恢复。Leader在服务启动时被选举。 |
| Follower         | 跟随leader指令的服务器节点。                                 |

### 层次命名空间

ZooKeeper节点称为 **znode** 。每个znode由一个名称标识，并用路径(/)序列分隔。

- 在图中，首先有一个由“/”分隔的znode。在根目录下，你有两个逻辑命名空间 **config** 和 **workers** 。
- **config** 命名空间用于集中式配置管理，**workers** 命名空间用于命名。
- 在 **config** 命名空间下，每个znode最多可存储1MB的数据。这与UNIX文件系统相类似，除了父znode也可以存储数据。这种结构的主要目的是存储同步数据并描述znode的元数据。此结构称为 **ZooKeeper数据模型**。

![分层命名空间](https://atts.w3cschool.cn/attachments/day_161229/201612291345162031.jpg)



ZooKeeper数据模型中的每个znode都维护着一个 **stat** 结构。一个stat仅提供一个znode的**元数据**。它由版本号，操作控制列表(ACL)，时间戳和数据长度组成。

- **版本号** - 每个znode都有版本号，这意味着每当与znode相关联的数据发生变化时，其对应的版本号也会增加。当多个zookeeper客户端尝试在同一znode上执行操作时，版本号的使用就很重要。
- **操作控制列表(ACL)** - ACL基本上是访问znode的认证机制。它管理所有znode读取和写入操作。
- **时间戳** - 时间戳表示创建和修改znode所经过的时间。它通常以毫秒为单位。ZooKeeper从“事务ID"(zxid)标识znode的每个更改。**Zxid** 是唯一的，并且为每个事务保留时间，以便你可以轻松地确定从一个请求到另一个请求所经过的时间。
- **数据长度** - 存储在znode中的数据总量是数据长度。你最多可以存储1MB的数据。

### Znode的类型

Znode被分为持久（persistent）节点，顺序（sequential）节点和临时（ephemeral）节点。

- **持久节点** - 即使在创建该特定znode的客户端断开连接后，持久节点仍然存在。默认情况下，除非另有说明，否则所有znode都是持久的。
- **临时节点** - 客户端活跃时，临时节点就是有效的。当客户端与ZooKeeper集合断开连接时，临时节点会自动删除。因此，只有临时节点不允许有子节点。如果临时节点被删除，则下一个合适的节点将填充其位置。临时节点在leader选举中起着重要作用。
- **顺序节点** - 顺序节点可以是持久的或临时的。当一个新的znode被创建为一个顺序节点时，ZooKeeper通过将10位的序列号附加到原始名称来设置znode的路径。例如，如果将具有路径 **/myapp** 的znode创建为顺序节点，则ZooKeeper会将路径更改为 **/myapp0000000001** ，并将下一个序列号设置为0000000002。如果两个顺序节点是同时创建的，那么ZooKeeper不会对每个znode使用相同的数字。顺序节点在锁定和同步中起重要作用。

### Sessions（会话）

会话对于ZooKeeper的操作非常重要。会话中的请求按FIFO顺序执行。一旦客户端连接到服务器，将建立会话并向客户端分配**会话ID** 。

客户端以特定的时间间隔发送**心跳**以保持会话有效。如果ZooKeeper集合在超过服务器开启时指定的期间（会话超时）都没有从客户端接收到心跳，则它会判定客户端死机。

会话超时通常以毫秒为单位。当会话由于任何原因结束时，在该会话期间创建的临时节点也会被删除。

### Watches（监视） 

监视是一种简单的机制，使客户端收到关于ZooKeeper集合中的更改的通知。客户端可以在读取特定znode时设置Watches。Watches会向注册的客户端发送任何znode（客户端注册表）更改的通知。

Znode更改是与znode相关的数据的修改或znode的子项中的更改。只触发一次watches。如果客户端想要再次通知，则必须通过另一个读取操作来完成。当连接会话过期时，客户端将与服务器断开连接，相关的watches也将被删除。

### Zookeeper 工作流

一旦ZooKeeper集合启动，它将等待客户端连接。客户端将连接到ZooKeeper集合中的一个节点。它可以是leader或follower节点。一旦客户端被连接，节点将向特定客户端分配会话ID并向该客户端发送确认。如果客户端没有收到确认，它将尝试连接ZooKeeper集合中的另一个节点。 一旦连接到节点，客户端将以有规律的间隔向节点发送心跳，以确保连接不会丢失。

- **如果客户端想要读取特定的znode，**它将会向具有znode路径的节点发送**读取请求**，并且节点通过从其自己的数据库获取来返回所请求的znode。为此，在ZooKeeper集合中读取速度很快。
- **如果客户端想要将数据存储在ZooKeeper集合中**，则会将znode路径和数据发送到服务器。连接的服务器将该请求转发给leader，然后leader将向所有的follower重新发出写入请求。如果只有大部分节点成功响应，而写入请求成功，则成功返回代码将被发送到客户端。 否则，写入请求失败。绝大多数节点被称为 **Quorum**

### ZAB协议

ZAB协议是为分布式协调服务Zookeeper专门设计的一种支持崩溃恢复的原子广播协议。

ZAB协议包括两种基本的模式：崩溃恢复和消息广播。

当整个zookeeper集群刚刚启动或者Leader服务器宕机、重启或者网络故障导致不存在过半的服务器与Leader服务器保持正常通信时，所有进程（服务器）进入崩溃恢复模式，首先选举产生新的Leader服务器，然后集群中Follower服务器开始与新的Leader服务器进行数据同步，当集群中超过半数机器与该Leader服务器完成数据同步之后，退出恢复模式进入消息广播模式，Leader服务器开始接收客户端的事务请求生成事物提案来进行事务请求处理。

### 四种类型的数据节点 

- PERSISTENT-持久节点
  除非手动删除，否则节点一直存在于Zookeeper上
- EPHEMERAL-临时节点
  临时节点的生命周期与客户端会话绑定，一旦客户端会话失效（客户端与zookeeper连接断开不一定会话失效），那么这个客户端创建的所有临时节点都会被移除。
- PERSISTENT_SEQUENTIAL-持久顺序节点
  基本特性同持久节点，只是增加了顺序属性，节点名后边会追加一个由父节点维护的自增整型数字。
- EPHEMERAL_SEQUENTIAL-临时顺序节点
  基本特性同临时节点，增加了顺序属性，节点名后边会追加一个由父节点维护的自增整型数字。

### Server工作状态

服务器具有四种状态，分别是LOOKING、FOLLOWING、LEADING、OBSERVING。

- LOOKING：寻找Leader状态。当服务器处于该状态时，它会认为当前集群中没有Leader，因此需要进入Leader选举状态。
- FOLLOWING：跟随者状态。表明当前服务器角色是Follower。
- LEADING：领导者状态。表明当前服务器角色是Leader。
- OBSERVING：观察者状态。表明当前服务器角色是Observer。

### Leader 选举

Leader选举是保证分布式数据一致性的关键所在。当Zookeeper集群中的一台服务器出现以下两种情况之一时，需要进入Leader选举。

　　(1) 服务器初始化启动。

　　(2) 服务器运行期间无法和Leader保持连接。

　　下面就两种情况进行分析讲解。

     　　1. 服务器启动时期的Leader选举

　　若进行Leader选举，则至少需要两台机器，这里选取3台机器组成的服务器集群为例。在集群初始化阶段，当有一台服务器Server1启动时，其单独无法进行和完成Leader选举，当第二台服务器Server2启动时，此时两台机器可以相互通信，每台机器都试图找到Leader，于是进入Leader选举过程。选举过程如下

　　(1) 每个Server发出一个投票。由于是初始情况，Server1和Server2都会将自己作为Leader服务器来进行投票，每次投票会包含所推举的服务器的myid和ZXID，使用(myid, ZXID)来表示，此时Server1的投票为(1, 0)，Server2的投票为(2, 0)，然后各自将这个投票发给集群中其他机器。

　　(2) 接受来自各个服务器的投票。集群的每个服务器收到投票后，首先判断该投票的有效性，如检查是否是本轮投票、是否来自LOOKING状态的服务器。

　　(3) 处理投票。针对每一个投票，服务器都需要将别人的投票和自己的投票进行PK，PK规则如下

　　　　· 优先检查ZXID。ZXID比较大的服务器优先作为Leader。

　　　　· 如果ZXID相同，那么就比较myid。myid较大的服务器作为Leader服务器。

　　对于Server1而言，它的投票是(1, 0)，接收Server2的投票为(2, 0)，首先会比较两者的ZXID，均为0，再比较myid，此时Server2的myid最大，于是更新自己的投票为(2, 0)，然后重新投票，对于Server2而言，其无须更新自己的投票，只是再次向集群中所有机器发出上一次投票信息即可。

　　(4) 统计投票。每次投票后，服务器都会统计投票信息，判断是否已经有过半机器接受到相同的投票信息，对于Server1、Server2而言，都统计出集群中已经有两台机器接受了(2, 0)的投票信息，此时便认为已经选出了Leader。

　　(5) 改变服务器状态。一旦确定了Leader，每个服务器就会更新自己的状态，如果是Follower，那么就变更为FOLLOWING，如果是Leader，就变更为LEADING。

     　　2. 服务器运行时期的Leader选举

　　在Zookeeper运行期间，Leader与非Leader服务器各司其职，即便当有非Leader服务器宕机或新加入，此时也不会影响Leader，但是一旦Leader服务器挂了，那么整个集群将暂停对外服务，进入新一轮Leader选举，其过程和启动时期的Leader选举过程基本一致。假设正在运行的有Server1、Server2、Server3三台服务器，当前Leader是Server2，若某一时刻Leader挂了，此时便开始Leader选举。选举过程如下

　　(1) 变更状态。Leader挂后，余下的非Observer服务器都会讲自己的服务器状态变更为LOOKING，然后开始进入Leader选举过程。

　　(2) 每个Server会发出一个投票。在运行期间，每个服务器上的ZXID可能不同，此时假定Server1的ZXID为123，Server3的ZXID为122；在第一轮投票中，Server1和Server3都会投自己，产生投票(1, 123)，(3, 122)，然后各自将投票发送给集群中所有机器。

　　(3) 接收来自各个服务器的投票。与启动时过程相同。

　　(4) 处理投票。与启动时过程相同，此时，Server1将会成为Leader。

　　(5) 统计投票。与启动时过程相同。

　　(6) 改变服务器的状态。与启动时过程相同。

### 保证事务的顺序一致性

zookeeper采用了全局递增的事务Id来标识，所有的proposal（提议）都在被提出的时候加上了zxid，zxid实际上是一个64位的数字，高32位是epoch（时期; 纪元; 世; 新时代）用来标识leader周期，如果有新的leader产生出来，epoch会自增，低32位用来递增计数。当新产生proposal的时候，会依据数据库的两阶段过程，首先会向其他的server发出事务执行请求，如果超过半数的机器都能执行并且能够成功，那么就会开始执行。

### zk节点宕机

Zookeeper本身也是集群，推荐配置不少于3个服务器。Zookeeper自身也要保证当一个节点宕机时，其他节点会继续提供服务。
如果是一个Follower宕机，还有2台服务器提供访问，因为Zookeeper上的数据是有多个副本的，数据并不会丢失；
如果是一个Leader宕机，Zookeeper会选举出新的Leader。
ZK集群的机制是只要超过半数的节点正常，集群就能正常提供服务。只有在ZK节点挂得太多，只剩一半或不到一半节点能工作，集群才失效。
所以
3个节点的cluster可以挂掉1个节点(leader可以得到2票>1.5)
2个节点的cluster就不能挂掉任何1个节点了(leader可以得到1票<=1)

### **分布式锁**

1.保持独占锁

在业务逻辑之前，每次都去尝试创建一个临时节点，谁创建到了这个临时节点，谁就拿到了锁，进行相应的业务逻辑操作，在逻辑操作结束之后对节点进行销毁也就是释放锁即可’

2.顺序执行锁

在业务逻辑之前，都在一个已经存在的节点下面创建一个临时顺序节点，保证每次都是顺序编号最小的节点进行业务逻辑操作，当逻辑操作结束后，删除该节点，然后继续是顺序编号最小的节点进行业务逻辑操作，同样可以保证每次都是只有一个节点也就是一个线程在进行逻辑操作

### **数据复制**

Zookeeper作为一个集群提供一致的数据服务，自然，它要在所有机器间做数据复制。数据复制的好处：

1、容错：一个节点出错，不致于让整个系统停止工作，别的节点可以接管它的工作；

2、提高系统的扩展能力 ：把负载分布到多个节点上，或者增加节点来提高系统的负载能力；

3、提高性能：让客户端本地访问就近的节点，提高用户访问速度。

1、写主(WriteMaster) ：对数据的修改提交给指定的节点。读无此限制，可以读取任何一个节点。这种情况下客户端需要对读与写进行区别，俗称读写分离；

2、写任意(Write Any)：对数据的修改可提交给任意的节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明。

### ZooKeeper分布式锁的流程

1 在zookeeper指定节点（locks）下创建临时顺序节点node_n

2 获取locks下所有子节点children

3 对子节点按节点自增序号从小到大排序

4 判断本节点是不是第一个子节点，若是，则获取锁；若不是，则监听比该节点小的那个节点的删除事件

5 若监听事件生效，则回到第二步重新进行判断，直到获取到锁代码部分
整个代码结构如图：


1、首先创建一个接口Lock，顾名思义，大家很容易想到JDK下面有一个Lock的接口，但是这里我并不打算直接使用这个接口，只是模拟了该接口里的方法自定义实现，这样可以按需使用，请看里面的代码，

```
public interface Lock {
	public void getLock();
	public void unlock();
接口里面比较简单，一个是获取锁的接口，一个是释放锁的接口，
```

2、再来看AbstratcLock这个类，这是个抽象类，里面有一个获取锁的方法和两个待实现的抽象方法，

//定义基本模板
public abstract class AbstratcLock implements Lock{

	public void getLock() {
		if(tryLock()){
			System.out.println("##获取锁的资源===============");
		}else{
			waitLock();
			getLock();
		}
	}
	public abstract boolean tryLock();
	public abstract void waitLock();
	}

3、zookeeper的基本配置项在ZookeeperAbstractLock这个类里面，在这里，我没有使用单独的配置文件或者类去配置连接zookeeper的配置信息，就在这个抽象类里面全部搞定，

public abstract class ZookeeperAbstractLock extends AbstratcLock{
	

	private static String CONNECT_PATH = "127.0.0.1:2181";
	
	protected ZkClient zkClient = new ZkClient(CONNECT_PATH);
	
	protected static final String PATH = "/lock";
	
	protected static final String PATH2 = "/lock2";
	4、接下来是zookeeper实现具体的分布式锁的业务逻辑部分，实现的思路前面已经解释过，再次总结一下就是，zookeeper基于内存实现的一种文件树节点，一旦某个线程成功创建了某个节点，其他线程继续创建同名节点就无法成功，但该线程可以注册一个监听器，监听上一个线程对该节点的变换情况，通过这个机制来判定对该节点的锁的持有和释放，从而实现效果，下面通过两种方式来实现，

4.1 直接通过一个节点实现分布式锁，代码如下，
1
public class ZookeeperDistributeLock extends ZookeeperAbstractLock{

	private CountDownLatch countDownLatch = null;
	
	//尝试获得锁
	@Override
	public boolean tryLock() {
		try {
			zkClient.createEphemeral(PATH);
			return true;
		} catch (Exception e) {
			return false;
		}
	}
	
	//监听某个节点,匿名回调函数实现对节点信息变化的监听,
	@Override
	public void waitLock() {
		
		//一旦zookeeper检测到节点信息的变化，就会触发匿名匿名回调函数，通知订阅的客户端，即zkClient
		IZkDataListener iZkDataListener = new IZkDataListener() {
			
			public void handleDataDeleted(String path) throws Exception {
				//唤醒被等待的线程
				if(countDownLatch != null){
					countDownLatch.countDown();
				}
			}
			
			public void handleDataChange(String path, Object data) throws Exception {
				
			}
		};
		//注册事件监听
		zkClient.subscribeDataChanges(PATH, iZkDataListener);
		
		//如果节点存在了，则需要等待一直到接收到了事件通知
		if(zkClient.exists(PATH)){
			countDownLatch = new CountDownLatch(1);
			try {
				countDownLatch.await();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		zkClient.unsubscribeDataChanges(PATH, iZkDataListener);
	}
	
	//释放锁
	public void unlock() {
		if(zkClient != null){
			zkClient.delete(PATH);
			zkClient.close();
			System.out.println("释放锁资源");
		}
	}
	}
	5、下面是测试类，模拟50个线程并发生成订单的动作，OrderService该类，代码如下，
		
	public class OrderService implements Runnable{
	
	//订单号生成类
	private OrderNumGenerator orderNumGenerator = new OrderNumGenerator();
	
	private Lock lock = new ZookeeperDistributeLock();
	//private Lock lock = new ZookeeperDistributeLock2();
	
	public void run() {
		getNumber();
	}
	
	public void getNumber(){
		try {
			lock.getLock();
			String number = orderNumGenerator.getNumber();
			System.out.println(Thread.currentThread().getName() + ",产生了订单:" + number);
		} catch (Exception e) {
			e.printStackTrace();
		}finally{
			lock.unlock();
		}
	}
	
	public static void main(String[] args) {
		System.out.println("##生成了订单####");
		for(int i=0;i<50;i++){
			new Thread(new OrderService()).start();
		}
	}

将生成订单的类的代码也附上，

	public class OrderNumGenerator {
	
	public static int count =0;
	private Lock lock = new ReentrantLock();
	
	public String getNumber(){
		try {
			lock.lock();
			SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd-HH-mm-ss");
			return sdf.format(new Date()) + "_" + ++count;
		} finally {
			lock.unlock();
		}
	}


下面运行上述的OrderService的main函数，看控制台输出结果，


上述50个线程很快就执行完毕了，而且没有出现任何问题，我们知道zookeeper是基于内存型的分布式协调服务器，大家有没有发现，我这里只创建了一个文件节点，就可以实现效果，这主要是得益于内存操作的快速，但是问题来了，如果线程数量足够多，等于是某个线程释放了锁，其他的线程一起去争夺锁，那样，不管内存的执行速度有多快，总会有并发争夺的时候出现，所以下面演示用zookeeper的临时有序节点的方式实现，

6、实现的步骤上面已经说明，这里再说一下，主要步骤是：

建立一个节点，这里为：lock2 。节点类型为持久节点（PERSISTENT）
每当进程需要访问共享资源时，会调用分布式锁的lock()或tryLock()方法获得锁，这个时候会在第一步创建的lock节点下建立相应的顺序子节点，节点类型为临时顺序节点（EPHEMERAL_SEQUENTIAL），由于在这样的节点模式下，继续创建同名节点，会直接在该节点下生成一个有序的临时子节点编号，从小到大，依次排序；
在建立子节点后，对lock下面的所有以name开头的子节点进行排序，判断刚刚建立的子节点顺序号是否是最小的节点，假如是最小节点，则获得该锁对资源进行访问。
假如不是该节点，就获得该节点的上一顺序节点，并给该节点是否存在注册监听事件。同时在这里阻塞。等待监听事件的发生，获得锁控制权。
当调用完共享资源后，调用unlock（）方法，关闭zk，进而可以引发监听事件，释放该锁。
代码如下，

	public class ZookeeperDistributeLock2 extends ZookeeperAbstractLock{
	
	private CountDownLatch countDownLatch = null;
	
	private String beforePath;		//前一个节点
	private String currentPath;		//当前节点
	
	//初始化主节点，如果不存在则创建
	public ZookeeperDistributeLock2(){
		if(!this.zkClient.exists(PATH2)){
			this.zkClient.createPersistent(PATH2);
		}
	}
	
	@Override
	public boolean tryLock() {
		//基于lock2节点，新建一个临时节点
		if(currentPath == null || currentPath.length() <= 0){
			currentPath = this.zkClient.createEphemeralSequential(PATH2 + "/", beforePath);
		}
		//获取所有临时节点并进行排序
		List<String> children = this.zkClient.getChildren(PATH2);
		Collections.sort(children);
		
		if(currentPath.equals(PATH2 + "/" + children.get(0))){
			return true;
		}else{
			//如果当前节点在节点列表中不是排第一的位置，则获取当前节点前面的节点，并赋值
			int wz = Collections.binarySearch(children, currentPath.substring(7));
			beforePath = PATH2 + "/" + children.get(wz-1);
		}
		return false;
	}
	
	@Override
	public void waitLock() {	//等待锁
		IZkDataListener iZkDataListener = new IZkDataListener() {
			
			public void handleDataDeleted(String dataPath) throws Exception {
				//唤醒被等待的线程
				if(countDownLatch != null){
					countDownLatch.countDown();
				}
			}
			public void handleDataChange(String path, Object data) throws Exception {
				
			}
		};
		//注册事件，对前一个节点进行监听
		zkClient.subscribeDataChanges(beforePath, iZkDataListener);
		
		//如果节点存在了，则需要等待一直到接收到事件通知
		if(zkClient.exists(beforePath)){
			countDownLatch = new CountDownLatch(1);
			try {
				countDownLatch.await();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		zkClient.unsubscribeDataChanges(beforePath, iZkDataListener);
		
	}
	
	//释放锁
	public void unlock() {
		zkClient.delete(currentPath);
		zkClient.close();
	}


运行main函数，


	public class OrderService implements Runnable{
	//订单号生成类
	private OrderNumGenerator orderNumGenerator = new OrderNumGenerator();
	
	//方式1实现
	//private Lock lock = new ZookeeperDistributeLock();
	
	//方式2实现
	private Lock lock = new ZookeeperDistributeLock2();
	
	public void run() {
		getNumber();
	}
	
	public void getNumber(){
		try {
			lock.getLock();
			String number = orderNumGenerator.getNumber();
			System.out.println(Thread.currentThread().getName() + ",产生了订单:" + number);
		} catch (Exception e) {
			e.printStackTrace();
		}finally{
			lock.unlock();
		}
	}
	
	public static void main(String[] args) {
		System.out.println("##生成了订单####");
		for(int i=0;i<50;i++){
			new Thread(new OrderService()).start();
		}
	}

但是方式2的实现上更可靠，这是基于zookeeper自身的可靠性机制实现的。

