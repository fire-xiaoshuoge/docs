# Apache Dubbo

## Dubbo基础

### Dubbo

Dubbo(读音[ˈdʌbəʊ])是阿里巴巴公司开源的一个高性能优秀的[服务框架](https://baike.baidu.com/item/服务框架)，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和  [Spring](https://baike.baidu.com/item/Spring)框架无缝集成。

Dubbo是一款高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

### 主要核心部件

**Remoting:** 网络通信框架，实现了 sync-over-async 和request-response 消息机制.

**RPC:** 一个[远程过程调用](https://baike.baidu.com/item/远程过程调用)的抽象，支持[负载均衡](https://baike.baidu.com/item/负载均衡)、[容灾](https://baike.baidu.com/item/容灾)和[集群](https://baike.baidu.com/item/集群)功能

**Registry:** 服务目录框架用于服务的注册和服务事件发布和订阅

### Dubbo架构

![img](http://dubbo.apache.org/img/architecture.png)

**Provider**

暴露服务方称之为“服务提供者”。

**Consumer**

调用[远程服务](https://baike.baidu.com/item/远程服务)方称之为“服务消费者”。

**Registry**

服务注册与发现的中心目录服务称之为“服务注册中心”。

**Monitor**

统计服务的调用次数和调用时间的日志服务称之为“服务监控中心”。

(1) 连通性：

注册中心负责服务地址的注册与查找，相当于[目录服务](https://baike.baidu.com/item/目录服务)，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小

监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示

服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销

服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销

注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外

注册中心通过[长连接](https://baike.baidu.com/item/长连接)感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者

注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在[本地缓存](https://baike.baidu.com/item/本地缓存)了提供者列表

注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

(2) 健壮性：

监控中心宕掉不影响使用，只是丢失部分[采样数据](https://baike.baidu.com/item/采样数据)

数据库宕掉后，注册中心仍能通过[缓存](https://baike.baidu.com/item/缓存)提供服务列表查询，但不能注册新服务

注册中心对等[集群](https://baike.baidu.com/item/集群)，任意一台宕掉后，将自动切换到另一台

注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯

服务提供者无状态，任意一台宕掉后，不影响使用

服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

(3) 伸缩性：

注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心

服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

### 特性

- 面向接口代理的高性能RPC调用

  提供高性能的基于代理的远程调用能力，服务以接口为粒度，为开发者屏蔽远程调用底层细节。

- 智能负载均衡

  内置多种负载均衡策略，智能感知下游节点健康状况，显著减少调用延迟，提高系统吞吐量。

- 服务自动注册与发现

  支持多种注册中心服务，服务实例上下线实时感知。

- 高度可扩展能力

  遵循微内核+插件的设计原则，所有核心能力如Protocol、Transport、Serialization被设计为扩展点，平等对待内置实现和第三方实现。

- 运行期流量调度

  内置条件、脚本等路由策略，通过配置不同的路由规则，轻松实现灰度发布，同机房优先等功能。

- 可视化的服务治理与运维

  提供丰富服务治理、运维工具：随时查询服务元数据、服务健康状态及调用统计，实时下发路由策略、调整配置参数。

### 异步调用

Dubbo框架中的异步调用是发生在服务消费端的，异步调用实现基于NIO的非阻塞能力实现并行调用，服务消费端不需要启动多线程即可完成并行调用多个远程服务，相比多线程其开销较小，图8-9所示为Dubbo异步调用链路的概要流程。

![image-20200311215535512](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311215535512.png)

● 在图8-9中，当服务消费端发起RPC调用时使用的是用户线程（步骤1），请求会被转换为IO线程（步骤2），具体向远程服务提供方发起远程调用。

● 步骤2的IO线程使用NIO发起远程调用，用户线程通过步骤3创建了一个Future对象，然后通过步骤4将其设置到RpcContext中。

● 然后用户线程则可以在某个时间从RpcContext中获取设置的Future对象（步骤5），并且通过步骤6设置回调函数，这样用户线程就返回了。

● 当服务提供方返回结果（步骤7）后，调用方线程模型中的线程池线程会把结果通过步骤8写入Future，然后就会回调注册的回调函数。

### 异步执行

Dubbo框架的异步执行是发生在服务提供端的，在Provider端非异步执行时，其对调用方发来的请求的处理是在Dubbo内部线程模型的线程池中的线程来执行的，在Dubbo中服务提供方提供的所有服务接口都使用这一个线程池来执行，所以当一个服务执行比较耗时时，可能会占用线程池中的很多线程，这可能就会影响到其他服务的处理。

Provider端异步执行则将服务的处理逻辑从Dubbo内部线程池切换到业务自定义线程，避免Dubbo线程池中线程被过度占用，有助于避免不同服务间的互相影响。

但是需要注意，Provider端异步执行对节省资源和提升RPC响应性能是没有效果的，这是因为如果服务处理比较耗时，虽然不是使用Dubbo框架的内部线程，但还是需要业务自己的线程来处理，另外副作用还有会新增一次线程上下文切换（从Dubbo内部线程池线程切换到业务线程）。

如图8-11所示，Provider端在同步提供服务时是使用Dubbo内部线程池中的线程来处理的，在异步执行时则是使用业务自己设置的线程来从Dubbo内部线程池中的线程接收请求并进行处理。

![image-20200311215702037](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311215702037.png)



### 服务降级

Dubbo提供了一些服务降级措施，当服务提供端某一个非关键的服务出错时，可以手动对消费端的调用进行降级，这样服务消费端就避免了再去调用出错的服务，以避免加重服务提供端的负担。

· force：return策略：当服务调用方设置某个接口的降级策略为这种方式时，服务调用方在调用该接口服务时会直接在客户端内返回设置的mock值，而不会在通过远程调用方式调用服务提供者，比如配置URL为override：//0.0.0.0/com.books.dubbo.demo.api.GreetingService？category=configurators&dynamic=false&application=first-dubbo-consumer&"+"mock="+type+"：return+null&group=dubbo&version=1.0.0"，其中mock=force：return+null表示服务调用方在调用该服务的方法时都直接返回mock的null值，而不发起远程调用。需要注意的是，在URL里要指明是对哪个接口的哪个分组的哪个版本的服务进行降级，另外category必须为configurators，application为你的服务调用方的应用名称，也就是ApplicationConfig的name值。override：//0.0.0.0/标识该降级策略对所有的服务消费者生效。该URL会被保存到ZooKeeper中，持久化存放该设置，当消费端启动时会获取到该配置。

· fail：return策略：表示服务调用方调用服务提供方的服务失败后再返回mock值，与force：return的区别是前者如果调用服务提供者成功，则返回正常的结果，如果调用失败则返回mock的值。这个功能和上一节讲解的本地服务mock功能一致。

### 线程池策略

· FixedThreadPool：创建一个具有固定个数线程的线程池。

· LimitedThreadPool：创建一个线程池，这个线程池中的线程个数随着需要量动态增加，但是数量不超过配置的阈值。另外，空闲线程不会被回收，会一直存在。

· EagerThreadPool：创建一个线程池，在这个线程池中，当所有核心线程都处于忙碌状态时，将创建新的线程来执行新任务，而不是把任务放入线程池阻塞队列。

· CachedThreadPool：创建一个自适应线程池，当线程空闲1分钟时，线程会被回收；当有新请求到来时，会创建新线程。

## Dubbo框架内核原理

### Dubbo分层架构

![/dev-guide/images/dubbo-framework.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-framework.jpg)

图例说明：

- 图中左边淡蓝背景的为服务消费方使用的接口，右边淡绿色背景的为服务提供方使用的接口，位于中轴线上的为双方都用到的接口。
- 图中从下至上分为十层，各层均为单向依赖，右边的黑色箭头代表层之间的依赖关系，每一层都可以剥离上层被复用，其中，Service 和 Config 层为 API，其它各层均为 SPI。
- 图中绿色小块的为扩展接口，蓝色小块为实现类，图中只显示用于关联各层的实现类。
- 图中蓝色虚线为初始化过程，即启动时组装链，红色实线为方法调用过程，即运行时调时链，紫色三角箭头为继承，可以把子类看作父类的同一个节点，线上的文字为调用的方法。

各层说明

- **config 配置层**：对外配置接口，以 `ServiceConfig`, `ReferenceConfig` 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
- **proxy 服务代理层**：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 `ServiceProxy` 为中心，扩展接口为 `ProxyFactory`
- **registry 注册中心层**：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 `RegistryFactory`, `Registry`, `RegistryService`
- **cluster 路由层**：封装多个提供者的路由及负载均衡，并桥接注册中心，以 `Invoker` 为中心，扩展接口为 `Cluster`, `Directory`, `Router`, `LoadBalance`
- **monitor 监控层**：RPC 调用次数和调用时间监控，以 `Statistics` 为中心，扩展接口为 `MonitorFactory`, `Monitor`, `MonitorService`
- **protocol 远程调用层**：封装 RPC 调用，以 `Invocation`, `Result` 为中心，扩展接口为 `Protocol`, `Invoker`, `Exporter`
- **exchange 信息交换层**：封装请求响应模式，同步转异步，以 `Request`, `Response` 为中心，扩展接口为 `Exchanger`, `ExchangeChannel`, `ExchangeClient`, `ExchangeServer`
- **transport 网络传输层**：抽象 mina 和 netty 为统一接口，以 `Message` 为中心，扩展接口为 `Channel`, `Transporter`, `Client`, `Server`, `Codec`
- **serialize 数据序列化层**：可复用的一些工具，扩展接口为 `Serialization`, `ObjectInput`, `ObjectOutput`, `ThreadPool`

关系说明

- 在 RPC 中，Protocol 是核心层，也就是只要有 Protocol + Invoker + Exporter 就可以完成非透明的 RPC 调用，然后在 Invoker 的主过程上 Filter 拦截点。
- 图中的 Consumer 和 Provider 是抽象概念，只是想让看图者更直观的了解哪些类分属于客户端与服务器端，不用 Client 和 Server 的原因是 Dubbo 在很多场景下都使用 Provider, Consumer, Registry, Monitor 划分逻辑拓普节点，保持统一概念。
- 而 Cluster 是外围概念，所以 Cluster 的目的是将多个 Invoker 伪装成一个 Invoker，这样其它人只要关注 Protocol 层 Invoker 即可，加上 Cluster 或者去掉 Cluster 对其它层都不会造成影响，因为只有一个提供者时，是不需要 Cluster 的。
- Proxy 层封装了所有接口的透明化代理，而在其它层都以 Invoker 为中心，只有到了暴露给用户使用时，才用 Proxy 将 Invoker 转成接口，或将接口实现转成 Invoker，也就是去掉 Proxy 层 RPC 是可以 Run 的，只是不那么透明，不那么看起来像调本地服务一样调远程服务。
- 而 Remoting 实现是 Dubbo 协议的实现，如果你选择 RMI 协议，整个 Remoting 都不会用上，Remoting 内部再划为 Transport 传输层和 Exchange 信息交换层，Transport 层只负责单向消息传输，是对 Mina, Netty, Grizzly 的抽象，它也可以扩展 UDP 传输，而 Exchange 层是在传输层之上封装了 Request-Response 语义。
- Registry 和 Monitor 实际上不算一层，而是一个独立的节点，只是为了全局概览，用层的方式画在一起。

模块分包

![/dev-guide/images/dubbo-modules.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-modules.jpg)

模块说明：

- **dubbo-common 公共逻辑模块**：包括 Util 类和通用模型。
- **dubbo-remoting 远程通讯模块**：相当于 Dubbo 协议的实现，如果 RPC 用 RMI协议则不需要使用此包。
- **dubbo-rpc 远程调用模块**：抽象各种协议，以及动态代理，只包含一对一的调用，不关心集群的管理。
- **dubbo-cluster 集群模块**：将多个服务提供方伪装为一个提供方，包括：负载均衡, 容错，路由等，集群的地址列表可以是静态配置的，也可以是由注册中心下发。
- **dubbo-registry 注册中心模块**：基于注册中心下发地址的集群方式，以及对各种注册中心的抽象。
- **dubbo-monitor 监控模块**：统计服务调用次数，调用时间的，调用链跟踪的服务。
- **dubbo-config 配置模块**：是 Dubbo 对外的 API，用户通过 Config 使用Dubbo，隐藏 Dubbo 所有细节。
- **dubbo-container 容器模块**：是一个 Standlone 的容器，以简单的 Main 加载 Spring 启动，因为服务通常不需要 Tomcat/JBoss 等 Web 容器的特性，没必要用 Web 容器去加载服务。

整体上按照分层结构进行分包，与分层的不同点在于：

- container 为服务容器，用于部署运行服务，没有在层中画出。
- protocol 层和 proxy 层都放在 rpc 模块中，这两层是 rpc 的核心，在不需要集群也就是只有一个提供者时，可以只使用这两层完成 rpc 调用。
- transport 层和 exchange 层都放在 remoting 模块中，为 rpc 调用的通讯基础。
- serialize 层放在 common 模块中，以便更大程度复用。

依赖关系

![/dev-guide/images/dubbo-relation.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-relation.jpg)

图例说明：

- 图中小方块 Protocol, Cluster, Proxy, Service, Container, Registry, Monitor 代表层或模块，蓝色的表示与业务有交互，绿色的表示只对 Dubbo 内部交互。
- 图中背景方块 Consumer, Provider, Registry, Monitor 代表部署逻辑拓扑节点。
- 图中蓝色虚线为初始化时调用，红色虚线为运行时异步调用，红色实线为运行时同步调用。
- 图中只包含 RPC 的层，不包含 Remoting 的层，Remoting 整体都隐含在 Protocol 中。

调用链

![/dev-guide/images/dubbo-extension.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-extension.jpg)

暴露服务时序

![/dev-guide/images/dubbo-export.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-export.jpg)

引用服务时序

![/dev-guide/images/dubbo-refer.jpg](http://dubbo.apache.org/docs/zh-cn/dev/sources/images/dubbo-refer.jpg)

领域模型

在 Dubbo 的核心领域模型中：

- Protocol 是服务域，它是 Invoker 暴露和引用的主功能入口，它负责 Invoker 的生命周期管理。
- Invoker 是实体域，它是 Dubbo 的核心模型，其它模型都向它靠扰，或转换成它，它代表一个可执行体，可向它发起 invoke 调用，它有可能是一个本地的实现，也可能是一个远程的实现，也可能一个集群实现。
- Invocation 是会话域，它持有调用过程中的变量，比如方法名，参数等。

基本设计原则

- 采用 Microkernel + Plugin 模式，Microkernel 只负责组装 Plugin，Dubbo 自身的功能也是通过扩展点实现的，也就是 Dubbo 的所有功能点都可被用户自定义扩展所替换。
- 采用 URL 作为配置信息的统一格式，所有扩展点都通过传递 URL 携带配置信息。

### Dubbo的动态编译原理

众所周知，Java程序要想运行首先需要使用javac把源代码编译为class字节码文件，然后使用JVM把class字节码文件加载到内存创建Class对象后，使用Class对象创建对象实例。正常情况下我们是把所有源文件静态编译为字节码文件，然后由JVM统一加载，而动态编译则是在JVM进程运行时把源文件编译为字节码文件，然后使用字节码文件创建对象实例。

在Dubbo框架中框架会给每个SPI扩展接口动态生成一个对应的适配器类，那么如何生成呢？这里就使用了动态编译技术，在Dubbo中提供了一个Compiler的SPI：

![image-20200328053056210](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328053056210.png)

Dubbo提供Compiler的实现有JavassistCompiler（默认实现）和JdkCompiler两种。

Dubbo框架会为每个扩展接口生成其对应的适配器类的源码，然后选择具体的动态编译类的扩展实现对源码进行编译以生成适配器类的Class对象，然后就可以调用Class对象的newInstance（）方法生成扩展接口对应的适配器类的实例。

### Dubbo增强SPI

SPI 全称为 Service Provider Interface，是一种服务发现机制。SPI 的本质是将接口实现类的全限定名配置在文件中，并由服务加载器读取配置文件，加载实现类。这样可以在运行时，动态为接口替换实现类。正因此特性，我们可以很容易的通过 SPI 机制为我们的程序提供拓展功能。SPI 机制在第三方框架中也有所应用，比如 Dubbo 就是通过 SPI 机制加载所有的组件。不过，Dubbo 并未使用 Java 原生的 SPI 机制，而是对其进行了增强，使其能够更好的满足需求。

JDK标准SPI

Dubbo增强的SPI功能是从JDK标准SPI演化而来的，所以有必要先讲讲标准SPI的原理。

JDK中的SPI是面向接口编程的，服务规则提供者会在JRE的核心API里提供服务访问接口，而具体实现则由其他开发商提供。

例如，如果规范制定者在rt.jar包里定义了数据库的驱动接口java.sql.Driver，那么MySQL实现的开发商则会在MySQL的驱动包的META-INF/services文件夹下建立名称为java.sql.Driver的文件，文件内容就是MySQL对java.sql.Driver接口的实现类。

Dubbo的扩展点加载机制是基于JDK标准的SPI扩展机制增强而来的，Dubbo解决了JDK标准的SPI的以下问题：

· JDK标准的SPI会一次性实例化扩展点的所有实现，如果有些扩展实现初始化很耗时，但又没用上，那么加载就很浪费资源。

· 如果扩展点加载失败，是不会友好地向用户通知具体异常的。比如：对于JDK标准的ScriptEngine来说，如果Ruby ScriptEngine因为所依赖的jruby.jar不存在，导致RubyScriptEngine类加载失败，那么这个失败原因就被隐藏了，当用户执行Ruby脚本时，会报空指针异常，而不是报Ruby ScriptEngine不存在。

· 增加了对扩展点IoC和AOP的支持，一个扩展点可以直接使用setter（）方法注入其他扩展点，也可以对扩展点使用Wrapper类进行功能增强。

### Dubbo使用JavaAssist减少反射调用开销

Dubbo会给每个服务提供者的实现类生产一个Wrapper类，这个Wrapper类里面最终调用服务提供者的接口实现类，Wrapper类的存在是为了减少反射的调用。当服务提供方收到消费方发来的请求后，需要根据消费者传递过来的方法名和参数反射调用服务提供者的实现类，而反射本身是有性能开销的，Dubbo把每个服务提供者的实现类通过JavaAssist包装为一个Wrapper类以减少反射调用开销。

## 远程服务发布与引用

### Dubbo服务发布端启动流程

![image-20200428095058253](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200428095058253.png)

服务提供方需要使用ServiceConfig API发布服务，具体来说就是执行图3.1中步骤1的export（）方法来激活发布服务。export（）方法的核心代码如下：通过上面的代码可知，Dubbo的延迟发布是通过使用ScheduledExecutorService来实现的，可以通过调用ServiceConfig的setDelay（Integer delay）方法来设置延迟发布时间。

如果没有设置延迟时间，则直接调用doExport（）方法发布服务；如果设置了延迟发布，则等时间过期后调用doExport（）方法来发布服务。

图3.1中的步骤2主要是根据ServiceConfig里面的属性进行合法性检查，这里我们主要看其内部最后调用的doExportUrls（）方法。

图3.1中的步骤4通过调用loadRegistries（）方法加载所有的服务注册中心对象，在Dubbo中，一个服务可以被注册到多个服务注册中心。

图3.1中的步骤5是在doExportUrlsFor1Protocol（）方法内部首先把参数封装为URL（在Dubbo里会把所有参数封装到一个URL里），然后具体执行服务导出，其核心代码如下：代码1解析MethodConfig对象设置的方法级别的配置并保存到参数map中；代码2用来判断调用类型，如果为泛型调用，则设置泛型类型（true、nativejava或bean方式）；代码5用来导出服务，Dubbo服务导出分本地导出与远程导出，本地导出使用了injvm协议，是一个伪协议，它不开启端口，不发起远程调用，只在JVM内直接关联，但执行Dubbo的Filter链；在默认情况下，Dubbo同时支持本地导出与远程导出协议，可以通过ServiceConfig的setScope（）方法设置，其中配置为none表示不导出服务，为remote表示只导出远程服务，为local表示只导出本地服务。

图3.1中步骤10的RegistryProtocol中的export（）方法通过步骤11的doLocalExport启动了NettyServer进行监听服务，步骤12、步骤13则将当前服务注册到服务注册中心。到这里就完成了2.2节中谈到的Invoker到Exporter的转换。

### Dubbo服务提供方如何处理请求

我们先看看当消费端发起TCP链接并完成后，在接收消费端发来的请求时，服务提供方是如何处理的，其处理时序图如图3.6所示。

![image-20200428095322589](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200428095322589.png)

让我们看看图3.6中的connected部分（步骤1～步骤11），消费端发起TCP链接并完成后，服务提供方的NettyServer的connected方法会被激活，该方法的执行是在Netty的I/O线程上执行的，为了可以及时释放I/O线程，Netty默认的线程模型为All，正如在第7章所介绍的，所有消息都派发到Dubbo内部的业务线程池，这些消息包括请求事件、响应事件、连接事件、断开事件、心跳事件等，这里对应的是AllChannelHandler类把I/O线程接收到的所有消息包装为ChannelEventRunnable任务并都投递到了线程池里。

图3.6中的received部分（步骤12～步骤22）类似于connected，received事件被投递到线程池后进行异步处理。线程池任务被激活后调用了HeaderExchangeHandler的received（）方法。

### Dubbo服务消费方启动流程剖析

![image-20200428095520335](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200428095520335.png)

我们提到服务消费方需要使用ReferenceConfig API来消费服务，这里就是执行图3.8中步骤1的get（）方法来生成远程调用代理类。get（）方法首先会执行init（）方法：其中，checkMock（）方法用来检查设置的mock是否正确，然后通过调用createProxy（）方法来创建代理类。

## Dubbo集群容错与负载均衡策略

### Dubbo集群容错策略

当服务消费方调用服务提供方的服务出现错误时，Dubbo提供了多种容错方案，默认模式为Failover Cluster，也就是失败重试。

下面让我们看看Dubbo提供的集群容错模式。

· Failover Cluster：失败重试

当服务消费方调用服务提供者失败后，会自动切换到其他服务提供者服务器进行重试，这通常用于读操作或者具有幂等的写操作。需要注意的是，重试会带来更长延迟。可以通过retries="2"来设置重试次数（不含第1次）。

可以使用＜dubbo：reference retries="2"/＞来进行接口级别配置的重试次数，当服务消费方调用服务失败后，此例子会再重试两次，也就是说最多会做3次调用，这里的配置对该接口的所有方法生效。

· Failfast Cluster：快速失败

当服务消费方调用服务提供者失败后，立即报错，也就是只调用一次。通常，这种模式用于非幂等性的写操作。

· Failsafe Cluster：安全失败

当服务消费者调用服务出现异常时，直接忽略异常。这种模式通常用于写入审计日志等操作。

· Failback Cluster：失败自动恢复

服务消费端调用服务出现异常后，在后台记录失败的请求，并按照一定的策略后期再进行重试。这种模式通常用于消息通知操作。

· Forking Cluster：并行调用

当消费方调用一个接口方法后，Dubbo Client会并行调用多个服务提供者的服务，只要其中有一个成功即返回。这种模式通常用于实时性要求较高的读操作，但需要浪费更多服务资源。如下代码可通过forks="4"来设置最大并行数。

· Broadcast Cluster：广播调用

当消费者调用一个接口方法后，Dubbo Client会逐个调用所有服务提供者，任意一台服务器调用异常则这次调用就标志失败。这种模式通常用于通知所有提供者更新缓存或日志等本地资源信息。

###  Dubbo负载均衡策略

Dubbo提供的负载均衡策略有如下几种。

· Random LoadBalance：随机策略。按照概率设置权重，比较均匀，并且可以动态调节提供者的权重。

· RoundRobin LoadBalance：轮循策略。轮循，按公约后的权重设置轮循比率。会存在执行比较慢的服务提供者堆积请求的情况，比如一个机器执行得非常慢，但是机器没有宕机（如果宕机了，那么当前机器会从ZooKeeper的服务列表中删除），当很多新的请求到达该机器后，由于之前的请求还没处理完，会导致新的请求被堆积，久而久之，消费者调用这台机器上的所有请求都会被阻塞。

· LeastActive LoadBalance：最少活跃调用数。如果每个提供者的活跃数相同，则随机选择一个。在每个服务提供者里维护着一个活跃数计数器，用来记录当前同时处理请求的个数，也就是并发处理任务的个数。这个值越小，说明当前服务提供者处理的速度越快或者当前机器的负载比较低，所以路由选择时就选择该活跃度最底的机器。如果一个服务提供者处理速度很慢，由于堆积，那么同时处理的请求就比较多，也就是说活跃调用数目较大（活跃度较高），这时，处理速度慢的提供者将收到更少的请求。

· ConsistentHash LoadBalance一致性Hash策略。一致性Hash，可以保证相同参数的请求总是发到同一提供者，当某一台提供者机器宕机时，原本发往该提供者的请求，将基于虚拟节点平摊给其他提供者，这样就不会引起剧烈变动。

## Dubbo线程模型与线程池策略

### Dubbo的线程模型概述

Dubbo默认的底层网络通信使用的是Netty，服务提供方NettyServer使用两级线程池，其中EventLoopGroup（boss）主要用来接收客户端的链接请求，并把完成TCP三次握手的连接分发给EventLoopGroup（worker）来处理，我们把boss和worker线程组称为I/O线程。

根据请求的消息类是被I/O线程处理还是被业务线程池处理，Dubbo提供了下面几种线程模型。

· all（AllDispatcher类）：所有消息都派发到业务线程池，这些消息包括请求、响应、连接事件、断开事件、心跳事件等，如图7.1所示。

![image-20200328054911029](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328054911029.png)

· direct（DirectDispatcher类）：所有消息都不派发到业务线程池，全部在IO线程上直接执行，如图7.2所示。

![image-20200328054921963](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328054921963.png)

· message（MessageOnlyDispatcher类）：只有请求响应消息派发到业务线程池，其他消息如连接事件、断开事件、心跳事件等，直接在I/O线程上执行，如图7.3所示。

![image-20200328054935867](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328054935867.png)

· execution（ExecutionDispatcher类）：只把请求类消息派发到业务线程池处理，但是响应、连接事件、断开事件、心跳事件等消息直接在I/O线程上执行，如图7.4所示。

![image-20200328055016470](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328055016470.png)

· connection（ConnectionOrderedDispatcher类）：在I/O线程上将连接事件、断开事件放入队列，有序地逐个执行，其他消息派发到业务线程池处理，如图7.5所示。

![image-20200328055031233](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328055031233.png)

### Dubbo的线程池策略

讲解Dubbo线程模型时提到，为了尽量早地释放Netty的I/O线程，某些线程模型会把请求投递到线程池进行异步处理，那么这里所谓的线程池是什么样的线程池呢？其实这里的线程池ThreadPool也是一个扩展接口SPI，Dubbo提供了该扩展接口的一些实现，具体如下。

· FixedThreadPool：创建一个具有固定个数线程的线程池。

· LimitedThreadPool：创建一个线程池，这个线程池中的线程个数随着需要量动态增加，但是数量不超过配置的阈值。另外，空闲线程不会被回收，会一直存在。

· EagerThreadPool：创建一个线程池，在这个线程池中，当所有核心线程都处于忙碌状态时，将创建新的线程来执行新任务，而不是把任务放入线程池阻塞队列。

· CachedThreadPool：创建一个自适应线程池，当线程空闲1分钟时，线程会被回收；当有新请求到来时，会创建新线程。

## Dubbo并发控制

### 服务消费端并发控制

在客户端并发控制中，如果当激活并发量达到指定值后，当前客户端请求线程会被挂起。如果在等待超时期间激活并发请求量少了，那么阻塞的线程会被激活，然后发送请求到服务提供方；如果等待超时了，则直接抛出异常，这时服务根本就没有发送到服务提供方服务器。

### 服务提供端并发控制

服务提供方在设置并发数量后，如果同时请求数量大于设置的executes值，则会直接抛出异常。

##  Dubbo协议与网络传输

### Dubbo协议

在TCP协议栈中，每层协议都有自己的协议报文格式，比如TCP是网络七层模型中的传输层，是TCP协议报文格式；在TCP上层是应用层（应用层协议常见的有HTTP协议等），Dubbo协议作为建立在TCP之上的一种应用层协议，自然也有自己的协议报文格式。Dubbo协议也是参考了TCP协议栈中的协议，协议内容由header和body两部分组成，其结构如图13.1所示。

![image-20200328055617701](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328055617701.png)

![image-20200328055630704](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200328055630704.png)

### 协议详情

- Magic - Magic High & Magic Low (16 bits)

  标识协议版本号，Dubbo 协议：0xdabb

- Req/Res (1 bit)

  标识是请求或响应。请求： 1; 响应： 0。

- 2 Way (1 bit)

  仅在 Req/Res 为1（请求）时才有用，标记是否期望从服务器返回值。如果需要来自服务器的返回值，则设置为1。

- Event (1 bit)

  标识是否是事件消息，例如，心跳事件。如果这是一个事件，则设置为1。

- Serialization ID (5 bit)

  标识序列化类型：比如 fastjson 的值为6。

- Status (8 bits)

  仅在 Req/Res 为0（响应）时有用，用于标识响应的状态。

  - 20 - OK
  - 30 - CLIENT_TIMEOUT
  - 31 - SERVER_TIMEOUT
  - 40 - BAD_REQUEST
  - 50 - BAD_RESPONSE
  - 60 - SERVICE_NOT_FOUND
  - 70 - SERVICE_ERROR
  - 80 - SERVER_ERROR
  - 90 - CLIENT_ERROR
  - 100 - SERVER_THREADPOOL_EXHAUSTED_ERROR

- Request ID (64 bits)

  标识唯一请求。类型为long。

- Data Length (32 bits)

  序列化后的内容长度（可变部分），按字节计数。int类型。

- Variable Part

  被特定的序列化类型（由序列化 ID 标识）序列化后，每个部分都是一个 byte [] 或者 byte

  - 如果是请求包 ( Req/Res = 1)，则每个部分依次为：
    - Dubbo version
    - Service name
    - Service version
    - Method name
    - Method parameter types
    - Method arguments
    - Attachments
  - 如果是响应包（Req/Res = 0），则每个部分依次为：
    - 返回值类型(byte)，标识从服务器端返回的值类型：
      - 返回空值：RESPONSE_NULL_VALUE 2
      - 正常响应值： RESPONSE_VALUE 1
      - 异常：RESPONSE_WITH_EXCEPTION 0
    - 返回值：从服务端返回的响应bytes

### Dubbo 协议的优缺点

优点

- 协议设计上很紧凑，能用 1 个 bit 表示的，不会用一个 byte 来表示，比如 boolean 类型的标识。
- 请求、响应的 header 一致，通过序列化器对 content 组装特定的内容，代码实现起来简单。

可以改进的点

- 类似于 http 请求，通过 header 就可以确定要访问的资源，而 Dubbo 需要涉及到用特定序列化协议才可以将服务名、方法、方法签名解析出来，并且这些资源定位符是 string 类型或者 string 数组，很容易转成 bytes，因此可以组装到 header 中。类似于 http2 的 header 压缩，对于 rpc 调用的资源也可以协商出来一个int来标识，从而提升性能，如果在`header`上组装资源定位符的话，该功能则更易实现。
- 通过 req/res 是否是请求后，可以精细定制协议，去掉一些不需要的标识和添加一些特定的标识。比如`status`,`twoWay`标识可以严格定制，去掉冗余标识。还有超时时间是作为 Dubbo 的 `attachment` 进行传输的，理论上应该放到请求协议的header中，因为超时是网络请求中必不可少的。提到 `attachment` ，通过实现可以看到 `attachment` 中有一些是跟协议 `content`中已有的字段是重复的，比如 `path`和`version`等字段，这些会增大协议尺寸。
- Dubbo 会将服务名`com.alibaba.middleware.hsf.guide.api.param.ModifyOrderPriceParam`，转换为`Lcom/alibaba/middleware/hsf/guide/api/param/ModifyOrderPriceParam;`，理论上是不必要的，最后追加一个`;`即可。
- Dubbo 协议没有预留扩展字段，没法新增标识，扩展性不太好，比如新增`响应上下文`的功能，只有改协议版本号的方式，但是这样要求客户端和服务端的版本都进行升级，对于分布式场景很不友好。



