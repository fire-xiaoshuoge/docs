# Hikari

## 数据库

### 数据库连接池

数据库连接池在百度百科中的定义是：数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个；释放空闲时间超过最大空闲时间的数据库连接，来避免因为没有释放数据库连接而引起的数据库连接遗漏。这项技术能明显提高对数据库操作的性能。

![image-20200331014540651](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331014540651.png)

以访问MySQL为例，执行一个SQL语句的完整TCP流程共经历TCP三次握手建立连接、MySQL三次握手认证、SQL语句执行、MySQL关闭、TCP四次挥手关闭连接5个步骤，如图2-2所示。

![image-20200331014647836](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331014647836.png)

这样的传统方式比较适合教科书，但是却不适合企业实际生产。它存在的主要问题列举如下：

1）创建连接和关闭连接的过程比较耗时，并发时系统会变得很卡顿。

2）数据库同时支持的连接总数是有限的，如果并发量很大，那么数据库连接的总数就会被消耗光，增加数据库的负载，新的数据库连接请求就会失败。这样就会极大地浪费数据库的资源，极易造成数据库服务器内存溢出、宕机。

3）为了执行一条SQL，却产生了很多我们并不关心的网络IO。

4）应用如果频繁地创建连接和关闭连接，会导致JVM临时对象较多，GC频繁。

5）频繁关闭连接后，会出现大量TIME_WAIT的TCP状态（在2个MSL之后关闭），这点很棘手。这个问题在第1章中已经结合实践调优经验详细说明过。

6）应用的响应时间及QPS较低。

数据库连接池带来的优点列举如下：

1）资源重用更佳。由于数据库连接得到复用，减少了大量创建和关闭连接带来的开销，也大量减少了内存碎片和数据库临时进程、线程的数量，整体系统的运行更加平稳。

2）系统调优更简便。由于频繁关闭连接会出现TCP大量TIME_WAIT状态，如第1章的案例所描述的那样，TIME_WAIT的调优非常繁琐，使用了数据库连接池以后，由于资源重用，大大减少了频繁关闭连接的开销，大大降低TIME_WAIT的出现频率。当然，数据库连接池也有它自己独特的配置参数，这些参数如何调优本书后续章节会详细介绍。

3）系统响应更快。数据库连接池在应用初始化的过程中一般都会提前准备好一些数据库连接，业务请求可以直接使用已经创建的连接而不需要等待创建连接的开销。初始化数据库连接配合资源重用，使得数据库连接池可以大大缩短系统整体响应时间。

4）连接管理更灵活。数据库连接池作为一款中间件，除了扮演有界缓冲的角色以外，在统一的连接管理上同样可以做很多文章。用户可以自行配置连接的最小数量、最大数量、最常空闲时间、获取连接超时间、心跳检测等。另外，用户也可以结合新的技术趋势，增加数据库连接池的动态配置、监控、故障演习等一系列实用的功能。

### 数据库连接池原理

数据库连接池原理是：在系统初始化的时候，在内存中开辟一片空间，将一定数量的数据库连接作为对象存储在对象池里，并对外提供数据库连接的获取和归还方法。用户访问数据库时，并不是建立一个新的连接，而是从数据库连接池中取出一个已有的空闲连接对象；使用完毕归还后的连接也不会马上被关闭，而是由数据库连接池统一管理回收，为下一次借用做好准备。如果由于高并发请求导致数据库连接池中的连接被借用完毕，其他线程就会等待，直到有连接被归还。整个过程中，连接并不会被关闭，而是源源不断地循环使用，有借有还。数据库连接池还可以通过设置其参数来控制连接池中的初始连接数、连接的上下限数，以及每个连接的最大使用次数、最大空闲时间等，也可以通过其自身的管理机制来监视数据库连接的数量、使用情况等。

### 常见数据库连接池

❑c3p0：是一个开放源代码的JDBC连接池，它在lib目录中与Hibernate一起发布，包括了实现JDBC3和JDBC2扩展规范说明的Connection和Statement池的DataSources对象。

❑Proxool：是一个Java SQL Driver驱动程序，提供了对选择的其他类型的驱动程序的连接池封装。可以非常简单地移植到现存的代码中，完全可配置，快速、成熟、健壮。可以透明地为现存的JDBC驱动程序增加连接池功能。

❑Jakarta DBCP:DBCP是一个依赖Jakartacommons-pool对象池机制的数据库连接池。DBCP可以直接在应用程序中使用。也许这就是Tomcat DBCP连接池，Tomcat默认使用的就是这个连接池。

❑DDConnectionBroker：是一个简单、轻量级的数据库连接池。

❑DBPool：是一个高效、易配置的数据库连接池。它除了支持连接池应有的功能之外，还包括了一个对象池，使用户能够开发一个满足自己需求的数据库连接池。

❑XAPool：是一个XA数据库连接池。它实现了javax.sql.XADataSource，并提供了连接池工具。

❑Primrose：是一个Java开发的数据库连接池。当前支持的容器包括Tomcat4&5、Resin3与JBoss3。它同样也有一个独立的版本，可以在应用程序中使用而不必运行在容器中。Primrose通过一个Web接口来控制SQL处理的追踪、配置，以及动态池管理。在重负荷的情况下可进行连接请求队列处理。

❑SmartPool：是一个连接池组件，它模仿应用服务器对象池的特性。SmartPool能够解决一些临界问题，如连接泄漏（connection leaks）、连接阻塞、打开的JDBC对象（如Statements、PreparedStatements）等。

❑MiniConnectionPoolManager：是一个轻量级JDBC数据库连接池。它只需要Java1.5（或更高）即可，并且没有依赖第三方包。

❑BoneCP：是一个快速、开源的数据库连接池。帮用户管理数据连接，让应用程序能更快速地访问数据库。比c3p0/DBCP连接池速度快25倍。

❑Druid：它不仅是一个数据库连接池，还包含一个ProxyDriver、一系列内置的JDBC组件库、一个SQL Parser。支持所有JDBC兼容的数据库，包括Oracle、MySQL、Derby、Postgresql、SQL Server、H2等。

![image-20200331014905413](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331014905413.png)

![image-20200331014917390](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331014917390.png)

## Hikari

### Hikari

HikariCP是Spring Boot 2.x版本官方宣布默认的数据库连接池，SpringBoot对HikariCP的采纳也从侧面说明HikariCP在使用广泛性、认可度、性能、稳定性等方面是经得起检验的，也表明HikariCP的应用前景和未来发展是一片光明。

### HikariCP参数

1、dataSourceClassName
这是DataSourceJDBC驱动程序提供的类的名称。请查阅您的特定JDBC驱动程序的文档以获取此类名称，或参阅下表。注XA数据源不受支持。XA需要像bitronix这样的真正的事务管理器 。请注意，如果您正在使用jdbcUrl“旧式”基于DriverManager的JDBC驱动程序配置，则不需要此属性 。 默认值：无

2、jdbcUrl
该属性指示HikariCP使用“基于DriverManager的”配置。我们认为基于DataSource的配置（上图）由于各种原因（参见下文）是优越的，但对于许多部署来说，几乎没有显着差异。 在“旧”驱动程序中使用此属性时，您可能还需要设置该 driverClassName属性，但不要先尝试。 请注意，如果使用此属性，您仍然可以使用DataSource属性来配置您的驱动程序，实际上建议您使用URL本身中指定的驱动程序参数。 默认值：无

3、username
此属性设置从基础驱动程序获取连接时使用的默认身份验证用户名。请注意，对于DataSources，这通过调用DataSource.getConnection(*username*, password)基础DataSource 以非常确定的方式工作。但是，对于基于驱动程序的配置，每个驱动程序都不同。在基于驱动程序的情况下，HikariCP将使用此username属性来设置传递给驱动程序调用的user属性。如果这不是你所需要的，例如完全跳过这个方法并且调用。 默认值：无PropertiesDriverManager.getConnection(jdbcUrl, props)addDataSourceProperty("username", ...)

4、password
此属性设置从基础驱动程序获取连接时使用的默认身份验证密码。请注意，对于DataSources，这通过调用DataSource.getConnection(username, *password*)基础DataSource 以非常确定的方式工作。但是，对于基于驱动程序的配置，每个驱动程序都不同。在基于驱动程序的情况下，HikariCP将使用此password属性来设置传递给驱动程序调用的password属性。如果这不是你所需要的，例如完全跳过这个方法并且调用。 默认值：无PropertiesDriverManager.getConnection(jdbcUrl, props)addDataSourceProperty("pass", ...)

5、autoCommit
此属性控制从池返回的连接的默认自动提交行为。它是一个布尔值。 默认值：true

6、 connectionTimeout
此属性控制客户端（即您）将等待来自池的连接的最大毫秒数。如果在没有可用连接的情况下超过此时间，则会抛出SQLException。最低可接受的连接超时时间为250 ms。 默认值：30000（30秒）

7、 idleTimeout
此属性控制允许连接在池中闲置的最长时间。 此设置仅适用于minimumIdle定义为小于maximumPoolSize。一旦池达到连接，空闲连接将不会退出minimumIdle。连接是否因闲置而退出，最大变化量为+30秒，平均变化量为+15秒。在超时之前，连接永远不会退出。值为0意味着空闲连接永远不会从池中删除。允许的最小值是10000ms（10秒）。 默认值：600000（10分钟）

8、 maxLifetime
此属性控制池中连接的最大生存期。正在使用的连接永远不会退休，只有在关闭后才会被删除。在逐个连接的基础上，应用较小的负面衰减来避免池中的大量消失。 我们强烈建议设置此值，并且应该比任何数据库或基础设施规定的连接时间限制短几秒。 值为0表示没有最大寿命（无限寿命），当然是idleTimeout设定的主题。 默认值：1800000（30分钟）

9、connectionTestQuery
如果您的驱动程序支持JDBC4，我们强烈建议您不要设置此属性。这是针对不支持JDBC4的“传统”驱动程序Connection.isValid() API。这是在连接从池中获得连接以确认与数据库的连接仍然存在之前将要执行的查询。再一次，尝试运行没有此属性的池，如果您的驱动程序不符合JDBC4的要求，HikariCP将记录一个错误以告知您。 默认值：无

10、minimumIdle
该属性控制HikariCP尝试在池中维护的最小空闲连接数。如果空闲连接低于此值并且连接池中的总连接数少于此值maximumPoolSize，则HikariCP将尽最大努力快速高效地添加其他连接。但是，为了获得最佳性能和响应尖峰需求，我们建议不要设置此值，而是允许HikariCP充当固定大小的连接池。 默认值：与maximumPoolSize相同

11、maximumPoolSize
此属性控制池允许达到的最大大小，包括空闲和正在使用的连接。基本上这个值将决定到数据库后端的最大实际连接数。对此的合理价值最好由您的执行环境决定。当池达到此大小并且没有空闲连接可用时，对getConnection（）的调用将connectionTimeout在超时前阻塞达几毫秒。请阅读关于游泳池尺寸。 默认值：10

12、metricRegistry
该属性仅通过编程配置或IoC容器可用。该属性允许您指定池使用的Codahale / Dropwizard 实例MetricRegistry来记录各种指标。有关 详细信息，请参阅Metrics维基页面。 默认值：无

13、healthCheckRegistry
该属性仅通过编程配置或IoC容器可用。该属性允许您指定池使用的Codahale / Dropwizard 的实例HealthCheckRegistry来报告当前的健康信息。有关 详细信息，请参阅健康检查 wiki页面。 默认值：无

14、poolName
此属性表示连接池的用户定义名称，主要出现在日志记录和JMX管理控制台中以识别池和池配置。 默认：自动生成

15、 initializationFailTimeout
如果池无法成功初始化连接，则此属性控制池是否将“快速失败”。任何正数都取为尝试获取初始连接的毫秒数; 应用程序线程将在此期间被阻止。如果在超时发生之前无法获取连接，则会引发异常。此超时被应用后的connectionTimeout 期。如果值为零（0），HikariCP将尝试获取并验证连接。如果获得连接但未通过验证，将抛出异常并且池未启动。但是，如果无法获得连接，则会启动该池，但后续获取连接的操作可能会失败。小于零的值将绕过任何初始连接尝试，并且在尝试获取后台连接时，池将立即启动。因此，以后努力获得连接可能会失败。 默认值：1

16、isolateInternalQueries
此属性确定HikariCP是否在其自己的事务中隔离内部池查询，例如连接活动测试。由于这些通常是只读查询，因此很少有必要将它们封装在自己的事务中。该属性仅适用于autoCommit禁用的情况。 默认值：false

17、allowPoolSuspension
该属性控制池是否可以通过JMX暂停和恢复。这对于某些故障转移自动化方案很有用。当池被暂停时，呼叫 getConnection()将不会超时，并将一直保持到池恢复为止。 默认值：false

18、readOnly
此属性控制默认情况下从池中获取的连接是否处于只读模式。注意某些数据库不支持只读模式的概念，而其他数据库则在Connection设置为只读时提供查询优化。无论您是否需要此属性，都将主要取决于您的应用程序和数据库。 默认值：false

19、registerMbeans
该属性控制是否注册JMX管理Bean（“MBeans”）。 默认值：false

20、catalog
该属性设置默认目录为支持目录的概念数据库。如果未指定此属性，则使用由JDBC驱动程序定义的默认目录。 默认：驱动程序默认

21、connectionInitSql
该属性设置一个SQL语句，在将每个新连接创建后，将其添加到池中之前执行该语句。如果这个SQL无效或引发异常，它将被视为连接失败并且将遵循标准重试逻辑。 默认值：无

22、driverClassName
HikariCP将尝试通过DriverManager仅基于驱动程序来解析驱动程序jdbcUrl，但对于一些较旧的驱动程序，driverClassName还必须指定它。除非您收到明显的错误消息，指出找不到驱动程序，否则请忽略此属性。 默认值：无

23、transactionIsolation
此属性控制从池返回的连接的默认事务隔离级别。如果未指定此属性，则使用由JDBC驱动程序定义的默认事务隔离级别。如果您有针对所有查询通用的特定隔离要求，请仅使用此属性。此属性的值是从不断的名称Connection 类，如TRANSACTION_READ_COMMITTED，TRANSACTION_REPEATABLE_READ等 默认值：驱动程序默认

24、 validationTimeout
此属性控制连接测试活动的最长时间。这个值必须小于connectionTimeout。最低可接受的验证超时时间为250 ms。 默认值：5000

25、 leakDetectionThreshold
此属性控制在记录消息之前连接可能离开池的时间量，表明可能存在连接泄漏。值为0意味着泄漏检测被禁用。启用泄漏检测的最低可接受值为2000（2秒）。 默认值：0

26、 dataSource
此属性仅通过编程配置或IoC容器可用。这个属性允许你直接设置DataSource池的实例，而不是让HikariCP通过反射来构造它。这在一些依赖注入框架中可能很有用。当指定此属性时，dataSourceClassName属性和所有DataSource特定的属性将被忽略。 默认值：无

27、schema
该属性设置的默认模式为支持模式的概念数据库。如果未指定此属性，则使用由JDBC驱动程序定义的默认模式。 默认：驱动程序默认

28、 threadFactory
此属性仅通过编程配置或IoC容器可用。该属性允许您设置java.util.concurrent.ThreadFactory将用于创建池使用的所有线程的实例。在一些只能通过ThreadFactory应用程序容器提供的线程创建线程的有限执行环境中需要它。 默认值：无

29、 scheduledExecutor
此属性仅通过编程配置或IoC容器可用。该属性允许您设置java.util.concurrent.ScheduledExecutorService将用于各种内部计划任务的实例。如果为ScheduledThreadPoolExecutor 实例提供HikariCP，建议setRemoveOnCancelPolicy(true)使用它。 默认值：无

### HikariCP优化

HikariCP官网详细了介绍HikariCP所做的优化，总结如下：

❑优化并精简字节码、优化代码和拦截器。

❑使用FastList替代ArrayList。

❑更好的并发集合类实现ConcurrentBag。

❑其他针对BoneCP缺陷的优化，比如对于耗时超过一个CPU时间片的方法调用的研究。

## HikariCP连接原理

数据库连接池的定义就是负责分配、管理和释放数据库连接。它允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个；释放空闲时间超过最大空闲时间的数据库连接，来避免因为没有释放数据库连接而引起的数据库连接遗漏。“麻雀虽小，五脏俱全”，数据库连接池以连接池的管理为中心，连接池的建立和释放作为两个基本点，辅以一系列的功能。HikariCP也不例外，核心功能主要包括连接的生成、获取、归还、销毁等生命周期。作为数据库连接池，HikariCP也是具有这些特征的。

### 获取连接

获取连接是HikariCP的核心功能，HikariDataSource对象首先通过getConnection方法获得HikariPool真正的getConnection方法，HikariPool内部通过我们介绍过的ConcurrentBag（一个lock-free集合，在连接池及多线程交互的实现上具有比LinkedBlocking-Queue和LinkedTransferQueue更优越的并发读写性能）的borrow方法获取PoolEntry，它将首先尝试从线程的ThreadLocal最近使用的连接列表中获取未使用的连接。最后通过PoolEntry执行Create Proxy Connection方法创建一个物理连接并返回其代理连接Proxy Connection。

![image-20200331015838102](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331015838102.png)

### 归还连接

连接有借必有还，看完连接的获取，我们一起再来看一下连接归还的时序图，如图7-6所示。

![image-20200331015920633](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331015920633.png)

在ProxyConnection代理层获取到的连接，进行归还时调用了代理层的close方法。HikariCP归还连接是一系列没有返回值的void操作，ProxyConnection的close方法并没有直接调用JDBC的close方法，而是依次调用了PoolEnry的recycle方法、HikariPool的recycle方法及ConcurrentBag的requite方法，这一系列方法传递的参数都是PoolEntry。

据库连接池HikariCP的close方法返回的连接其实是封装在这个ProxyConnection代理连接中的，当调用它的时候，它只返回与池的连接，但是依然保持与数据库的基础连接是打开的。数据库连接池通常都是以这种方式工作的，数据库连接池的性能优势就是来自于连接保持打开，通过代理连接，对用户来说是透明的。如果需要关闭这个连接，可以将它先转换为ProxyConnection，然后调用它的unwrap方法，最后关闭这个内部连接。

### 关闭连接

在可以热部署的Web应用程序中，关闭DataSource非常重要。通常可以调用HikariDataSource实例的shutdown或者close方法，也可以配置Spring或其他IOC容器来指定destroy方法。一旦Connection进入关闭连接阶段，它立即从池中被移除，但是仍然和数据库DB有活动连接，直到Connection.close完成。HikariCP永远不会杀死正在使用的连接，除非池本身被关闭。

关闭分为HikariDataSouce和HikariPool两种关闭，这两者截然不同，不可同日而语。

HikariDatasource作为HikariCP对外对使用方提供的DataSource，它的close方法是粗暴的shutdown操作。

与HikariDataSource对外提供的close（或者理解为shutdown）接口不同，HikariPool提供的内部closeConnection方法才是我们通常意义上理解的数据库连接池内部的连接关闭逻辑。它的时序图如图7-7所示。

![image-20200331020015444](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331020015444.png)

当HikariPool执行closeConnection方法时，首先先从ConcurrentBag中移除PoolEntry，然后PoolEntry自身close，接着独立线程池closeConnectionExecutor（本质是ThreadPoolExecutor）调用JDBC的方法进行物理连接的关闭。如果poolState的状态为0，还会使用另一个独立线程池addConnectionExecutor（与closeConnectionExecutor对称，本质上也是ThreadPoolExecutor）进行新连接的生成，fillPool补足到HikariCP的配置值。

HikariCP关闭连接的5种情况

❑连接验证失败。这对应用程序是不可见的。连接已停用并已替换。用户会看到一条日志消息：“Failed to validate connection...”。

❑连接闲置的时间超过了idleTimeout。这对应用程序是不可见的。连接已停用并已替换。用户看到关闭的原因：“connection has passed idleTimeout”。

❑连接达到了其maxLifetime。这对应用程序是不可见的。连接已停用并已替换。用户会看到一个关闭原因：“connection has passed maxLifetime”，或者如果在到达时maxLifetime正在使用该连接，用户会晚一点看到原因：“connection is evicted ordead”。

❑用户手动驱逐连接。这对应用程序是不可见的。连接已停用并已替换。用户会看到关闭的原因：“connection evicted by user”。

❑JDBC调用引发了无法恢复的问题SQLException。这应该对应用程序可见。用户会看到关闭的原因：“connection is broken”。

###  生成连接

连接的创建我们需要了解PoolEntry的概念。前文已经介绍过了，PoolEntry是一个封装了物理连接的对象，在HikariPool中定义了两个PoolEntry，分别代表我们刚才介绍的连接生成的两种场景（addBagItem和fillPool）：

addConnectionExecutor线程调用HikariPool的createPoolEntry方法进行连接生成，HikariPool继承的父类PoolBase提供的newPoolEntry会先进行物理连接的创建，创建完成以后的连接会被封装为PoolEntry后放入ConcurrentBag。

## HikariCP参数源码解析

### allowPoolSuspension

1．概念

该属性控制池是否可以通过JMX暂停和恢复。这对于某些故障转移自动化方案很有用。当池被暂停时，调用getConnection()将不会超时，并将一直保持到池恢复为止。默认值为false。

2．用途

allowPoolSuspension是一个暂停模式，实战中使用该配置主要分为如下4步：

1）暂停数据库连接池。

2）更改数据库连接池的配置，或者更改DNS配置（指向新的主服务器）。

3）软驱逐现有的连接。

4）恢复数据库连接池。

3．源码解析

suspendPool这个方法来源于接口HikariPoolMXBean，它是HikariCP连接池实例的MBean管理接口。

![image-20200331020249828](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331020249828.png)

如上代码所示，该接口中的几个主要方法分别对应了上一小点中使用该配置的4个步骤：

1）暂停数据库连接池，使用suspendPool()。

2）更改池配置，或更改DNS配置（指向新主服务器）。

3）软驱逐现有的连接，使用softEvictConnections()。

4）恢复数据库连接池，使用resumePool()。

### validationTimeout

 1．概念

此属性控制连接测试活动的最长时间。这个值必须小于connectionTimeout。最低可接受的验证超时时间为250ms。默认值为5000。

2. Write

有两处validationTimeout的写入，一处是PoolBase构造函数，另一处是HouseKeeper线程。

3. Read

在com.zaxxer.hikari.pool.HikariPool的核心方法getConnection中用到了validation-Timeout，我们看一下源码，borrow到poolEntry之后，如果不是isMarkedEvicted，则会调用isConnectionAlive来判断连接的有效性。再强调一下，HikariCP在borrow连接的时候校验连接的有效性。

### leakDetectionThreshold

1．概念

如果leakDetectionThreshold的值大于0且当前不是单元测试模式，则会进一步判断：如果leakDetectionThreshold值小于2秒或者leakDetectionThreshold值大于maxLifetime连接最长生命周期，那么leakDetectionThreshold会被重置为0。因此，若要leakDetectionThreshold配置项生效，leakDetectionThreshold的配置值必须大于0且不能小于2秒，而且leakDetectionThreshold的值不能超过maxLifetime的值（maxLifetime默认值为1800000毫秒=30分钟）。

2．写

有两处validationTimeout的写入，一处是PoolBase构造函数，另一处是HouseKeeper线程。

3．读

在com.zaxxer.hikari.pool.HikariPool的核心方法getConnection返回的时候调用了poolEntry.createProxyConnection(leakTaskFactory.schedule(poolEntry), now)，创建代理连接的时候关联了ProxyLeakTask，这里就是leakDetectionThreshold读取部分的内容。

连接泄漏检测的原理就是：连接有借有还，HikariCP是每借用一个connection则会创建一个延时的定时任务，在归还或者出异常时，或者用户手动调用evictConnection的时候取消这个任务。

## HikariCP监控指标

连接池的监控指标也会展示部分配置的信息，配置需要根据当前数据库的类型和规模、应用的QPS和RT、系统的并发能力综合考虑。HikariCP配置的信息、原理和建议在前面的章节已经详细介绍过，比较重要的是要规划好初始化连接的大小、最大连接、空闲连接，以及心跳语句检查尽量采用性能更高的ping命令等方面。

![image-20200331020814578](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331020814578.png)

Metrics一般支持以下5种类型。

❑Gauge：常规计量，用来统计瞬时状态的数据信息，如系统中处于pending状态的job。

❑Counter:Gauge的特例，维护一个计数器，用于累计值。

❑Meters：度量某个时间段的平均处理次数（request per second），如每隔1分钟、5分钟、15分钟的TPS。

❑Histograms：统计数据的分布情况，最大值、最小值、平均值、中位数，以及百分比（75%、90%、95%、98%、99%和99.9%）。

❑Timers：统计某段代码段执行时间及分布，是基于Histograms和Meters实现的。

### hikaricp_pending_threads

hikaricp_pending_threads表示当前排队获取连接的线程数，Gauge类型。该指标持续飙高，说明DB连接池中基本上已无空闲连接。

### hikaricp_connection_acquired_nanos

hikaricp_connection_acquired_nanos表示连接获取的等待时间，一般取99位数，Summary类型。

### hikaricp_idle_connections

hikaricp_idle_connections表示当前空闲连接数，Gauge类型。HikariCP是可以配置最小空闲连接数的，当此指标长期比较高（等于最大连接数）时，可以适当减小配置项中的最小连接数。

### hikaricp_active_connections

hikaricp_active_connections表示当前正在使用的连接数，Gauge类型。如果此指标长期在设置的最大连接数附近上下波动时，或者长期保持在最大线程数时，可以考虑增大最大连接数。

### hikaricp_connection_usage_millis

hikaricp_connection_usage_millis表示连接被复用的间隔时长，一般取99位数，Summary类型。该配置的意义在于表明连接池中的一个连接从被返回连接池到再被复用的时间间隔，对于使用较少的数据源，此指标可能会达到秒级。可以结合流量高峰期的此项指标与激活连接数指标来确定是否需要减小最小连接数，若在高峰期也是秒级，说明对此数据源使用不频繁，可考虑减小最小连接数。

### hikaricp_connection_timeout_total

hikaricp_connection_timeout_total表示每分钟超时连接数，Counter类型。主要用来反映连接池中超时的总连接数量，此处的超时指的是连接创建超时。若经常连接创建超时，一个排查方向是与运维人员配合，检查网络是否正常。

### hikaricp_connection_creation_millis

hikaricp_connection_creation_millis表示连接创建成功的耗时，一般取99位数，Summary类型。该配置的意义在于表明创建一个连接的耗时，主要反映从当前机器到数据库的网络情况。在IDC中意义不大，除非是网络抖动或者机房间通讯中断才会有异常波动。

## HikariCP常见问题

在排查HikariCP相关问题之前，可以根据具体情况先尝试做3件基础的事情：

1）确保使用的是最新的HikariCP版本和数据库驱动程序（当然，类路径下也不要同时出现多个版本的HikariCP，否则可能出现NoClassDefFoundError问题）。同理，如果使用Scala和slick来使用HikariCP，也推荐使用新版本，许多问题比如事务死锁、池耗尽、异步问题、连接泄漏等在HikariCP 3.x和Slick 2.x版本中都得到了修复。

2）启用com.zaxxer.hikari包下的DEBUG级别日志记录，开启leakDetectionThreshold连接泄漏检测，统计每个连接创建以及连接的堆栈跟踪信息。一般来说，HikariCP每30秒记录一次池统计信息，如果没有报告泄漏则可以将日志增加到30分钟，可以的话结合Metrics采集和直观图展示进行问题分析。No operations allowed after connection closed是一个经典问题，它本质上是若连接从数据库连接池中借用出来过很久后才被使用，这样的问题可以直接采用该方法进行排查。

3）将maxPoolSize设为一个较小的值，并设置maxLifeTime为15或20分钟（1200000毫秒）。还有一个需要再次强调的概念，HikariCP作为数据库连接池，当HikariCP或任何池返回连接时getConnection()，它实际返回的是一个代理对象，它拦截该close()调用并将连接返回到池，不是实际关闭它，而是将该连接放回池中。evictConnection()是一种很少使用的方法，这是一种强制关闭与数据库的连接并从池中驱逐连接的方法。

### HikariCP故障分析技巧

HikariCP常见的异常是“Connection is not available, request timed out after”，这个异常通常是在数据库连接池已经达到了最大容量，且大量连接都在同时调用数据库连接池的getConnection方法时产生的。不仅如此，它表明线程在调用getConnection等待了connectionTimeout的一段时间内，希望连接返回到池，但是没有连接可用。它并不表示HikariCP提供了无效的连接，HikariCP也几乎做不到这个功能。导致这个错误通常有两种可能：

1）连接泄漏。在这种情况下，连接被借用但是从未返回。最终，池将耗尽连接，应用程序将永远阻塞。此时建议启用leakDetectionThreshold，并将其设置为connectionTimeout的2倍。如果在日志中看到连接泄漏的告警，则应该提供指向泄漏源的堆栈跟踪。

2）长时间运行的查询（执行时间超过connectionTimeout的查询）。在这种情况下，可以从慢SQL角度进行分析，也可以使所有连接都被这些查询占用，并防止新的线程在connectionTimeout到达之前从池中获取连接。

connectionTimeout是一个很重要的问题排查指标。connectionTimeout的意思并不是获取连接的超时时间，而是从连接池返回连接的超时时间。关于SQL执行的超时时间，JDBC可以直接使用Statement.setQueryTimeout, Spring可以使用@Transactional(timeout=10)。关于connectionTimeout有3点结论：

1）如果在没有空闲连接且连接池满不能新建连接的情况下，HikariCP将阻塞connectionTimeout的时间，没有得到连接抛出SQLTransientConnectionException。

2）如果微服务使用了连接的健康检测，如果捕获了此异常，就会不断地报告健康监测的错误。

3）如果connectionTimeout设置得太大的话，在数据库挂起的时候，也很容易阻塞业务线程。

### Spark/Scala连接池泄漏

连接泄漏是一个需要解决的严重的应用程序错误，HikariCP不会通过从池中驱逐连接来掩盖连接泄漏的问题。如果泄漏文件句柄，最终无法再打开文件；如果泄漏套接字句柄，最终将无法接受或创建新的套接字连接。正确的资源管理是应用程序的基础。

### JDBC超时

如图13-1所示，一个应用中我们挑选出一个实例Instance，采用HikariCP的数据库连接池连接数据库，这就是完整的应用与数据库之间的超时层级关系。超时按照级别由高到低分别是Transaction Timeout（事务超时）、Statement Timeout（语句超时）和SocketTimeout（Socket超时）。高级别的超时依赖于低级别的超时，只有当低级别的超时无误时，高级别的超时才能确保正常。例如，当Socket超时出现问题时，高级别的Statement Timeout和Transaction Timeout都将失效。从图13-1中我们也可以看出，HikariCP其实并不参与数据库超时的处理，它的功能仅仅是连接的管理。一次请求从HikariCP到获取JDBC Connection，依次经历了事务超时、语句超时、JDBC驱动Socket超时、JDBC Driver Socket超时、操作系统超时等段。

![image-20200331022316905](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200331022316905.png)

1. Transaction TimeoutHikariCP

HikariCP永远不会让正在进行中的事务超时。在这种场景下如果要配置超时，idleTimeout显然不适合，因为它仅适用于池中闲置的连接；maxLifetime也不适用，因为连接池正在使用中。因此只会被标记为驱逐，并且在返回池之前不会退出。因此需要在Statement本身设置超时，或在数据库中配置事务超时。

2. Statement Timeout

Statement Timeout用来限制SQL语句的执行时间，也就是限制Statement的执行时长，通过JDBC的java.sql.Statement.setQueryTimeout(int timeout) API进行设置，但是该项配置更多的是通过框架配置。以iBatis为例，Statement Timeout的默认值可通过sql-map-config.xml中的defaultStatementTimeout属性进行设置。同时，还可以设置Sqlmap中select、insert、update标签的timeout属性，从而对不同sql语句的超时时间进行独立的配置。

3. JDBC Socket Timeout

这是一种底层的超时，通常JDBC驱动类型是第4种基于Socket Timeout的方式与数据库进行连接，但MySQL的JDBC驱动中的connectionTimeout（建立连接的时间）和socketTimeout的默认值为0，数据库并不对应用与数据库中的连接超时进行处理。在连接时和读写时可以分别通过Socket.connect(SocketAddress endpoint, int timeout)和Socket. setSoTimeout(inttimeout)来设置。强烈建议Socket Timeout值要设置得高于Statement Timeout，否则Statement的超时就没有意义。

4．操作系统的Socket Timeout

除了JDBC的Socket Timeout和Connection Timeout，操作系统同样能够对Socket Timeout进行配置，它同样可以作为防止网络故障等问题无休止等待的dead connection的最后一道防线。有时候我们没有设置JDBC的Socket Timeout，但是会发现过了段时间莫名其妙恢复了，这就是因为系统重连了。

### 快速恢复

1．未确认TCP

数据库连接池本质上还是通过TCP进行通信的，但是HikariCP无法恢复未确认TCP流量的池外连接。TCP是一种同步的通信手段，当交换SYN和ACK分组时，需要连接的双方进行“握手”。但是如果TCP通信突然中断，客户端或服务器可能会等待永远不会到来的数据包，导致连接直接卡住，直到发生操作系统级别的TCP超时。这个过程可能长达数小时，具体取决于操作系统TCP的堆栈调整。

这种问题的解决方式通常有两种。

1）确保socketTimeout（下面TCP超时的内容）。不过这需要小心，socketTimeout应该设置为至少比最长的预期查询长2～3倍，通常也可以设置为比最长的预期查询长1分钟。

2）依赖驱动程序进行解决。PostgreSQL暂未支持Connection.setNetworkTimeout()，如果支持了就可以通过HikariCP配置。

2. TCP超时

HikariCP官方文档建议将驱动程序级别的Socket Timeout设置为至少是运行时间最长SQL事务的2～3倍，或者30秒，以时间较长者为准。当然，这个时间也可以由用户根据应用的超时时间来设置。

如果驱动程序支持网络差时，并且为了确保网络中断不会导致应用程序查询无限期地挂在TCP堆栈中，建议设置全局网络超时。如下是3款常见数据库的配置设置：

1）MySQL数据库驱动程序（Connector/J）通过socketTimeout[插图]属性支持套接字超时。但是，如果将其设置为2分钟，则还需要将HikariCP的idleTimeout设置为相同值或更短。

2）PostgreSQL数据库驱动程序通过socketTimeout[插图]属性支持套接字超时。

3）Microsoft SQL Server数据库驱动程序通过支持socketTimeout[插图]属性套接字超时。

3．为DNS名称查找设置JVM TTL

HikariCP不太可能快速进行DNS的更改检测。如果需要写一个每30秒轮询一次的调度程序以检查DNS是否已更改，那么发生了更改建议不要创建一个新池实例，而是使用在HikariConfigMXbean上调用softEvictConnections的方法。

### HikariCP关闭连接的5种情况

以下5种情况可以作为线上疑难问题的指导工具手册：

1）连接验证失败。这对应用程序是不可见的。连接已停用并已替换。用户会看到一条日志消息“Failed to validate connection...”。但是为什么这种连接关闭是在池中进行的？这种异常通常表示数据库在达到maxLifetime之前就关闭了连接，可以检查NTP服务器有没有产生时间跳跃，也可以查看maxLifetime和数据库的wait_timeout的值大小。

2）连接闲置的时间超过了idleTimeout。这对应用程序是不可见的。连接已停用并已替换。用户看到关闭的原因(connection has passed idleTimeout)。

3）连接达到了它的maxLifetime。这对应用程序是不可见的。连接已停用并已替换。用户会看到一个关闭原因(connection has passed maxLifetime)，或者如果在到达时maxLifetime正在使用该连接，用户会晚一点看到原因(connection is evicted or dead)。

4）用户手动逐出连接。这对应用程序是不可见的。连接已停用并已替换。用户会看到关闭的原因(connection evicted by user)。

5）JDBC调用引发了无法恢复的问题SQLException。这应该对应用程序可见。用户会看到关闭的原因(connection is broken)。