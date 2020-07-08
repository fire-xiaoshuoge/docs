

## Spring Boot

### Springboot自动配置原理

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication .class, args);
    }
}
```

我们看到，MyApplication作为入口类，入口类中有一个main方法，这个方法其实就是一个标准的Java应用的入口方法，一般在main方法中使用SpringApplication.run()来启动整个应用。值得注意的是，这个入口类要使用@SpringBootApplication注解声明，它是SpringBoot的核心注解。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
    // 略
}
```

这个注解里面，最主要的就是@EnableAutoConfiguration，这么直白的名字，一看就知道它要开启自动配置，SpringBoot要开始骚了，于是默默进去@EnableAutoConfiguration的源码。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
    // 略
}
```

可以看到，在@EnableAutoConfiguration注解内使用到了@import注解来完成导入配置的功能，而EnableAutoConfigurationImportSelector内部则是使用了SpringFactoriesLoader.loadFactoryNames方法进行扫描具有META-INF/spring.factories文件的jar包。下面是1.5.8.RELEASE实现源码：

```java
 @Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    try {
          AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
                    .loadMetadata(this.beanClassLoader);
          AnnotationAttributes attributes = getAttributes(annotationMetadata);
          //扫描具有META-INF/spring.factories文件的jar包
          List<String> configurations = getCandidateConfigurations(annotationMetadata,
                    attributes);
          //去重
          configurations = removeDuplicates(configurations);
          //排序
          configurations = sort(configurations, autoConfigurationMetadata);
           //删除需要排除的类
         Set<String> exclusions = getExclusions(annotationMetadata, attributes);
         checkExcludedClasses(configurations, exclusions);
         configurations.removeAll(exclusions);
         configurations = filter(configurations, autoConfigurationMetadata);
         fireAutoConfigurationImportEvents(configurations, exclusions);
         return configurations.toArray(new String[configurations.size()]);
    }
    catch (IOException ex) {
        throw new IllegalStateException(ex);
    }
}
```

```java
//加载spring.factories实现
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
        AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
            getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
    Assert.notEmpty(configurations,
            "No auto configuration classes found in META-INF/spring.factories. If you "
                      + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

任何一个springboot应用，都会引入spring-boot-autoconfigure，而spring.factories文件就在该包下面。spring.factories文件是Key=Value形式，多个Value时使用,隔开，该文件中定义了关于初始化，监听器等信息，而真正使自动配置生效的key是org.springframework.boot.autoconfigure.EnableAutoConfiguration，如下所示：

```java
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
//省略
```

上面的EnableAutoConfiguration配置了多个类，这些都是Spring Boot中的自动配置相关类；在启动过程中会解析对应类配置信息。每个Configuation类都定义了相关bean的实例化配置。都说明了哪些bean可以被自动配置，什么条件下可以自动配置，并把这些bean实例化出来。如果我们自定义了一个starter的话，也要在该starter的jar包中提供 spring.factories文件，并且为其配置org.springframework.boot.autoconfigure.EnableAutoConfiguration对应的配置类。所有框架的自动配置流程基本都是一样的，判断是否引入框架，获取配置参数，根据配置参数初始化框架相应组件。下面的初始化数据源的部分源码：

```java
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@EnableConfigurationProperties(DataSourceProperties.class)
@Import({ Registrar.class, DataSourcePoolMetadataProvidersConfiguration.class })
public class DataSourceAutoConfiguration {

    private static final Log logger = LogFactory
            .getLog(DataSourceAutoConfiguration.class);

    @Bean
    @ConditionalOnMissingBean
    public DataSourceInitializer dataSourceInitializer(DataSourceProperties properties,
            ApplicationContext applicationContext) {
        return new DataSourceInitializer(properties, applicationContext);
    }
    //略
}
```

我们可以看到自动化配置代码中有很多我们之前没有用到的注解配置，下面一一介绍。

> @Configuration：这个配置就不用多做解释了，我们一直在使用
> @EnableConfigurationProperties：这是一个开启使用配置参数的注解，value值就是我们配置实体参数映射的ClassType，将配置实体作为配置来源。
> 以下为SpringBoot内置条件注解：
> @ConditionalOnBean：当SpringIoc容器内存在指定Bean的条件
> @ConditionalOnClass：当SpringIoc容器内存在指定Class的条件
> @ConditionalOnExpression：基于SpEL表达式作为判断条件
> @ConditionalOnJava：基于JVM版本作为判断条件
> @ConditionalOnJndi：在JNDI存在时查找指定的位置
> @ConditionalOnMissingBean：当SpringIoc容器内不存在指定Bean的条件
> @ConditionalOnMissingClass：当SpringIoc容器内不存在指定Class的条件
> @ConditionalOnNotWebApplication：当前项目不是Web项目的条件
> @ConditionalOnProperty：指定的属性是否有指定的值
> @ConditionalOnResource：类路径是否有指定的值
> @ConditionalOnSingleCandidate：当指定Bean在SpringIoc容器内只有一个，或者虽然有多个但是指定首选的Bean
> @ConditionalOnWebApplication：当前项目是Web项目的条件
> 以上注解都是元注解@Conditional演变而来的，根据不用的条件对应创建以上的具体条件注解。
>
> **结论：**
>
> 1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
> 2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
> 3. 以前我们需要自己配置的东西 ， 自动配置类都帮我们解决了
> 4. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
> 5. **它将所有需要导入的组件以全类名的方式返回 ， 这些组件就会被添加到容器中 ；**
> 6. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
> 7. **有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；**

### SpringApplication

这个类主要做了以下四件事情

1. 推断应用的类型是普通的项目还是Web项目
2. 查找并加载所有可用初始化器 ， 设置到initializers属性中
3. 找出所有的应用程序监听器，设置到listeners属性中
4. 推断并设置main方法的定义类，找到运行的主类

### run方法

<img src="https://blog.kuangstudy.com/usr/uploads/2019/10/700745206.png" alt="1353149994.png"  />

###  @SpringBootApplication

事实上它是一个复合注解，等价于@ComponentScan、@SpringBootConfiguration和@EnableAutoConfiguration。

@ComponentScan继承于@Configuration，用来表示程序启动时将自动扫描当前包及子包下的所有类。

@SpringBootConfiguration表示将会把本类里声明的一个或多个以@Bean注解标记的实例纳入Spring容器中。

 @EnableAutoConfiguration用来表示程序启动时将自动地装载springboot默认配置文件。

## Spring Cloud

### Spring Cloud

Spring Cloud是一个基于Spring Boot实现的云原生应用开发工具，它为基于JVM的云原生应用开发中涉及的配置管理、服务发现、熔断器、智能路由、微代理、控制总线、分布式会话和集群状态管理等操作提供了一种简单的开发方式。

### Spring Cloud常用组件归纳表

![image-20200220215946862](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200220215946862.png)

## Spring Cloud Eureka

### Eureka

Eureka是Netflix开发的服务发现框架，本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，以实现SpringCloud的服务发现功能。

Eureka包含两个组件：Eureka Server和Eureka Client。

Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就是一个内置的、使用轮询(round-robin)负载算法的负载均衡器。

在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。

Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、客户端缓存等机制，确保了系统的高可用性、灵活性和可伸缩性。

### Eureka 核心概念

![img](https://img-blog.csdnimg.cn/20190703102014756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3F3ZTg2MzE0,size_16,color_FFFFFF,t_70)

**Eureka Server：注册中心服务端**

注册中心服务端主要对外提供了三个功能：

**服务注册**
服务提供者启动时，会通过 Eureka Client 向 Eureka Server 注册信息，Eureka Server 会存储该服务的信息，Eureka Server 内部有二层缓存机制来维护整个注册表

**提供注册表**
服务消费者在调用服务时，如果 Eureka Client 没有缓存注册表的话，会从 Eureka Server 获取最新的注册表

**同步状态**
Eureka Client 通过注册、心跳机制和 Eureka Server 同步当前客户端的状态。

**Eureka Client：注册中心客户端**
Eureka Client 是一个 Java 客户端，用于简化与 Eureka Server 的交互。Eureka Client 会拉取、更新和缓存 Eureka Server 中的信息。因此当所有的 Eureka Server 节点都宕掉，服务消费者依然可以使用缓存中的信息找到服务提供者，但是当服务有更改的时候会出现信息不一致。

**Register: 服务注册**
服务的提供者，将自身注册到注册中心，服务提供者也是一个 Eureka Client。当 Eureka Client 向 Eureka Server 注册时，它提供自身的元数据，比如 IP 地址、端口，运行状况指示符 URL，主页等。

**Renew: 服务续约**
Eureka Client 会每隔 30 秒发送一次心跳来续约。 通过续约来告知 Eureka Server 该 Eureka Client 运行正常，没有出现问题。 默认情况下，如果 Eureka Server 在 90 秒内没有收到 Eureka Client 的续约，Server 端会将实例从其注册表中删除，此时间可配置，一般情况不建议更改。

**服务续约的两个重要属性**

```java
服务续约任务的调用间隔时间，默认为30秒
eureka.instance.lease-renewal-interval-in-seconds=30

服务失效的时间，默认为90秒。
eureka.instance.lease-expiration-duration-in-seconds=90
12345
```

**Eviction 服务剔除**
当 Eureka Client 和 Eureka Server 不再有心跳时，Eureka Server 会将该服务实例从服务注册列表中删除，即服务剔除。

**Cancel: 服务下线**
Eureka Client 在程序关闭时向 Eureka Server 发送取消请求。 发送请求后，该客户端实例信息将从 Eureka Server 的实例注册表中删除。该下线请求不会自动完成，它需要调用以下内容：

```java
DiscoveryManager.getInstance().shutdownComponent()；
1
```

**GetRegisty: 获取注册列表信息**
Eureka Client 从服务器获取注册表信息，并将其缓存在本地。客户端会使用该信息查找其他服务，从而进行远程调用。该注册列表信息定期（每30秒钟）更新一次。每次返回注册列表信息可能与 Eureka Client 的缓存信息不同，Eureka Client 自动处理。

如果由于某种原因导致注册列表信息不能及时匹配，Eureka Client 则会重新获取整个注册表信息。 Eureka Server 缓存注册列表信息，整个注册表以及每个应用程序的信息进行了压缩，压缩内容和没有压缩的内容完全相同。Eureka Client 和 Eureka Server 可以使用 JSON/XML 格式进行通讯。在默认情况下 Eureka Client 使用压缩 JSON 格式来获取注册列表的信息。

**获取服务是服务消费者的基础，所以必有两个重要参数需要注意：**

```java
# 启用服务消费者从注册中心拉取服务列表的功能
eureka.client.fetch-registry=true

# 设置服务消费者从注册中心拉取服务列表的间隔
eureka.client.registry-fetch-interval-seconds=30
12345
```

**Remote Call: 远程调用**
当 Eureka Client 从注册中心获取到服务提供者信息后，就可以通过 Http 请求调用对应的服务；服务提供者有多个时，Eureka Client 客户端会通过 Ribbon 自动进行负载均衡。

### 自我保护机制

默认情况下，如果 Eureka Server 在一定的 90s 内没有接收到某个微服务实例的心跳，会注销该实例。但是在微服务架构下服务之间通常都是跨进程调用，网络通信往往会面临着各种问题，比如微服务状态正常，网络分区故障，导致此实例被注销。

固定时间内大量实例被注销，可能会严重威胁整个微服务架构的可用性。为了解决这个问题，Eureka 开发了自我保护机制，那么什么是自我保护机制呢？

Eureka Server 在运行期间会去统计心跳失败比例在 15 分钟之内是否低于 85%，如果低于 85%，Eureka Server 即会进入自我保护机制。

**Eureka Server 触发自我保护机制后，页面会出现提示：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190703103514416.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3F3ZTg2MzE0,size_16,color_FFFFFF,t_70)

**Eureka Server 进入自我保护机制，会出现以下几种情况：**
(1 Eureka 不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
(2 Eureka 仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(即保证当前节点依然可用)
(3 当网络稳定时，当前实例新的注册信息会被同步到其它节点中

Eureka 自我保护机制是为了防止误杀服务而提供的一个机制。当个别客户端出现心跳失联时，则认为是客户端的问题，剔除掉客户端；当 Eureka 捕获到大量的心跳失败时，则认为可能是网络问题，进入自我保护机制；当客户端心跳恢复时，Eureka 会自动退出自我保护机制。

如果在保护期内刚好这个服务提供者非正常下线了，此时服务消费者就会拿到一个无效的服务实例，即会调用失败。对于这个问题需要服务消费者端要有一些容错机制，如重试，断路器等。

**通过在 Eureka Server 配置如下参数，开启或者关闭保护机制，生产环境建议打开：**

```java
eureka.server.enable-self-preservation=true
```

### Eureka 集群原理

再来看看 Eureka 集群的工作原理。我们假设有三台 Eureka Server 组成的集群，第一台 Eureka Server 在北京机房，另外两台 Eureka Server 在深圳和西安机房。这样三台 Eureka Server 就组建成了一个跨区域的高可用集群，只要三个地方的任意一个机房不出现问题，都不会影响整个架构的稳定性。

![img](https://img-blog.csdnimg.cn/20190703103823398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3F3ZTg2MzE0,size_16,color_FFFFFF,t_70)

从图中可以看出 Eureka Server 集群相互之间通过 Replicate 来同步数据，相互之间不区分主节点和从节点，所有的节点都是平等的。在这种架构中，节点通过彼此互相注册来提高可用性，每个节点需要添加一个或多个有效的 serviceUrl 指向其他节点。

如果某台 Eureka Server 宕机，Eureka Client 的请求会自动切换到新的 Eureka Server 节点。当宕机的服务器重新恢复后，Eureka 会再次将其纳入到服务器集群管理之中。当节点开始接受客户端请求时，所有的操作都会进行节点间复制，将请求复制到其它 Eureka Server 当前所知的所有节点中。

另外 Eureka Server 的同步遵循着一个非常简单的原则：只要有一条边将节点连接，就可以进行信息传播与同步。所以，如果存在多个节点，只需要将节点之间两两连接起来形成通路，那么其它注册中心都可以共享信息。每个 Eureka Server 同时也是 Eureka Client，多个 Eureka Server 之间通过 P2P 的方式完成服务注册表的同步。

Eureka Server 集群之间的状态是采用异步方式同步的，所以不保证节点间的状态一定是一致的，不过基本能保证最终状态是一致的。

**Eureka 分区**
Eureka 提供了 Region 和 Zone 两个概念来进行分区，这两个概念均来自于亚马逊的 AWS:
**region**：可以理解为地理上的不同区域，比如亚洲地区，中国区或者深圳等等。没有具体大小的限制。根据项目具体的情况，可以自行合理划分 region。
**zone**：可以简单理解为 region 内的具体机房，比如说 region 划分为深圳，然后深圳有两个机房，就可以在此 region 之下划分出 zone1、zone2 两个 zone。

上图中的 us-east-1c、us-east-1d、us-east-1e 就代表了不同的 Zone。Zone 内的 Eureka Client 优先和 Zone 内的 Eureka Server 进行心跳同步，同样调用端优先在 Zone 内的 Eureka Server 获取服务列表，当 Zone 内的 Eureka Server 挂掉之后，才会从别的 Zone 中获取信息。

**Eurka 保证 AP**

Eureka Server 各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而 Eureka Client 在向某个 Eureka 注册时，如果发现连接失败，则会自动切换至其它节点。只要有一台 Eureka Server 还在，就能保证注册服务可用(保证可用性)，只不过查到的信息可能不是最新的(不保证强一致性)。

### Eurka 工作流程

1、Eureka Server 启动成功，等待服务端注册。在启动过程中如果配置了集群，集群之间定时通过 Replicate 同步注册表，每个 Eureka Server 都存在独立完整的服务注册表信息

2、Eureka Client 启动时根据配置的 Eureka Server 地址去注册中心注册服务

3、Eureka Client 会每 30s 向 Eureka Server 发送一次心跳请求，证明客户端服务正常

4、当 Eureka Server 90s 内没有收到 Eureka Client 的心跳，注册中心则认为该节点失效，会注销该实例

5、单位时间内 Eureka Server 统计到有大量的 Eureka Client 没有上送心跳，则认为可能为网络异常，进入自我保护机制，不再剔除没有上送心跳的客户端

6、当 Eureka Client 心跳请求恢复正常之后，Eureka Server 自动退出自我保护模式

7、Eureka Client 定时全量或者增量从注册中心获取服务注册表，并且将获取到的信息缓存到本地

8、服务调用时，Eureka Client 会先从本地缓存找寻调取的服务。如果获取不到，先从注册中心刷新注册表，再同步到本地缓存

9、Eureka Client 获取到目标服务器信息，发起服务调用

10、Eureka Client 程序关闭时向 Eureka Server 发送取消请求，Eureka Server 将实例从注册表中删除

### Eureka REST API接口列表

![image-20200225220914468](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225220914468.png)

### Eureka Client参数说明

![image-20200225221120346](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221120346.png)

![image-20200225221154150](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221154150.png)

![image-20200225221223582](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221223582.png)

### Eureka Server端参数说明

![image-20200225221252567](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221252567.png)

![image-20200225221311267](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221311267.png)



## Spring Cloud Ribbon

### Ribbon

Spring Cloud Ribbon是一个基于HTTP和TCP的客户端负载均衡工具，它基于Netflix Ribbon实现。通过Spring Cloud的封装，可以让我们轻松地将面向服务的REST模版请求自动转换成客户端负载均衡的服务调用。Spring Cloud Ribbon虽然只是一个工具类框架，它不像服务注册中心、配置中心、API网关那样需要独立部署，但是它几乎存在于每一个Spring Cloud构建的微服务和基础设施中。因为微服务间的调用，API网关的请求转发等内容，实际上都是通过Ribbon来实现的，包括后续我们将要介绍的Feign，它也是基于Ribbon实现的工具。所以，对Spring Cloud Ribbon的理解和使用，对于我们使用Spring Cloud来构建微服务非常重要。

### 客户端负载均衡

 负载均衡在系统架构中是一个非常重要，并且是不得不去实施的内容。因为负载均衡是对系统的高可用、网络压力的缓解和处理能力扩容的重要手段之一。我们通常所说的负载均衡都指的是服务端负载均衡，其中分为硬件负载均衡和软件负载均衡。硬件负载均衡主要通过在服务器节点之间按照专门用于负载均衡的设备，比如F5等；而软件负载均衡则是通过在服务器上安装一些用于负载均衡功能或模块等软件来完成请求分发工作，比如Nginx等。不论采用硬件负载均衡还是软件负载均衡，只要是服务端都能以类似下图的架构方式构建起来：

![img](https:////upload-images.jianshu.io/upload_images/8796251-20be966344ffe722.png?imageMogr2/auto-orient/strip|imageView2/2/w/786/format/webp)

负载均衡架构图

​    硬件负载均衡的设备或是软件负载均衡的软件模块都会维护一个下挂可用的服务端清单，通过心跳检测来剔除故障的服务端节点以保证清单中都是可以正常访问的服务端节点。当客户端发送请求到负载均衡设备的时候，该设备按某种算法（比如线性轮询、按权重负载、按流量负载等）从维护的可用服务端清单中取出一台服务端端地址，然后进行转发。

​    而客户端负载均衡和服务端负载均衡最大的不同点在于上面所提到服务清单所存储的位置。在客户端负载均衡中，所有客户端节点都维护着自己要访问的服务端清单，而这些服务端端清单来自于服务注册中心，比如上一章我们介绍的Eureka服务端。同服务端负载均衡的架构类似，在客户端负载均衡中也需要心跳去维护服务端清单的健康性，默认会创建针对各个服务治理框架的Ribbon自动化整合配置，比如Eureka中的org.springframework.cloud.netflix.ribbon.eureka.RibbonEurekaAutoConfiguration，Consul中的org.springframework.cloud.consul.discovery.RibbonConsulAutoConfiguration。在实际使用的时候，我们可以通过查看这两个类的实现，以找到它们的配置详情来帮助我们更好地使用它。

​    通过Spring Cloud Ribbon的封装，我们在微服务架构中使用客户端负载均衡调用非常简单，只需要如下两步：

​    ▪️服务提供者只需要启动多个服务实例并注册到一个注册中心或是多个相关联的服务注册中心。

​    ▪️服务消费者直接通过调用被@LoadBalanced注解修饰过的RestTemplate来实现面向服务的接口调用。

​    这样，我们就可以将服务提供者的高可用以及服务消费者的负载均衡调用一起实现了。

### 负载均衡策略

通过上面的源码解读，我们已经对Ribbon实现的负载均衡器以及其中包含的服务实例过滤器、服务实例信息的存储对象、区域的信息快照等都有了深入的认识和理解，但是对于负载均衡器中的服务实例选择策略只是讲解了几个默认实现的内容，而对于IRule的其他实现还没有详细解读，下面我们来看看在Ribbon中都提供了哪些负载均衡的策略实现。

​    如下图所示，可以看到在Ribbon中实现了非常多的选择策略，其中也包含了我们在前面内容中提到过的RoudRobinRule和ZoneAvoidanceRule。下面我们来详细可读一下IRule接口的各个实现。

![img](https:////upload-images.jianshu.io/upload_images/8796251-7e0d94929421c90b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

ribbon的负载均衡策略

▪️**AbstractLoadBalancerRule**

​    负载均衡策略的抽象类，在该抽象类中定义了负载均衡器ILoadBalancer对象，该对象能够在具体实现选择服务策略时，获取到一些负载均衡器中维护的信息来作为分配依据，并以此设计一些算法来实现针对特定场景的高效策略。

▪️**RandomRule**

​    该策略实现了从服务实例清单中随机选择一个服务实例的功能。具体的选择逻辑在一个while(server==null)循环之内，而根据选择逻辑的实现，正常情况下每次选择都应该选出一个服务实例，如果出现死循环获取不到服务实例，如果出现死循环获取不到服务实例时，则很有可能存在并发的Bug。

▪️**RoundRobinRule**

​    该策略实现了按照线性轮询的方式的方式一次选择每个服务实例的功能。它的具体实现如下，其详细结构与RandomRule非常类似。除了循环条件不同外，就是从可用列表中获取所谓的逻辑不通。从循环条件中，我们可以看到增加了一个count计数变量，该变量会在每次循环之后累加，也就是说，如果一直选择不到server超过10次，那么就会结束尝试，并打印一个警告信息No available alive servers after 10tries from load balancer : ... 。

▪️**RetryRule**

​    该策略实现了一个具备重试机制的实例选择功能。从下面的实现中我们可以看到，在其内部还定义了一个IRule对象，默认使用了RoudRobinRule实例。而在choose方法中则实现了对内部定义的策略进行反复尝试的策略，若期间能够选择到具体的服务实例就反悔，若选择不到就根据设置结束时间为阀值(maxRetryMillis参数定义的值+choose方法开始执行的时间戳)，当超过该阀值就返回null。

![img](https:////upload-images.jianshu.io/upload_images/8796251-5464ac396dea6e9d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

choose方法

▪️**WeightedResponseTimeRule**

​    该策略是对RoundRobinRule的扩展，增加了根据实例等运行情况来计算权重，并根据权重来挑选实例，以达到更优的分配效果，它的实现主要有三个核心内容。

​    **定时任务**

​    **权重计算**

​    **实例选择**

▪️**ClientConfigEnabledRoundRobinRule**

​    该策略较为特殊，我们一般不直接使用它，因为它本身并没有实现什么特殊的处理逻辑，正如下面的源码所示，在它的内部定义了一个RoundRobinRule策略，而choose函数的实现也正是使用了RoundRobinRule的线性轮询机制，所以它实现的功能实际上与RoundRobinRule相同，那么定义它有什么特殊的用处呢？

​    虽然我们不会直接使用该策略，但是通过继承该策略，默认的choose就实现了线性轮询机制，在子类中做一些高级策略时通常有可能会存在一些无法实施的情况，那么久可以用父类的实现作为备选。在后面中我们将继续介绍的高级策略均是基于ClientConfigEnabledRoundRobinRule的扩展。

![img](https:////upload-images.jianshu.io/upload_images/8796251-740f3ac95e01700a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

ClientConfigEnabledRoundRobinRule

▪️**BestAvailableRule**

​    该策略继承自ClientConfigEnabledRoundRobinRule，在实现中它注入了负载均衡器的统计对象LoadBalancerStats，同时在具体的choose算法中利用LoadBalancerStats保存的实例统计信息来选择满足要求的实例。从如下源码中我们可以看到，它通过遍历负载均衡器中维护的所有服务实例，会过滤掉故障的实例，并找出并发请求数最小的一个，所以该策略的特性是可选出最空闲的实例。

![img](https:////upload-images.jianshu.io/upload_images/8796251-534b33193135c5e7.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

​    同时，由于该算法的核心依据是统计对象LoadBalancerStats，当其为空的时候，该策略是无法执行的。所以从源码中我们可以看到，当loadBalancerStats为空的时候，它会采用父类的线性轮询策略，正如我们在介绍ClientConfigEnabledRoundRobinRule时那样，它的子类在无法满足实现高级策略的时候，可以使用线性轮询策略的特性。后面将要介绍的策略因为也都继承自ClientConfigEnabledRoundRobinRule，所以它们都会具有这样的特性。

▪️**PredicateBasedRule**

​    这是一个抽象策略，它也继承了ClientConfigEnabledRoundRobinRule，从其命名中可以猜出这是一个基于Predicate实现的策略，Predicate是Google Guava Collections工具对集合进行过滤掉条件接口。

​    Google Guava Collections是一个对Java Collections Framework增强和扩展的开源项目。虽然Java Collections Framework已经能够满足我们大多数抢空下使用集合的要求，但是当遇到一些特殊情况时，我们的代码会比较冗长且容易出错。Google Guava Collections可以帮助我们让集合操作代码更为简短精炼并大大增强代码的可读性。

▪️**AvailabilityFilteringRule**

​    该策略继承自上面介绍的抽象策略PredicateBasedRule，所以它也继承了“先过滤清单，再轮询选择”的的基本处理逻辑，其中过滤条件使用了AvailabilityPredicate。简单地说，该策略通过线性抽样的方式直接尝试寻找可用且较空闲的实例来使用，优化了父类每次都要遍历所有实例的开销。

▪️**ZoneAvoidanceRule**

​    该策略我们在介绍负载均衡器ZoneAwareLoadBalancer时已经提到过，它也是PredicateBasedRule的具体实现类，在之前的介绍中主要针对ZoneAvoidanceRule中用于选择Zone区域策略的一些静态函数，比如createSnapshot(LoadBalancerStats lbStats)、getAvailableZones(Map snapshot, double triggeringLoad,double triggeringBlackoutPercentage)。在这里我们将详细看看ZoneAvoidanceRule作为服务实例过滤条件的实现原理。从下面ZoneAvoidanceRule的源码片段中可以看到，它使用了CompositePredicate来进行服务实例清单的过滤。这是一个组合过来条件，在其构造函数中，它以ZoneAvoidanceRule为主过滤条件，AvailabilityPredicate为次过滤条件初始化了组合过滤条件的实例。

### Ribbon核心接口

![image-20200225221632831](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225221632831.png)

## Spring Cloud Feign

### Feign

feign是声明式的web service客户端，它让微服务之间的调用变得更简单了，类似controller调用service。Spring Cloud集成了Ribbon和Eureka，可在使用Feign时提供负载均衡的http客户端。

### 引入Feign

项目中使用了gradle作为依赖管理，maven类似。

```groovy
dependencies {
    //feign
    implementation('org.springframework.cloud:spring-cloud-starter-openfeign:2.0.2.RELEASE')
    //web
    implementation('org.springframework.boot:spring-boot-starter-web')
    //eureka client
    implementation('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client:2.1.0.M1')
    //test
    testImplementation('org.springframework.boot:spring-boot-starter-test')
}
```

因为feign底层是使用了ribbon作为负载均衡的客户端，而ribbon的负载均衡也是依赖于eureka 获得各个服务的地址，所以要引入eureka-client。

SpringbootApplication启动类加上@FeignClient注解，以及@EnableDiscoveryClient。

```java
@EnableFeignClients
@EnableDiscoveryClient
@SpringBootApplication
public class ProductApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProductApplication.class, args);
    }
}
```

yaml配置：

```yaml
server:
  port: 8082

#配置eureka
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    status-page-url-path: /info
    health-check-url-path: /health

#服务名称
spring:
  application:
    name: product
  profiles:
    active: ${boot.profile:dev}
#feign的配置，连接超时及读取超时配置
feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: basic
```

### Feign的使用

```java
@FeignClient(value = "CART")
public interface CartFeignClient {

    @PostMapping("/cart/{productId}")
    Long addCart(@PathVariable("productId")Long productId);
}
```

上面是最简单的feign client的使用，声明完为feign client后，其他spring管理的类，如service就可以直接注入使用了，例如：

```java
//这里直接注入feign client
@Autowired
private CartFeignClient cartFeignClient;

@PostMapping("/toCart/{productId}")
public ResponseEntity addCart(@PathVariable("productId") Long productId){
    Long result = cartFeignClient.addCart(productId);
    return ResponseEntity.ok(result);
}
```

可以看到，使用feign之后，我们调用eureka 注册的其他服务，在代码中就像各个service之间相互调用那么简单。

### FeignClient注解

| 属性名        | 默认值     | 作用                                                         | 备注                                        |
| ------------- | ---------- | ------------------------------------------------------------ | ------------------------------------------- |
| value         | 空字符串   | 调用服务名称，和name属性相同                                 |                                             |
| serviceId     | 空字符串   | 服务id，作用和name属性相同                                   | 已过期                                      |
| name          | 空字符串   | 调用服务名称，和value属性相同                                |                                             |
| url           | 空字符串   | 全路径地址或hostname，http或https可选                        |                                             |
| decode404     | false      | 配置响应状态码为404时是否应该抛出FeignExceptions             |                                             |
| configuration | {}         | 自定义当前feign client的一些配置                             | 参考FeignClientsConfiguration               |
| fallback      | void.class | 熔断机制，调用失败时，走的一些回退方法，可以用来抛出异常或给出默认返回数据。 | 底层依赖hystrix，启动类要加上@EnableHystrix |
| path          | 空字符串   | 自动给所有方法的requestMapping前加上前缀，类似与controller类上的requestMapping |                                             |
| primary       | true       |                                                              |                                             |

此外，还有qualifier及fallbackFactory，这里就不再赘述。

### 自定义处理返回的异常

这里贴上GitHub上openFeign的wiki给出的自定义errorDecoder例子。

```java
public class StashErrorDecoder implements ErrorDecoder {

    @Override
    public Exception decode(String methodKey, Response response) {
        if (response.status() >= 400 && response.status() <= 499) {
            //这里是给出的自定义异常
            return new StashClientException(
                    response.status(),
                    response.reason()
            );
        }
        if (response.status() >= 500 && response.status() <= 599) {
            //这里是给出的自定义异常
            return new StashServerException(
                    response.status(),
                    response.reason()
            );
        }
        //这里是其他状态码处理方法
        return errorStatus(methodKey, response);
    }
}
```

自定义好异常处理类后，要在@Configuration修饰的配置类中声明此类。

### 使用OKhttp发送request

Feign底层默认是使用jdk中的HttpURLConnection发送HTTP请求，feign也提供了OKhttp来发送请求，具体配置如下：

```java
feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: basic
  okhttp:
    enabled: true
  hystrix:
    enabled: true
```

### Feign原理简述

- 启动时，程序会进行包扫描，扫描所有包下所有@FeignClient注解的类，并将这些类注入到spring的IOC容器中。当定义的Feign中的接口被调用时，通过JDK的动态代理来生成RequestTemplate。
- RequestTemplate中包含请求的所有信息，如请求参数，请求URL等。
- RequestTemplate声场Request，然后将Request交给client处理，这个client默认是JDK的HTTPUrlConnection，也可以是OKhttp、Apache的HTTPClient等。
- 最后client封装成LoadBaLanceClient，结合ribbon负载均衡地发起调用。

详细原理请参考源码解析。

Feign、hystrix与retry的关系请参考[https://xli1224.github.io/2017/09/22/configure-feign/](https://links.jianshu.com/go?to=https%3A%2F%2Fxli1224.github.io%2F2017%2F09%2F22%2Fconfigure-feign%2F)

### Feign开启GZIP压缩

Spring Cloud Feign支持对请求和响应进行GZIP压缩，以提高通信效率。

application.yml配置信息如下：

```yaml
feign:
  compression:
    request: #请求
      enabled: true #开启
      mime-types: text/xml,application/xml,application/json #开启支持压缩的MIME TYPE
      min-request-size: 2048 #配置压缩数据大小的下限
    response: #响应
      enabled: true #开启响应GZIP压缩
```

注意：

由于开启GZIP压缩之后，Feign之间的调用数据通过二进制协议进行传输，返回值需要修改为ResponseEntity<byte[]>才可以正常显示，否则会导致服务之间的调用乱码。

示例如下：

```java
@PostMapping("/order/{productId}")
ResponseEntity<byte[]> addCart(@PathVariable("productId") Long productId);
```

### Feign Client配置方式

方式一：通过java bean 的方式指定。

@EnableFeignClients注解上有个defaultConfiguration属性，可以指定默认Feign Client的一些配置。

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration.class)
@EnableDiscoveryClient
@SpringBootApplication
@EnableCircuitBreaker
public class ProductApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProductApplication.class, args);
    }
}
```

DefaultFeignConfiguration内容：

```java
@Configuration
public class DefaultFeignConfiguration {

    @Bean
    public Retryer feignRetryer() {
        return new Retryer.Default(1000,3000,3);
    }
}
```

方式二：通过配置文件方式指定。

```yaml
feign:
  client:
    config:
      default:
        connectTimeout: 5000 #连接超时
        readTimeout: 5000 #读取超时
        loggerLevel: basic #日志等级
```

### 开启日志

日志配置和上述配置相同，也有两种方式。

方式一：通过java bean的方式指定

```java
@Configuration
public class DefaultFeignConfiguration {
    @Bean
    public Logger.Level feignLoggerLevel(){
        return Logger.Level.BASIC;
    }
}
```

方式二：通过配置文件指定

```yaml
logging:
  level:
    com.xt.open.jmall.product.remote.feignclients.CartFeignClient: debug
```

### GET的多参数传递

目前，feign不支持GET请求直接传递POJO对象的，目前解决方法如下：

1. 把POJO拆散城一个一个单独的属性放在方法参数中
2. 把方法参数编程Map传递
3. 使用GET传递@RequestBody，但此方式违反restful风格

介绍一个最佳实践，通过feign的拦截器来实现。

```java
@Component
@Slf4j
public class FeignCustomRequestInteceptor implements RequestInterceptor {

    @Autowired
    private ObjectMapper objectMapper;

    @Override
    public void apply(RequestTemplate template) {
        if (HttpMethod.GET.toString() == template.method() && template.body() != null) {
            //feign 不支持GET方法传输POJO 转换成json，再换成query
            try {
                Map<String, Collection<String>> map = objectMapper.readValue(template.bodyTemplate(), new TypeReference<Map<String, Collection<String>>>() {

                });
                template.body(null);
                template.queries(map);
            } catch (IOException e) {
                log.error("cause exception", e);
            }
        }
    }
```

## Spring Cloud Hystrix

### Hystrix

在分布式环境中，许多服务依赖项中的一些必然会失败。Hystrix是一个库，通过添加延迟容忍和容错逻辑，帮助你控制这些分布式服务之间的交互。Hystrix通过隔离服务之间的访问点、停止级联失败和提供回退选项来实现这一点，所有这些都可以提高系统的整体弹性。

### 雪崩效应常见场景

- 硬件故障：如服务器宕机，机房断电，光纤被挖断等。
- 流量激增：如异常流量，重试加大流量等。
- 缓存穿透：一般发生在应用重启，所有缓存失效时，以及短时间内大量缓存失效时。大量的缓存不命中，使请求直击后端服务，造成服务提供者超负荷运行，引起服务不可用。
- 程序BUG：如程序逻辑导致内存泄漏，JVM长时间FullGC等。
- 同步等待：服务间采用同步调用模式，同步等待造成的资源耗尽。

### Hystrix处理流程

![img](https://static.oschina.net/uploads/space/2018/0129/174915_Bvjy_2663573.png)

Hystrix整个工作流如下：

1. 构造一个 HystrixCommand或HystrixObservableCommand对象，用于封装请求，并在构造方法配置请求被执行需要的参数；
2. 执行命令，Hystrix提供了4种执行命令的方法，后面详述；
3. 判断是否使用缓存响应请求，若启用了缓存，且缓存可用，直接使用缓存响应请求。Hystrix支持请求缓存，但需要用户自定义启动；
4. 判断熔断器是否打开，如果打开，跳到第8步；
5. 判断线程池/队列/信号量是否已满，已满则跳到第8步；
6. 执行HystrixObservableCommand.construct()或HystrixCommand.run()，如果执行失败或者超时，跳到第8步；否则，跳到第9步；
7. 统计熔断器监控指标；
8. 走Fallback备用逻辑
9. 返回请求响应

### 执行命令的几种方法

Hystrix提供了4种执行命令的方法，execute()和queue() 适用于HystrixCommand对象，而observe()和toObservable()适用于HystrixObservableCommand对象。

**execute()**

以同步堵塞方式执行run()，只支持接收一个值对象。hystrix会从线程池中取一个线程来执行run()，并等待返回值。

**queue()**

以异步非阻塞方式执行run()，只支持接收一个值对象。调用queue()就直接返回一个Future对象。可通过 Future.get()拿到run()的返回结果，但Future.get()是阻塞执行的。若执行成功，Future.get()返回单个返回值。当执行失败时，如果没有重写fallback，Future.get()抛出异常。

**observe()**

事件注册前执行run()/construct()，支持接收多个值对象，取决于发射源。调用observe()会返回一个hot Observable，也就是说，调用observe()自动触发执行run()/construct()，无论是否存在订阅者。

如果继承的是HystrixCommand，hystrix会从线程池中取一个线程以非阻塞方式执行run()；如果继承的是HystrixObservableCommand，将以调用线程阻塞执行construct()。

observe()使用方法：

1. 调用observe()会返回一个Observable对象
2. 调用这个Observable对象的subscribe()方法完成事件注册，从而获取结果

**toObservable()**

事件注册后执行run()/construct()，支持接收多个值对象，取决于发射源。调用toObservable()会返回一个cold Observable，也就是说，调用toObservable()不会立即触发执行run()/construct()，必须有订阅者订阅Observable时才会执行。

如果继承的是HystrixCommand，hystrix会从线程池中取一个线程以非阻塞方式执行run()，调用线程不必等待run()；如果继承的是HystrixObservableCommand，将以调用线程堵塞执行construct()，调用线程需等待construct()执行完才能继续往下走。

toObservable()使用方法：

1. 调用observe()会返回一个Observable对象
2. 调用这个Observable对象的subscribe()方法完成事件注册，从而获取结果

需注意的是，HystrixCommand也支持toObservable()和observe()，但是即使将HystrixCommand转换成Observable，它也只能发射一个值对象。只有HystrixObservableCommand才支持发射多个值对象。

### Hystrix异常机制和处理

Hystrix的异常处理中，有5种出错的情况下会被fallback所截获，从而触发fallback，这些情况是：

❑ FAILURE：执行失败，抛出异常。

❑ TIMEOUT：执行超时。

❑ SHORT_CIRCUITED：断路器打开。

❑ THREAD_POOL_REJECTED：线程池拒绝。

❑ SEMAPHORE_REJECTED：信号量拒绝。

### Hystrix容错

Hystrix的容错主要是通过添加容许延迟和容错方法，帮助控制这些分布式服务之间的交互。 还通过隔离服务之间的访问点，阻止它们之间的级联故障以及提供回退选项来实现这一点，从而提高系统的整体弹性。Hystrix主要提供了以下几种容错方法：

- 资源隔离
- 熔断
- 降级

下面我们详细谈谈这几种容错机制。

### 资源隔离

资源隔离主要指对线程的隔离。Hystrix提供了两种线程隔离方式：线程池和信号量。

线程隔离-线程池

Hystrix通过命令模式对发送请求的对象和执行请求的对象进行解耦，将不同类型的业务请求封装为对应的命令请求。如订单服务查询商品，查询商品请求->商品Command；商品服务查询库存，查询库存请求->库存Command。并且为每个类型的Command配置一个线程池，当第一次创建Command时，根据配置创建一个线程池，并放入ConcurrentHashMap，如商品Command：

后续查询商品的请求创建Command时，将会重用已创建的线程池。线程池隔离之后的服务依赖关系：

![img](https://static.oschina.net/uploads/space/2018/0205/114513_i1Ld_2663573.png)

通过将发送请求线程与执行请求的线程分离，可有效防止发生级联故障。当线程池或请求队列饱和时，Hystrix将拒绝服务，使得请求线程可以快速失败，从而避免依赖问题扩散。

**线程池隔离优缺点**

优点：

- 保护应用程序以免受来自依赖故障的影响，指定依赖线程池饱和不会影响应用程序的其余部分。
- 当引入新客户端lib时，即使发生问题，也是在本lib中，并不会影响到其他内容。
- 当依赖从故障恢复正常时，应用程序会立即恢复正常的性能。
- 当应用程序一些配置参数错误时，线程池的运行状况会很快检测到这一点（通过增加错误，延迟，超时，拒绝等），同时可以通过动态属性进行实时纠正错误的参数配置。
- 如果服务的性能有变化，需要实时调整，比如增加或者减少超时时间，更改重试次数，可以通过线程池指标动态属性修改，而且不会影响到其他调用请求。
- 除了隔离优势外，hystrix拥有专门的线程池可提供内置的并发功能，使得可以在同步调用之上构建异步门面（外观模式），为异步编程提供了支持（Hystrix引入了Rxjava异步框架）。

注意：尽管线程池提供了线程隔离，我们的客户端底层代码也必须要有超时设置或响应线程中断，不能无限制的阻塞以致线程池一直饱和。

缺点：

线程池的主要缺点是增加了计算开销。每个命令的执行都在单独的线程完成，增加了排队、调度和上下文切换的开销。因此，要使用Hystrix，就必须接受它带来的开销，以换取它所提供的好处。

通常情况下，线程池引入的开销足够小，不会有重大的成本或性能影响。但对于一些访问延迟极低的服务，如只依赖内存缓存，线程池引入的开销就比较明显了，这时候使用线程池隔离技术就不适合了，我们需要考虑更轻量级的方式，如信号量隔离。

**线程隔离-信号量**

上面提到了线程池隔离的缺点，当依赖延迟极低的服务时，线程池隔离技术引入的开销超过了它所带来的好处。这时候可以使用信号量隔离技术来代替，通过设置信号量来限制对任何给定依赖的并发调用量。下图说明了线程池隔离和信号量隔离的主要区别：

![img](https://static.oschina.net/uploads/space/2018/0206/111924_txyW_2663573.png)

  

使用线程池时，发送请求的线程和执行依赖服务的线程不是同一个，而使用信号量时，发送请求的线程和执行依赖服务的线程是同一个，都是发起请求的线程。先由于Hystrix默认使用线程池做线程隔离，使用信号量隔离需要显示地将属性execution.isolation.strategy设置为ExecutionIsolationStrategy.SEMAPHORE，同时配置信号量个数，默认为10。客户端需向依赖服务发起请求时，首先要获取一个信号量才能真正发起调用，由于信号量的数量有限，当并发请求量超过信号量个数时，后续的请求都会直接拒绝，进入fallback流程。

信号量隔离主要是通过控制并发请求量，防止请求线程大面积阻塞，从而达到限流和防止雪崩的目的。

**线程隔离总结**

线程池和信号量都可以做线程隔离，但各有各的优缺点和支持的场景，对比如下：

|        | 线程切换 | 支持异步 | 支持超时 | 支持熔断 | 限流 | 开销 |
| ------ | -------- | -------- | -------- | -------- | ---- | ---- |
| 信号量 | 否       | 否       | 否       | 是       | 是   | 小   |
| 线程池 | 是       | 是       | 是       | 是       | 是   | 大   |

线程池和信号量都支持熔断和限流。相比线程池，信号量不需要线程切换，因此避免了不必要的开销。但是信号量不支持异步，也不支持超时，也就是说当所请求的服务不可用时，信号量会控制超过限制的请求立即返回，但是已经持有信号量的线程只能等待服务响应或从超时中返回，即可能出现长时间等待。线程池模式下，当超过指定时间未响应的服务，Hystrix会通过响应中断的方式通知线程立即结束并返回。

### **熔断**

现实生活中，可能大家都有注意到家庭电路中通常会安装一个保险盒，当负载过载时，保险盒中的保险丝会自动熔断，以保护电路及家里的各种电器，这就是熔断器的一个常见例子。Hystrix中的熔断器(Circuit Breaker)也是起类似作用，Hystrix在运行过程中会向每个commandKey对应的熔断器报告成功、失败、超时和拒绝的状态，熔断器维护并统计这些数据，并根据这些统计信息来决策熔断开关是否打开。如果打开，熔断后续请求，快速返回。隔一段时间（默认是5s）之后熔断器尝试半开，放入一部分流量请求进来，相当于对依赖服务进行一次健康检查，如果请求成功，熔断器关闭。

**熔断器配置**

Circuit Breaker主要包括如下6个参数：

1、circuitBreaker.enabled

是否启用熔断器，默认是TRUE。
2 、circuitBreaker.forceOpen

熔断器强制打开，始终保持打开状态，不关注熔断开关的实际状态。默认值FLASE。
3、circuitBreaker.forceClosed
熔断器强制关闭，始终保持关闭状态，不关注熔断开关的实际状态。默认值FLASE。

4、circuitBreaker.errorThresholdPercentage
错误率，默认值50%，例如一段时间（10s）内有100个请求，其中有54个超时或者异常，那么这段时间内的错误率是54%，大于了默认值50%，这种情况下会触发熔断器打开。

5、circuitBreaker.requestVolumeThreshold

默认值20。含义是一段时间内至少有20个请求才进行errorThresholdPercentage计算。比如一段时间了有19个请求，且这些请求全部失败了，错误率是100%，但熔断器不会打开，总请求数不满足20。

6、circuitBreaker.sleepWindowInMilliseconds

半开状态试探睡眠时间，默认值5000ms。如：当熔断器开启5000ms之后，会尝试放过去一部分流量进行试探，确定依赖服务是否恢复。

**熔断器工作原理**

下图展示了HystrixCircuitBreaker的工作原理：

![img](https://static.oschina.net/uploads/space/2018/0206/155833_vCne_2663573.png)

​                  图片来源Hystrix官网https://github.com/Netflix/Hystrix/wiki

熔断器工作的详细过程如下：

**第一步**，调用allowRequest()判断是否允许将请求提交到线程池

1. 如果熔断器强制打开，circuitBreaker.forceOpen为true，不允许放行，返回。
2. 如果熔断器强制关闭，circuitBreaker.forceClosed为true，允许放行。此外不必关注熔断器实际状态，也就是说熔断器仍然会维护统计数据和开关状态，只是不生效而已。

**第二步**，调用isOpen()判断熔断器开关是否打开

1. 如果熔断器开关打开，进入第三步，否则继续；
2. 如果一个周期内总的请求数小于circuitBreaker.requestVolumeThreshold的值，允许请求放行，否则继续；
3. 如果一个周期内错误率小于circuitBreaker.errorThresholdPercentage的值，允许请求放行。否则，打开熔断器开关，进入第三步。

**第三步**，调用allowSingleTest()判断是否允许单个请求通行，检查依赖服务是否恢复

1. 如果熔断器打开，且距离熔断器打开的时间或上一次试探请求放行的时间超过circuitBreaker.sleepWindowInMilliseconds的值时，熔断器器进入半开状态，允许放行一个试探请求；否则，不允许放行。

此外，为了提供决策依据，每个熔断器默认维护了10个bucket，每秒一个bucket，当新的bucket被创建时，最旧的bucket会被抛弃。其中每个blucket维护了请求成功、失败、超时、拒绝的计数器，Hystrix负责收集并统计这些计数器。

### **回退降级**

降级，通常指务高峰期，为了保证核心服务正常运行，需要停掉一些不太重要的业务，或者某些服务不可用时，执行备用逻辑从故障服务中快速失败或快速返回，以保障主体业务不受影响。Hystrix提供的降级主要是为了容错，保证当前服务不受依赖服务故障的影响，从而提高服务的健壮性。要支持回退或降级处理，可以重写HystrixCommand的getFallBack方法或HystrixObservableCommand的resumeWithFallback方法。

Hystrix在以下几种情况下会走降级逻辑：

- 执行construct()或run()抛出异常
- 熔断器打开导致命令短路
- 命令的线程池和队列或信号量的容量超额，命令被拒绝
- 命令执行超时

### **降级回退方式**

**Fail Fast 快速失败**

快速失败是最普通的命令执行方法，命令没有重写降级逻辑。 如果命令执行发生任何类型的故障，它将直接抛出异常。

**Fail Silent 无声失败**

指在降级方法中通过返回null，空Map，空List或其他类似的响应来完成。

**Fallback: Static**

指在降级方法中返回静态默认值。 这不会导致服务以“无声失败”的方式被删除，而是导致默认行为发生。如：应用根据命令执行返回true / false执行相应逻辑，但命令执行失败，则默认为true

**Fallback: Stubbed**

当命令返回一个包含多个字段的复合对象时，适合以Stubbed 的方式回退。

**Fallback: Cache via Network**

有时，如果调用依赖服务失败，可以从缓存服务（如redis）中查询旧数据版本。由于又会发起远程调用，所以建议重新封装一个Command，使用不同的ThreadPoolKey，与主线程池进行隔离。

**Primary + Secondary with Fallback**

有时系统具有两种行为- 主要和次要，或主要和故障转移。主要和次要逻辑涉及到不同的网络调用和业务逻辑，所以需要将主次逻辑封装在不同的Command中，使用线程池进行隔离。为了实现主从逻辑切换，可以将主次command封装在外观HystrixCommand的run方法中，并结合配置中心设置的开关切换主从逻辑。由于主次逻辑都是经过线程池隔离的HystrixCommand，因此外观HystrixCommand可以使用信号量隔离，而没有必要使用线程池隔离引入不必要的开销。原理图如下：

![img](https://static.oschina.net/uploads/space/2018/0207/181402_RQHc_2663573.png)

​             图片来源Hystrix官网https://github.com/Netflix/Hystrix/wiki

主次模型的使用场景还是很多的。如当系统升级新功能时，如果新版本的功能出现问题，通过开关控制降级调用旧版本的功能。

通常情况下，建议重写getFallBack或resumeWithFallback提供自己的备用逻辑，但不建议在回退逻辑中执行任何可能失败的操作。

## Spring Cloud Hystrix Dashboard

### Hystrix Dashboard

Hystrix提供了对于微服务调用状态的监控信息，但是需要结合spring-boot-actuator模块一起使用。

Hystrix Dashboard主要用来实时监控Hystrix的各项指标信息。通过Hystrix Dashboard反馈的实时信息，可以帮助我们快速发现系统中存在的问题。

我们在服务容错保护Hystrix - Spring Cloud系列(三)文章中例子的基础上进行改造，来显示下Dashboard的用法。

User：服务消费者
Order：服务提供者
Eureka：服务注册中心
我们先来看下Hystrix Dashboard长什么样子

新建一个Spring Boot工程，命名为hystrix-dashboard。
pom.xml

```java
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<version>2.0.6.RELEASE</version>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
	<version>2.0.2.RELEASE</version>
</dependency>
pom中引入了Hystrix Dashboard的相关依赖。
```

项目启动类：AppHystrixDashboard.java

```
@SpringBootApplication
@EnableHystrixDashboard
public class AppHystrixDashboard {
    public static void main(String[] args) {
        SpringApplication.run(AppHystrixDashboard.class);
    }
}
```


启动类中增加了@EnableHystrixDashboard注解。

配置文件：application.yml

server:
  port: 9000
只需要简单配置启动端口号即可。启动项目，浏览器访问http://localhost:9000/hystrix即可看到Hystrix Dashboard的监控首页。

![img](https://img-blog.csdnimg.cn/20190215230416243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

从首页中我们可以看出并没有具体的监控信息，从页面上的文件内容我们可以知道，Hystrix Dashboard共支持三种不同的监控方式：

默认的集群监控： http://turbine-hostname:port/turbine.stream
指定的集群监控： http://turbine-hostname:port/turbine.stream?cluster=[clusterName]
单体应用的监控： http://hystrix-app:port/actuator/hystrix.stream
页面上面的几个参数局域

最上面的输入框： 输入上面所说的三种监控方式的地址，用于访问具体的监控信息页面。
Delay： 该参数用来控制服务器上轮询监控信息的延迟时间，默认2000毫秒。
Title： 该参数对应头部标题Hystrix Stream之后的内容，默认会使用具体监控实例的Url。
我们先来看下单个服务实例的监控，从http://hystrix-app:port/actuator/hystrix.stream连接中可以看出，Hystrix Dashboard监控单节点实例需要访问实例的actuator/hystrix.stream接口，我们自然就需要为服务实例添加这个端点。

第一步：User工程中引入starter-actuator依赖
pom.xml

```java
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
	<version>2.0.6.RELEASE</version>
```


</dependency>
第二步：application.yml加入配置

```
management:  
 endpoints:   
  web:      
   exposure:        
    include: '*'
```

需要注意的是在Spring Cloud Finchley 版本以前访问路径是/hystrix.stream，如果是Finchley的话就需要加入上面的配置。因为spring Boot 2.0.x以后的actuator只暴露了info和health2个端点，这里我们把所有端点开放，include: '*'代表开放所有端点。

启动User、Order、Eureka工程，浏览器访问http://localhost:5000/actuator/hystrix.stream会看到如下页面，因为监控的实例本身还没有调用任何服务，所以监控端点也没记录任何信息。

![ping](https://img-blog.csdnimg.cn/20190215232507959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

新开一个浏览器tab页，访问下Order服务http://localhost:5000/getOrder.do，重新刷新下刚才的页面可以看到已经有数据返回了，说明我们的埋点生效了。

![getOrder](https://img-blog.csdnimg.cn/20190215232830335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

从浏览器中的信息可以看出这些信息是刚刚请求微服务时所记录的监控信息，但是直接去看这些信息肯定是不友好的，所以Hystrix Dashboard就派上用场了。

![hd](https://img-blog.csdnimg.cn/20190215233353187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

在Hystrix Dashboard中间这个输入框中，填入User服务的监控地址，也就是http://localhost:5000/actuator/hystrix.stream，点击Monitor Stream按钮，就会跳转到具体的监控页面:

![hd2](https://img-blog.csdnimg.cn/20190215233436126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

### Hystrix仪表盘相关概念

多次请求http://localhost:5000/getOrder.do，会发现监控页面的数据也在实时的更新

![hystrix dashboard](https://img-blog.csdnimg.cn/20190215234815269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

下面来介绍下图形中各元素的具体含义：

实心圆： 共有两种含义。通过颜色的变化代表了实例的健康程度，它的健康度从绿色、黄色、橙色、红色递减。通过圆的大小来代表请求流量的大小，流量越大该实心圆就越大。所以通过该实心圆的展示，就可以在大量的实例中快速的发现故障实例和高压力实例。
曲线： 记录2分钟内流量的相对变化，可以通过它来观察到流量的上升和下降趋势。
其他数量指标：

![数量指标](https://img-blog.csdnimg.cn/20190215235248112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

Terbine集群监控
从Hystrix Dashboard的监控首页中可以看出，集群的监控端点是http://turbine-hostname:port/turbine.stream，需要我们引入Trubine聚合服务，通过它来汇集监控信息，并将聚合后的信息提供给Hystrix Dashboard。

结合Eureka构建聚合服务
第一步：新建一个Spring Boot工程，命名turbine
pom.xml

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
		<version>2.0.6.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-netflix-turbine</artifactId>
		<version>2.0.2.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		<version>2.0.2.RELEASE</version>
	</dependency>

**application.yml**

```java
spring.application.name: turbine

turbine:
  appConfig: user-micro # 需要收集信息的服务名
  cluster-name-expression: default # 指定集群名称，
  combine-host-port: true # 同一主机上的服务通过主机名和端口号的组合来进行区分，默认以host来区分

server:
  port: 9090

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8081/eureka

```

**项目启动类：AppTurbine**

```java
@SpringBootApplication
@EnableTurbine
@EnableDiscoveryClient
public class AppTurbine {
    public static void main(String[] args) {
        SpringApplication.run(AppTurbine.class);
    }
}
```

**第二步: 复制一个User工程，端口号为5001，启动两个User工程实例**

到目前为止，根据我们搭建的工程，Turbine的运行流程可以展示为下图所示：

![turbine](https://img-blog.csdnimg.cn/20190216105524907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

分别启动eureka、order、user、user1、hystrix-dashboard、turbine等工程，然后访问http://localhost:9000/hystrix（Hystrix Dashboard的监控首页），在首页输入框中输入http://localhost:9090/turbine.stream，点击Monitor Stream按钮。可以发现页面此时处于loding状态，因为现在还没有收集到任何metrics数据。多次访问http://localhost:5000/getOrder.do和http://localhost:5001/getOrder.do，再次刷新监控页面即可看到监控页面有数据了。

![turbine1](https://img-blog.csdnimg.cn/2019021611023528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h6eWdjcw==,size_16,color_FFFFFF,t_70)

可以看到红框中的Hosts值已变成2。虽然启动了两个user实例，但是只展现了一个监控图。这是因为这两个user实例是同一个服务名称，而对于集群来说我们关注的是服务集群的高可用性，所有Turbine会将相同的服务作为整体来看待，并汇总成一个监控图。读者可以尝试将其中一个user工程的spring.application.name换成不同的值，就会发现是两个监控图形，这里不再演示。

## Spring Cloud Zuul

### API网关

在微服务架构中，通常会有多个服务提供者。设想一个电商系统，可能会有商品、订单、支付、用户等多个类型的服务，而每个类型的服务数量也会随着整个系统体量的增大也会随之增长和变更。作为UI端，在展示页面时可能需要从多个微服务中聚合数据，而且服务的划分位置结构可能会有所改变。网关就可以对外暴露聚合API，屏蔽内部微服务的微小变动，保持整个系统的稳定性。

### Zuul

Zuul是Spring Cloud全家桶中的微服务API网关。

所有从设备或网站来的请求都会经过Zuul到达后端的Netflix应用程序。作为一个边界性质的应用程序，Zuul提供了动态路由、监控、弹性负载和安全功能。Zuul底层利用各种filter实现如下功能：

- 认证和安全 识别每个需要认证的资源，拒绝不符合要求的请求。
- 性能监测 在服务边界追踪并统计数据，提供精确的生产视图。
- 动态路由 根据需要将请求动态路由到后端集群。
- 压力测试 逐渐增加对集群的流量以了解其性能。
- 负载卸载 预先为每种类型的请求分配容量，当请求超过容量时自动丢弃。
- 静态资源处理 直接在边界返回某些响应。

### Zuul的路由通配符映射规则

![image-20200225222236524](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225222236524.png)

### Zuul工作原理

Zuul的核心逻辑是由一系列紧密配合工作的Filter来实现的，它们能够在进行HTTP请求或者响应的时候执行相关操作。可以说，没有Filter责任链，就没有如今的Zuul，更不可能构成功能丰富的“网关”。本章后续实战技能大多可基于它完成，它是Zuul中最为开放与核心的功能。ZuulFilter的主要特性有以下几点：

❑ Filter的类型：Filter的类型决定了此Filter在Filter链中的执行顺序。可能是路由动作发生前，可能是路由动作发生时，可能是路由动作发生后，也可能是路由过程发生异常时。

❑ Filter的执行顺序：同一种类型的Filter可以通过filterOrder()方法来设定执行顺序。一般会根据业务的执行顺序需求，来设定自定义Filter的执行顺序。

❑ Filter的执行条件：Filter运行所需要的标准或条件。

❑ Filter的执行效果：符合某个Filter执行条件，产生的执行效果。

### Zuul内置Filter信息

![image-20200225222420059](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225222420059.png)

### Zuul限流

1．漏桶（Leaky Bucket）

当我们的请求或者具有一定体量的数据流涌来的时候，在漏桶的作用下，流量被整形，不能满足要求的部分被削减掉，所以，漏桶算法能够强制限定流量速率。注意，在我们的应用中，这部分溢出的流量是可以被利用起来的，并非完全丢弃，我们可以把它们收集到一个队列里面，做流量排队，尽量做到合理利用所有资源。

2．令牌桶（Token Bucket）

当我们的数据流涌来时，量化请求用于获取令牌，如果取到令牌则放行，同时桶内丢弃掉这个令牌；如果不能取到令牌，请求则被丢弃，由于令牌桶内可以存在一定数量的令牌，那么就可能存在一定程度的流量突发，这也是决定漏桶算法与令牌桶算法适用于不同应用场景的主要原因。

### Zuul灰度发布

灰度发布，是指在系统迭代新功能时的一种平滑过渡的上线发布方式。灰度发布是在原有系统的基础上，额外增加一个新版本，这个新版本包含我们需要待验证的新功能，随后用负载均衡器引入一小部分流量到这个新版本应用，如果整个过程没有出现任何差错，再平滑地把线上系统或服务一步步替换成新版本，至此完成了一次灰度发布

#### 常见发布方式优劣总结

![image-20200225230047700](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225230047700.png)

### Zuul应用优化

将对Zuul的优化分为以下几个类型。

❑ 容器优化：内置容器Tomcat与Undertow的比较与参数设置。

❑ 组件优化：内部集成的组件优化，如Hystrix线程隔离、Ribbon、HttpClient与OkHttp选择。

❑ JVM参数优化：适用于网关应用的JVM参数建议。

❑ 内部优化：一些内部原生参数，或者内部源码，以一种更恰当的方式重写它们。

### 路由配置

Zuul提供了一套简单且强大路由配置策略，利用路由配置我们可以完成对微服务和URL更精确的控制。

1、重写指定微服务的访问路径：

```yml
zuul:
  routes:
    rest-demo: /rest/**
```

这表示将rest-demo微服务的地址映射到/rest/**路径。

2、忽略指定微服务：

```
zuul:
  ignored-services: rest-demo,xxx-service 
```

使用“*”可忽略所有微服务，多个指定微服务以半角逗号分隔。

3、忽略所有微服务，只路由指定微服务：

```
zuul:
  ignored-services: *
  routes:
    rest-demo: /rest/** 
```

4、路由别名：

```
zuul:
  routes:
    route-name: #路由别名，无其他意义，与例1效果一致
     service-id: rest-demo
      path: /rest/**
```

5、指定path和URL

```
zuul:
  routes:
    route-name:
      url: http://localhost:8000/
      path: /rest/**
```

此例将http://ZUULHOST:ZUULPORT/rest/**映射到http://localhost:8000/**。同时由于并非用service-id定位服务，所以也无法使用负载均衡功能。

6、即指定path和URL，又保留Zuul的Hystrix、Ribbon特性

```
zuul:
  routes:
    route-name: #路由别名，无其他意义，与例1效果一致
      service-id: rest-demo
      path: /rest/**
ribbon:
  eureka:
    enable: false #为Ribbon禁用Eureka
rest-demo:
  ribbon:
    listOfServers: localhost:9000,localhost:9001
```

7、借助`PatternServiceRouteMapper`实现路由的正则匹配

```
@Bean
public PatternServiceRouteMapper serviceRouteMapper(){
    /**
     * A RegExp Pattern that extract needed information from a service ID. Ex :
     * "(?<name>.*)-(?<version>v.*$)"
     */
    //private Pattern servicePattern;
    /**
     * A RegExp that refer to named groups define in servicePattern. Ex :
     * "${version}/${name}"
     */
    //private String routePattern;
    return new PatternServiceRouteMapper("(?<name>^.+)-(?<version>v.+$)", "${version}/${name}");
}
```

此例可以将rest-demo-v1映射为/v1/rest-demo/**。

8、路由前缀

```
zuul:
  prefix: /api
  strip-prefix: true
  routes:
    rest-demo: /rest/**
```

此时访问Zuul的/api/rest/user/xdlysk会被转发到/rest-demo/user/xdlysk。

9、忽略某些微服务中的某些路径

```
zuul:
  ignoredPatterns: /**/user/* #忽略所有包含/user/的地址请求
  routes:
    route-demo:
      service-Id: rest-demo
      path: /rest/**
```

更多的配置项和配置方法可以参考

spring-cloud-netflix-zuul/src/main/java/org/springframework/cloud/netflix/zuul/filters/ZuulProperties.java并结合上述例子扩展。在ZuulProperties.java中每个字段都会有注释解释其作用。

## Spring Cloud Config

### Spring Cloud Config

Spring Cloud Config为分布式系统中的外部配置提供服务器和客户端支持。方便部署与运维。
 分客户端、服务端。
 服务端也称分布式配置中心，是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。
 客户端则是通过指定配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息。默认采用 git，并且可以通过 git 客户端工具来方便管理和访问配置内容。

优点：

- 集中管理配置文件
- 不同环境不同配置，动态化的配置更新
- 运行期间，不需要去服务器修改配置文件，服务会想配置中心拉取自己的信息
- 配置信息改变时，不需要重启即可更新配置信息到服务
- 配置信息以 rest 接口

### 实现最简单的配置中心

最简单的配置中心，就是启动一个服务作为服务方，之后各个需要获取配置的服务作为客户端来这个服务方获取配置。

*先在 github 中建立配置文件*

我创建的仓库地址为：[配置中心仓库](https://github.com/huzhicheng/config-only-a-demo/tree/master)

目录结构如下：

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725084302799-353615966.png)

配置文件的内容大致如下，用于区分，略有不同。

```
data:
  env: config-eureka-dev
  user:
    username: eureka-client-user
    password: 1291029102
```

注意文件的名称不是乱起的，例如上面的 config-single-client-dev.yml 和 config-single-client-prod.yml 这两个是同一个项目的不同版本，项目名称为 config-single-client， 一个对应开发版，一个对应正式版。config-eureka-client-dev.yml 和 config-eureka-client-prod.yml 则是另外一个项目的，项目的名称就是 config-eureka-client 。

创建配置中心服务端

1、新建 Spring Boot 项目，引入 config-server 和 starter-web

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring cloud config 服务端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

2、配置 config 相关的配置项

bootstrap.yml 文件

```java
spring:
  application:
    name: config-single-server  # 应用名称
  cloud:
     config:
        server:
          git:
            uri: https://github.com/huzhicheng/config-only-a-demo #配置文件所在仓库
            username: github 登录账号
            password: github 登录密码
            default-label: master #配置文件分支
            search-paths: config  #配置文件所在根目录
```

application.yml

```
server:
  port: 3301
```

3、在 Application 启动类上增加相关注解 `@EnableConfigServer`

```
@SpringBootApplication
@EnableConfigServer
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

启动服务，接下来测试一下。

Spring Cloud Config 有它的一套访问规则，我们通过这套规则在浏览器上直接访问就可以。

```java
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```

{application} 就是应用名称，对应到配置文件上来，就是配置文件的名称部分，例如我上面创建的配置文件。

{profile} 就是配置文件的版本，我们的项目有开发版本、测试环境版本、生产环境版本，对应到配置文件上来就是以 application-{profile}.yml 加以区分，例如application-dev.yml、application-sit.yml、application-prod.yml。

{label} 表示 git 分支，默认是 master 分支，如果项目是以分支做区分也是可以的，那就可以通过不同的 label 来控制访问不同的配置文件了。

上面的 5 条规则中，我们只看前三条，因为我这里的配置文件都是 yml 格式的。根据这三条规则，我们可以通过以下地址查看配置文件内容:

http://localhost:3301/config-single-client/dev/master

http://localhost:3301/config-single-client/prod

http://localhost:3301/config-single-client-dev.yml

http://localhost:3301/config-single-client-prod.yml

http://localhost:3301/master/config-single-client-prod.yml

通过访问以上地址，如果可以正常返回数据，则说明配置中心服务端一切正常。

创建配置中心客户端，使用配置

配置中心服务端好了，配置数据准备好了，接下来，就要在我们的项目中使用它了。

1、引用相关的 maven 包。

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring cloud config 客户端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2、初始化配置文件

bootstrap.yml

```
spring:
  profiles:
    active: dev

---
spring:
  profiles: prod
  application:
    name: config-single-client
  cloud:
     config:
       uri: http://localhost:3301
       label: master
       profile: prod


---
spring:
  profiles: dev
  application:
    name: config-single-client
  cloud:
     config:
       uri: http://localhost:3301
       label: master
       profile: dev
```

配置了两个版本的配置，并通过 spring.profiles.active 设置当前使用的版本，例如本例中使用的 dev 版本。

```
server:
  port: 3302
management:
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"

data:
  env: NaN
  user:
    username: NaN
    password: NaN
```

其中 management 是关于 actuator 相关的，接下来自动刷新配置的时候需要使用。

data 部分是当无法读取配置中心的配置时，使用此配置，以免项目无法启动。

3、要读取配置中心的内容，需要增加相关的配置类，Spring Cloud Config 读取配置中心内容的方式和读取本地配置文件中的配置是一模一样的。可以通过 @Value 或 @ConfigurationProperties 来获取。

```
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

4、要读取配置中心的内容，需要增加相关的配置类，Spring Cloud Config 读取配置中心内容的方式和读取本地配置文件中的配置是一模一样的。可以通过 @Value 或 @ConfigurationProperties 来获取。

使用 @Value 的方式：

```java
@Data 
@Component 
public class GitConfig {     
    @Value("${data.env}")    
    private String env;         @Value("${data.user.username}")    
    private String username;     @Value("${data.user.password}")    
    private String password; }
```

使用 @ConfigurationProperties 的方式：

```java
@Component
@Data
@ConfigurationProperties(prefix = "data")
public class GitAutoRefreshConfig {

    public static class UserInfo {
        private String username;

        private String password;

        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getPassword() {
            return password;
        }

        public void setPassword(String password) {
            this.password = password;
        }

        @Override
        public String toString() {
            return "UserInfo{" +
                    "username='" + username + '\'' +
                    ", password='" + password + '\'' +
                    '}';
        }
    }

    private String env;

    private UserInfo user;
}
```

4、增加一个 RESTController 来测试使用配置

```
@RestController
public class GitController {

    @Autowired
    private GitConfig gitConfig;

    @Autowired
    private GitAutoRefreshConfig gitAutoRefreshConfig;

    @GetMapping(value = "show")
    public Object show(){
        return gitConfig;
    }

    @GetMapping(value = "autoShow")
    public Object autoShow(){
        return gitAutoRefreshConfig;
    }
}
```

5、项目启动类

```
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

启动项目，访问 RESTful 接口

http://localhost:3302/show，结果如下：

```
{
  "env": "localhost-dev-edit",
  "username": "fengzheng-dev",
  "password": "password-dev"
}
```

http://localhost:3302/autoShow，结果如下：

```
{
  "env": "localhost-dev-edit",
  "user": {
      "username": "fengzheng-dev",
      "password": "password-dev"
    }
}
```

### 实现自动刷新

Spring Cloud Config 在项目启动时加载配置内容这一机制，导致了它存在一个缺陷，修改配置文件内容后，不会自动刷新。例如我们上面的项目，当服务已经启动的时候，去修改 github 上的配置文件内容，这时候，再次刷新页面，对不起，还是旧的配置内容，新内容不会主动刷新过来。
但是，总不能每次修改了配置后重启服务吧。如果是那样的话，还是不要用它了为好，直接用本地配置文件岂不是更快。

它提供了一个刷新机制，但是需要我们主动触发。那就是 @RefreshScope 注解并结合 actuator ，注意要引入 spring-boot-starter-actuator 包。

1、在 config client 端配置中增加 actuator 配置，上面大家可能就注意到了。

```
management:
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"
```

其实这里主要用到的是 refresh 这个接口

2、在需要读取配置的类上增加 @RefreshScope 注解，我们是 controller 中使用配置，所以加在 controller 中。

```
@RestController
@RefreshScope
public class GitController {

    @Autowired
    private GitConfig gitConfig;

    @Autowired
    private GitAutoRefreshConfig gitAutoRefreshConfig;

    @GetMapping(value = "show")
    public Object show(){
        return gitConfig;
    }

    @GetMapping(value = "autoShow")
    public Object autoShow(){
        return gitAutoRefreshConfig;
    }
}
```

注意，以上都是在 client 端做的修改。

之后，重启 client 端，重启后，我们修改 github 上的配置文件内容，并提交更改，再次刷新页面，没有反应。没有问题。

接下来，我们发送 POST 请求到 http://localhost:3302/actuator/refresh 这个接口，用 postman 之类的工具即可，此接口就是用来触发加载新配置的，返回内容如下:

```
[    "config.client.version",    
     "data.env" 
]
```

之后，再次访问 RESTful 接口，http://localhost:3302/autoShow 这个接口获取的数据已经是最新的了，说明 refresh 机制起作用了。而 http://localhost:3302/show 获取的还是旧数据，这与 @Value 注解的实现有关，所以，我们在项目中就不要使用这种方式加载配置了。

### 在 github 中配置 Webhook

这就结束了吗，并没有，总不能每次改了配置后，就用 postman 访问一下 refresh 接口吧，还是不够方便呀。 github 提供了一种 webhook 的方式，当有代码变更的时候，会调用我们设置的地址，来实现我们想达到的目的。

1、进入 github 仓库配置页面，选择 Webhooks ，并点击 add webhook；

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090559970-931500557.png)

2、之后填上回调的地址，也就是上面提到的 actuator/refresh 这个地址，但是必须保证这个地址是可以被 github 访问到的。如果是内网就没办法了。这也仅仅是个演示，一般公司内的项目都会有自己的代码管理工具，例如自建的 gitlab，gitlab 也有 webhook 的功能，这样就可以调用到内网的地址了。

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090614229-390401707.png)

### 结合 Eureka 使用 Spring Cloud Config

以上讲了 Spring Cloud Config 最基础的用法，但是如果我们的系统中使用了 Eureka 作为服务注册发现中心，那么 Spring Cloud Config 也应该注册到 Eureka 之上，方便其他服务消费者使用，并且可以注册多个配置中心服务端，以实现高可用。

好的，接下来就来集成 Spring Cloud Config 到 Eureka 上。

在 github 仓库中增加配置文件

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090637487-1608990683.png)

启动 Eureka Server

首先启动一个 Eureka Server，之前的文章有讲过 Eureka ，可以回过头去看看。[Spring Cloud Eureka 实现服务注册发现](https://mp.weixin.qq.com/s/kGrWQP_n_RCYTTaHbWQ3xQ)，为了清楚，这里还是把配置列出来

1、pom 中引入相关包

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

2、设置配置文件内容

bootstrap.yml

```
spring:
  application:
    name: kite-eureka-center
  security:
      user:
        name: test  # 用户名
        password: 123456   # 密码
  cloud:
    inetutils: ## 网卡设置
      ignoredInterfaces:  ## 忽略的网卡
        - docker0
        - veth.*
        - VM.*
      preferredNetworks:  ## 优先的网段
        - 192.168
```

application.yml

```
server:
  port: 3000
eureka:
  instance:
    hostname: eureka-center
    appname: 注册中心
  client:
    registerWithEureka: false # 单点的时候设置为 false 禁止注册自身
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://test:123456@localhost:3000/eureka
  server:
    enableSelfPreservation: false
    evictionIntervalTimerInMs: 4000
```

4、启动服务，在浏览器访问 3000 端口，并输出用户名 test，密码 123456 即可进入 Eureka UI

### 配置 Spring Cloud Config 服务端

服务端和前面的相比也就是多了注册到 Eureka 的配置，其他地方都是一样的。

1、在 pom 中引入相关的包

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring cloud config 服务端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>

<!-- eureka client 端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

2、配置文件做配置

application.yml

```
server:
  port: 3012
eureka:
  client:
    serviceUrl:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://test:123456@localhost:3000/eureka/
  instance:
    preferIpAddress: true
spring:
  application:
    name: config-eureka-server
  cloud:
     config:
        server:
          git:
            uri: https://github.com/huzhicheng/config-only-a-demo
            username: github 用户名
            password: github 密码
            default-label: master
            search-paths: config
```

相比于不加 Eureka 的版本，这里仅仅是增加了 Eureka 的配置，将配置中心注册到 Eureka ，作为服务提供者对外提供服务。

3、启动类，在 @EnableConfigServer 的基础上增加了 @EnableEurekaClient，另外也可以用 @EnableDiscoveryClient 代替 @EnableEurekaClient

```
@SpringBootApplication
@EnableConfigServer
@EnableEurekaClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

4、启动服务，之后访问 Eureka ，可以看到服务已注册成功

![img](https://img2018.cnblogs.com/blog/273364/201907/273364-20190725090702926-870149340.png)

### 配置 Spring Cloud Config 客户端

客户端的配置相对来说变动大一点，加入了 Eureka 之后，就不用再直接和配置中心服务端打交道了，要通过 Eureka 来访问。另外，还是要注意客户端的 application 名称要和 github 中配置文件的名称一致。

1、pom 中引入相关的包

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

2、配置文件

bootstrap.yml

```
eureka:
  client:
    serviceUrl:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://test:123456@localhost:3000/eureka/
  instance:
    preferIpAddress: true


spring:
  profiles:
    active: dev

---
spring:
  profiles: prod
  application:
    name: config-eureka-client
  cloud:
     config:
       label: master
       profile: prod
       discovery:
         enabled: true
         service-id: config-eureka-server


---
spring:
  profiles: dev
  application:
    name: config-eureka-client
  cloud:
     config:
       label: master  #指定仓库分支
       profile: dev   #指定版本 本例中建立了dev 和 prod两个版本
       discovery:
          enabled: true  # 开启
          service-id: config-eureka-server # 指定配置中心服务端的server-id 
```

除了注册到 Eureka 的配置外，就是配置和配置中心服务端建立关系。

其中 service-id 也就是服务端的application name。

application.yml

```
server:
  port: 3011
management:
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"

data:
  env: NaN
  user:
    username: NaN
    password: NaN
```

3、启动类，增加了 @EnableEurekaClient 注解，可以用 @EnableDiscoveryClient 代替

```
@SpringBootApplication
@EnableEurekaClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

4、另外的配置实体类和 RESTController 和上面的一样，没有任何更改，直接参考即可。

5、启动 client 端，访问 http://localhost:3011/autoShow，即可看到配置文件内容。

这个例子只是介绍了和 Eureka 结合的最基础的情况，还可以注册到高可用的 Eureka 注册中心，另外，配置中心服务端还可以注册多个实例，同时保证注册中心的高可用。

### 注意事项

*1.* 在 git 上的配置文件的名字要和 config 的 client 端的 application name 对应；

*2.* 在结合 eureka 的场景中，关于 eureka 和 git config 相关的配置要放在 bootstrap.yml 中，否则会请求默认的 config server 配置，这是因为当你加了配置中心，服务就要先去配置中心获取配置，而这个时候，application.yml 配置文件还没有开始加载，而 bootstrap.yml 是最先加载的。

## Spring Cloud Consul

### Consul

Consul是一个分布式高可用的服务网格（service mesh）解决方案，提供包括服务发现、配置和分段功能在内的全功能控制平面。这些功能中的每一个都可以根据需要单独使用，也可以一起使用以构建完整的服务网格。Consul是一个分布式高可用的系统服务发现与配置工具。简单来说，它跟Eureka的核心功能一样，但略有不同：

❑ Consul使用go语言编写，以HTTP方式对外提供服务。

❑ Consul支持多数据中心，这是它的一大特色。

❑ Consul除了服务发现之外，还有一些别的功能。

❑ Consul的一致性协议是Raft。

### Spring Cloud Consul

Spring Cloud Consul通过自动配置、对Spring Environment绑定和其他惯用的Spring模块编程，为Spring Boot应用程序提供了Consul集成。只需要一些简单注解，你就可以快速启用和配置Consul，并用它来构建大型分布式系统。

### Consul的模块介绍

围绕着Consul的核心功能，Spring Cloud Consul也提供了相应的功能模块与之匹配。下面就为大家逐一介绍。

❑ Spring-cloud-consul-binder：对Consul的事件功能封装。

❑ Spring-cloud-consul-config：对Consul的配置功能封装。

❑ Spring-cloud-consul-core：基础配置和健康检查模块。

❑ Spring-cloud-consul-discovery：对Consul服务治理功能封装。

## Spring Cloud Gateway

### Spring Cloud Gateway

Spring Cloud Gateway是Spring官方基于Spring 5.0、Spring Boot 2.0和Project Reactor等技术开发的网关，Spring Cloud Gateway旨在为微服务架构提供简单、有效且统一的API路由管理方式，如图17-1所示。Spring Cloud Gateway作为Spring Cloud生态系统中的网关，目标是替代Netflix Zuul，其不仅提供统一的路由方式，并且还基于Filter链的方式提供了网关基本的功能，例如：安全、监控/埋点、限流等。

![image-20200225224934763](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225224934763.png)

### Spring Cloud Gateway的核心概念

网关提供API全托管服务，丰富的API管理功能，辅助企业管理大规模的API，以降低管理成本和安全风险，包括协议适配、协议转发、安全策略（WAF）、防刷、流量、监控日志等功能。一般来说，网关对外暴露的URL或者接口信息，我们统称为路由信息。如果研发过网关中间件，或者使用或了解过Zuul的人，会知道网关的核心肯定是Filter以及FilterChain（Filter责任链）。

Spring Cloud Gateway也具有路由和Filter的概念。下面介绍一下Spring Cloud Gateway中最重要的几个概念。

❑ 路由（route）。路由是网关最基础的部分，路由信息由一个ID、一个目的url、一组断言工厂和一组Filter组成。如果路由断言为真，则说明请求的url和配置的路由匹配。

❑ 断言（Predicate）。Java 8中的断言函数。Spring CloudGateway中的断言函数输入类型是Spring 5.0框架中的ServerWebExchange。Spring Cloud Gateway中的断言函数允许开发者去定义匹配来自于Http Request中的任何信息，比如请求头和参数等。

❑ 过滤器（filter）。一个标准的Spring webFilter。Spring CloudGateway中的Filter分为两种类型的Filter，分别是Gateway Filter和Global Filter。过滤器Filter将会对请求和响应进行修改处理。

### Spring Cloud Gateway的工作原理

Spring Cloud Gateway的核心处理流程如图17-2所示，Gateway的客户端会向Spring Cloud Gateway发起请求，请求首先会被HttpWebHandlerAdapter进行提取组装成网关的上下文，然后网关的上下文会传递到DispatcherHandler。DispatcherHandler是所有请求的分发处理器，DispatcherHandler主要负责分发请求对应的处理器，比如将请求分发到对应RoutePredicate-HandlerMapping（路由断言处理映射器）。路由断言处理映射器主要用于路由的查找，以及找到路由后返回对应的FilteringWebHandler。FilteringWebHandler主要负责组装Filter链表并调用Filter执行一系列的Filter处理，然后把请求转到后端对应的代理服务处理，处理完毕之后，将Response返回到Gateway客户端。

![image-20200225225040428](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225225040428.png)

### Spring Cloud Gateway限流概述

在开发高并发系统时可以用三把利器来保护系统：缓存、降级和限流。缓存的目的是提升系统访问速度和增大系统处理的容量，是抗高并发流量的“银弹”；而降级是当服务出现问题或者影响到核心流程时，需要暂时将其屏蔽掉，待高峰过去之后或者问题解决后再打开；而有些场景并不能用缓存和降级来解决，比如稀缺资源（秒杀、抢购）、写服务（如评论、下单）、频繁的复杂查询，因此需要有一种手段来限制这些场景的并发/请求量，即限流。

限流的目的是通过对并发访问/请求进行限速或者对一个时间窗口内的请求进行限速来保护系统，一旦达到限制速率则可以拒绝服务（定向到错误页或友好的展示页）、排队或等待（比如秒杀、评论、下单等场景）、降级（返回兜底数据或默认数据）。

一般的中间件都会有单机限流框架，支持两种限流模式：控制速率和控制并发。Spring Cloud Gateway是一个API网关中间件，网关是所有请求流量的入口。特别是像天猫双十一、双十二等高并发场景下，当流量迅速剧增，网关除了要保护自身之外，还要限流保护后端应用。常见的限流算法有令牌桶和漏桶，计数器也可以进行粗暴限流实现。对限流算法，这里不再阐述，读者可以自行学习。下面介绍如何在Spring Cloud Gateway中实现限流。

### Spring Cloud Gateway的处理流程

![image-20200225225256341](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225225256341.png)

❑ HttpWebHandlerAdapter：构建组装网关请求的上下文。

❑ DispatcherHandler：所有请求的分发处理器，负责分发请求到对应的处理器。

❑ RoutePredicateHandlerMapping：路由断言处理映射器，用于路由的查找，以及找到路由后返回对应的WebHandler,DispatcherHandler会依次遍历HandlerMapping集合进行处理。

❑ FilteringWebHandler：创建过滤器链，使用Filter链表处理请求。RoutePredicateHandlerMapping找到路由后返回对应的FilteringWebHandler后对请求进行处理，FilteringWebHandler负责组装Filter链表并调用Filter处理请求。

## Spring Cloud Stream

### 工作流程

![image-20200228002741280](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200228002741280.png)

### Stream核心组件

（1）Binder

Binder是Spring Cloud Stream的一个重要抽象概念，是服务与消息传递系统之间的黏合剂。目前Spring Cloud Stream实现了面向Kafka和RabbitMQ这两种消息中间件的Binder。通过Binder，可以很方便地连接消息中间件，可以动态地改变消息的目标地址、发送方式，而不需要了解其背后的各种消息中间件在实现上的差异。

（2）Channel

Channel即通道，是对队列（Queue）的一种抽象。我们知道在消息传递系统中，队列的作用就是实现存储转发的媒介，消息发布者所生成的消息都将保存在队列中并由消息消费者进行消费。通道的名称对应的就是队列的名称，但是作为一种抽象和封装，各个消息传递系统所特有的队列概念并不会直接暴露在业务代码中，而是通过通道来对队列进行配置。

（3）Source和Sink

在介绍Source和Sink之前，需要明确两个在消息传递系统和企业服务总线领域中经常碰到的概念，即输入（Inbound）和输出（Outbound）。这里输入/输出的参照对象是Spring Cloud Stream自身，即从SpringCloud Stream发布消息就是输出，而通过Spring Cloud Stream接收消息就是输入。因此，Source组件用于面向单个输出通道的应用，而Sink组件则用于有单个输入通道的应用。

在Spring Cloud Stream中，Source组件就是使用一个POJO（Plain OldJava Object）对象来作为需要发布的消息，将该对象进行序列化（默认的序列化方式是JSON）后发布到通道中。而Sink组件用于监听通道并等待消息的到来，一旦有可用消息，Sink就会该消息反序列化为一个POJO对象并用于处理业务逻辑。

##  Spring Cloud Security

### 微服务下SSO单点登录方案

我们传统的做法就是通过SSO来实现，但在微服务架构情况下，应用被拆分，单体应用按照规则拆分成很多小的服务，这种情况下，如果对每个服务都进行每个用户的SSO动作，那么每个服务里都会做用户的认证和鉴权，可能保存用户信息或者每个用户都会和鉴权服务打交道，这些情况都将会带来非常大的网络消耗和性能损耗，也有可能会造成数据不一致，所以不太建议用这种方案。

![image-20200225224551915](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225224551915.png)

### 分布式Session与网关结合方案

![image-20200225224626193](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225224626193.png)

1）用户在网关进行SSO登录，进行用户认证，检查用户是否存在和有效。

2）如果认证通过，则将用户的信息或数据存储在第三方部件中，如MySQL, Redis。

3）后端微服务可以从共享存储中拿到用户的数据。很多场景下，这种方案是推荐的，因为很方便同时可以做扩展，也可以保证高可用的方案。但是这种方案的缺点是依赖于第三方部件，且这些部件需要做高可用，并且需要增加安全的控制，所以对于实现起来有一定复杂度。

### 客户端Token与网关结合方案

![image-20200225224702729](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225224702729.png)

它的实现步骤和方案如下。

1）客户端持有一个Token，通常可用JWT或者其他加密的算法实现自己的一种Token，然后通过Token保存了用户的信息。

2）发起请求并携带Token, Token传到网关层后，网关层进行认证和校验。

3）校验通过，携带Token到后台的微服务中，可以进行具体的接口或者url验证。

4）如果要涉及用户的大量数据存放，则Token就有可能不太适合，或者和上面的分布式Session一样，使用第三方部件来存储这些信息，这种方案也是业界很常用的方案，但对于Token来说，它的注销会有一定的麻烦，需要在网关层进行Token的注销。

### 网关与Token和服务间鉴权结合

![image-20200225224751983](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200225224751983.png)

这时的方案如下。

1）在Gateway网关层做认证，通过对用户校验后，传递用户的信息到header中，后台微服务在收到header后进行解析，解析完后查看是否有调用此服务或者某个url的权限，然后完成鉴权。

2）从服务内部发出的请求，在出去时进行拦截，把用户信息保存在header里，然后传出去，被调用方获取到header后进行解析和鉴权。

### OAuth2.0

开放授权（OAuth）是一个开放标准，允许用户让第三方应用访问该用户在某一网站上存储的私密的资源（如照片，视频，联系人列表），而无需将用户名和密码提供给第三方应用。

OAuth允许用户提供一个令牌，而不是用户名和密码来访问他们存放在特定服务提供者的数据。每一个令牌授权一个特定的网站（例如，视频编辑网站)在特定的时段（例如，接下来的2小时内）内访问特定的资源（例如仅仅是某一相册中的视频）。这样，OAuth让用户可以授权第三方网站访问他们存储在另外服务提供者的某些特定信息，而非所有内容。

**Resource Owner**: 资源所有者，我们可以直接理解为：用户(User);

**User Agent**: 用户代理，对于Web应用可以直接理解为浏览器;

**Authorization server**: 认证服务器，即提供用户认证和授权的服务器，可以是独立服务器;

**Resource server**: 资源服务器，这里我们可以理解为需要保护的微服务。

![img](https://upload-images.jianshu.io/upload_images/1488771-2a9cfe2dd74b9a6f.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)



认证流程步骤如下：

- (A)用户打开客户端以后，客户端请求用户给予授权;
- (B)用户同意授权给客户端;
- (C)客户端使用上一步获得的授权，向认证服务器申请令牌;
- (D)认证服务器对客户端进行认证以后，确认无误，同意发放令牌;
- (E)客户端使用令牌，向资源服务器申请获取资源;
- (F)资源服务器确认令牌无误，同意向客户端开放资源。

从流程上可以得知客户端必须在得到用户授权后才能够从认证服务中获取到令牌。OAuth2.0针对客户端授权提供了下面四种授权方式:

1. **授权码模式(authorization code)**: 该种模式是功能最完整、流程最严密的授权模式;
2. **简化模式(implicit)**: 该模式不需要通过第三方应用程序的服务器，跳过了"授权码"这个步骤，直接在浏览器中向认证服务器申请令牌，因此称为简化模式;
3. **密码模式(password)**: 用户向客户端提供自己的用户名和密码，客户端通过这些信息直接向认证服务器获取授权;
4. **客户端模式(client credentials)**: 指客户端以自己的名义，而不是以用户的名义向认证服务器获取认证，这种方式下认证服务器会将客户端作为一个用户来对待。

### JWT

JWT全称为Json Web Token，最近随着微服务架构的流行而越来越火，号称新一代的认证技术。

我们先来看一下OAuth2的token技术有没有什么痛点，相信从之前的介绍中你也发现了，token技术最大的问题是**不携带用户信息**，且资源服务器无法进行本地验证，每次对于资源的访问，资源服务器都需要向认证服务器发起请求，一是验证token的有效性，二是获取token对应的用户信息。如果有大量的此类请求，无疑处理效率是很低的，且认证服务器会变成一个中心节点，对于SLA和处理性能等均有很高的要求，这在分布式架构下是很要命的。

JWT就是在这样的背景下诞生的，从本质上来说，jwt就是一种**特殊格式**的token。普通的oauth2颁发的就是一串随机hash字符串，本身无意义，而jwt格式的token是有特定含义的，分为三部分：

- 头部`Header`
- 载荷`Payload`
- 签名`Signature`

这三部分均用base64进行编码，当中用`.`进行分隔，一个典型的jwt格式的token类似`xxxxx.yyyyy.zzzzz`。关于jwt格式的更多具体说明，不是本文讨论的重点，大家可以直接去官网查看[官方文档](https://jwt.io)，这里不过多赘述。

相信看到签名大家都很熟悉了，没错，jwt其实并不是什么高深莫测的技术，相反非常简单。认证服务器通过对称或非对称的加密方式利用`payload`生成`signature`，并在`header`中申明签名方式，仅此而已。通过这种本质上极其传统的方式，jwt可以实现**分布式的token验证功能**，即资源服务器通过事先维护好的对称或者非对称密钥（非对称的话就是认证服务器提供的公钥），直接在本地验证token，这种去中心化的验证机制无疑很对现在分布式架构的胃口。jwt相对于传统的token来说，解决以下两个痛点：

- 通过验证签名，token的验证可以直接在本地完成，不需要连接认证服务器
- 在payload中可以定义用户相关信息，这样就轻松实现了token和用户信息的绑定

在上面的那个资源服务器和认证服务器分离的例子中，如果认证服务器颁发的是jwt格式的token，那么资源服务器就可以直接自己验证token的有效性并绑定用户，这无疑大大提升了处理效率且减少了单点隐患。

## Spring Cloud Alibaba Nacos

### Nacos

Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

![image-20200306070542103](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200306070542103.png)

![nacos_landscape.png](https://cdn.nlark.com/lark/0/2018/png/11189/1533045871534-e64b8031-008c-4dfc-b6e8-12a597a003fb.png)

### 基本架构及概念

![nacos_arch.jpg](https://cdn.nlark.com/yuque/0/2019/jpeg/338441/1561217892717-1418fb9b-7faa-4324-87b9-f1740329f564.jpeg)

#### 服务 (Service)

服务是指一个或一组软件功能（例如特定信息的检索或一组操作的执行），其目的是不同的客户端可以为不同的目的重用（例如通过跨进程的网络调用）。Nacos 支持主流的服务生态，如 Kubernetes Service、gRPC|Dubbo RPC Service 或者 Spring Cloud RESTful Service.

#### 服务注册中心 (Service Registry)

服务注册中心，它是服务，其实例及元数据的数据库。服务实例在启动时注册到服务注册表，并在关闭时注销。服务和路由器的客户端查询服务注册表以查找服务的可用实例。服务注册中心可能会调用服务实例的健康检查 API 来验证它是否能够处理请求。

#### 服务元数据 (Service Metadata)

服务元数据是指包括服务端点(endpoints)、服务标签、服务版本号、服务实例权重、路由规则、安全策略等描述服务的数据

服务提供方 (Service Provider)

是指提供可复用和可调用服务的应用方

#### 服务消费方 (Service Consumer)

是指会发起对某个服务调用的应用方

#### 配置 (Configuration)

在系统开发过程中通常会将一些需要变更的参数、变量等从代码中分离出来独立管理，以独立的配置文件的形式存在。目的是让静态的系统工件或者交付物（如 WAR，JAR 包等）更好地和实际的物理运行环境进行适配。配置管理一般包含在系统部署的过程中，由系统管理员或者运维人员完成这个步骤。配置变更是调整系统运行时的行为的有效手段之一。

#### 配置管理 (Configuration Management)

在数据中心中，系统中所有配置的编辑、存储、分发、变更管理、历史版本管理、变更审计等所有与配置相关的活动统称为配置管理。

#### 名字服务 (Naming Service)

提供分布式系统中所有对象(Object)、实体(Entity)的“名字”到关联的元数据之间的映射管理服务，例如 ServiceName -> Endpoints Info, Distributed Lock Name -> Lock Owner/Status Info, DNS Domain Name -> IP List, 服务发现和 DNS 就是名字服务的2大场景。

#### 配置服务 (Configuration Service)

在服务或者应用运行过程中，提供动态配置或者元数据以及配置管理的服务提供者。

### Nacos 系统参数介绍

#### Nacos Server

对于Server端来说，一般是设置在`{nacos.home}/conf/application.properties`里，如果参数名后标注了(-D)的，则表示是 JVM 的参数，需要在`{nacos.home}/bin/startup.sh`里进行相应的设置。例如像设置 nacos.home 的值，可以在`{nacos.home}/bin/startup.sh`进行如下设置：

```
JAVA_OPT="${JAVA_OPT} -Dnacos.home=${BASE_DIR}"
```

#### 全局参数

| 参数名                                  | 含义                                                         | 可选值                       | 默认值          | 支持版本 |
| --------------------------------------- | ------------------------------------------------------------ | ---------------------------- | --------------- | -------- |
| nacos.home(-D)                          | Nacos的根目录                                                | 目录路径                     | Nacos安装的目录 | >= 0.1.0 |
| nacos.standalone(-D)                    | 是否在单机模式                                               | true/false                   | false           | >= 0.1.0 |
| nacos.functionMode(-D)                  | 启动模式，支持只启动某一个模块，不设置时所有模块都会启动     | config/naming/空             | 空              | >= 0.9.0 |
| nacos.inetutils.prefer-hostname-over-ip | `cluster.conf`里是否应该填`hostname`                         | true/false                   | false           | >= 0.3.0 |
| nacos.inetutils.ip-address              | 本机IP，该参数设置后，将会使用这个IP去`cluster.conf`里进行匹配，请确保这个IP的值在`cluster.conf`里是存在的 | 本机IP                       | null            | >= 0.3.0 |
| nacos.security.ignore.urls              | 控制台鉴权跳过的接口                                         | 需要跳过控制台鉴权的接口列表 | 空              | >= 0.9.0 |

#### Naming模块

| 参数名                                 | 含义                               | 可选值     | 默认值 | 支持版本 |
| -------------------------------------- | ---------------------------------- | ---------- | ------ | -------- |
| nacos.naming.data.warmup               | 是否在Server启动时进行数据预热     | true/false | false  | >= 1.0.2 |
| nacos.naming.expireInstance            | 是否自动摘除临时实例               | true/false | true   | >= 1.0.2 |
| nacos.naming.distro.taskDispatchPeriod | 同步任务生成的周期，单位为毫秒     | 正整数     | 200    | >= 1.0.2 |
| nacos.naming.distro.batchSyncKeyCount  | 同步任务每批的key的数目            | 正整数     | 1000   | >= 1.0.2 |
| nacos.naming.distro.syncRetryDelay     | 同步任务失败的重试间隔，单位为毫秒 | 正整数     | 5000   | >= 1.0.2 |

除了上面列到的在`application.properties`里配置的属性，还有一些可以在运行时调用接口来进行调节，这些参数都在[Open API](https://nacos.io/zh-cn/docs/open-api.html)里的`查看系统当前数据指标`这个API里有声明。

#### Config模块

| 参数名      | 含义               | 可选值 | 默认值 | 支持版本 |
| ----------- | ------------------ | ------ | ------ | -------- |
| db.num      | 数据库数目         | 正整数 | 0      | >= 0.1.0 |
| db.url.0    | 第一个数据库的URL  | 字符串 | 空     | >= 0.1.0 |
| db.url.1    | 第二个数据库的URL  | 字符串 | 空     | >= 0.1.0 |
| db.user     | 数据库连接的用户名 | 字符串 | 空     | >= 0.1.0 |
| db.password | 数据库连接的密码   | 字符串 | 空     | >= 0.1.0 |

#### CMDB模块

| 参数名                       | 含义                         | 可选值     | 默认值 | 支持版本 |
| ---------------------------- | ---------------------------- | ---------- | ------ | -------- |
| nacos.cmdb.loadDataAtStart   | 是否打开CMDB                 | true/false | false  | >= 0.7.0 |
| nacos.cmdb.dumpTaskInterval  | 全量dump的间隔，单位为秒     | 正整数     | 3600   | >= 0.7.0 |
| nacos.cmdb.eventTaskInterval | 变更事件的拉取间隔，单位为秒 | 正整数     | 10     | >= 0.7.0 |
| nacos.cmdb.labelTaskInterval | 标签集合的拉取间隔，单位为秒 | 正整数     | 300    | >= 0.7.0 |

#### Nacos Java Client

客户端的参数分为两种，一种是通过-D参数进行指定的配置，一种是构造客户端时，通过`Properties`对象指定的配置，以下没有带-D标注的都是通过`Properties`注入的配置。

#### 通用参数

| 参数名                 | 含义                                                         | 可选值              | 默认值                             | 支持版本 |
| ---------------------- | ------------------------------------------------------------ | ------------------- | ---------------------------------- | -------- |
| endpoint               | 连接Nacos Server指定的连接点，可以参考[文档](https://nacos.io/zh-cn/blog/address-server.html) | 域名                | 空                                 | >= 0.1.0 |
| endpointPort           | 连接Nacos Server指定的连接点端口，可以参考[文档](https://nacos.io/zh-cn/blog/address-server.html) | 合法端口号          | 空                                 | >= 0.1.0 |
| namespace              | 命名空间的ID                                                 | 命名空间的ID        | config模块为空，naming模块为public | >= 0.8.0 |
| serverAddr             | Nacos Server的地址列表，这个值的优先级比endpoint高           | ip:port,ip:port,... | 空                                 | >= 0.1.0 |
| nacos.logging.path(-D) | 客户端日志的目录                                             | 目录路径            | 用户根目录                         | >= 0.1.0 |

#### Naming客户端

| 参数名                                         | 含义                               | 可选值            | 默认值                   | 支持版本 |
| ---------------------------------------------- | ---------------------------------- | ----------------- | ------------------------ | -------- |
| namingLoadCacheAtStart                         | 启动时是否优先读取本地缓存         | true/false        | false                    | >= 1.0.0 |
| namingClientBeatThreadCount                    | 客户端心跳的线程池大小             | 正整数            | 机器的CPU数的一半        | >= 1.0.0 |
| namingPollingThreadCount                       | 客户端定时轮询数据更新的线程池大小 | 正整数            | 机器的CPU数的一半        | >= 1.0.0 |
| com.alibaba.nacos.naming.cache.dir(-D)         | 客户端缓存目录                     | 目录路径          | {user.home}/nacos/naming | >= 1.0.0 |
| com.alibaba.nacos.naming.log.level(-D)         | Naming客户端的日志级别             | info,error,warn等 | info                     | >= 1.0.0 |
| com.alibaba.nacos.client.naming.tls.enable(-D) | 是否打开HTTPS                      | true/false        | false                    | >= 1.0.0 |

#### Config客户端

| 参数名                                                    | 含义                           | 可选值            | 默认值                   | 支持版本 |
| --------------------------------------------------------- | ------------------------------ | ----------------- | ------------------------ | -------- |
| configLongPollTimeout(config.long-poll.timeout 1.0.1版本) | 长轮询的超时时间，单位为毫秒   | 正整数            | 30000                    | >= 1.0.2 |
| configRetryTime(config.retry.time 1.0.1版本)              | 长轮询任务重试时间，单位为毫秒 | 正整数            | 2000                     | >= 1.0.2 |
| maxRetry                                                  | 长轮询的重试次数               | 正整数            | 3                        | >= 1.0.2 |
| enableRemoteSyncConfig                                    | 监听器首次添加时拉取远端配置   | 布尔值            | false                    | >= 1.0.2 |
| com.alibaba.nacos.config.log.level(-D)                    | Config客户端的日志级别         | info,error,warn等 | info                     | >= 1.0.0 |
| JM.SNAPSHOT.PATH(-D)                                      | 客户端缓存目录                 | 目录路径          | {user.home}/nacos/config | >= 1.0.0 |

## Spring Cloud Alibaba Sentinel

### Sentinel

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

Sentinel 具有以下特征:

- **丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
- **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
- **完善的 SPI 扩展点**：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

### 流量控制

流量控制在网络传输中是一个常用的概念，它用于调整网络包的发送数据。然而，从系统稳定性角度考虑，在处理请求的速度上，也有非常多的讲究。任意时间到来的请求往往是随机不可控的，而系统的处理能力是有限的。我们需要根据系统的处理能力对流量进行控制。Sentinel 作为一个调配器，可以根据需要把随机的请求调整成合适的形状，如下图所示：

![img](https://github.com/alibaba/Sentinel/wiki/image/limitflow.gif)

### 工作原理

在 Sentinel 里面，所有的资源都对应一个资源名称（`resourceName`），每次资源调用都会创建一个 `Entry` 对象。Entry 可以通过对主流框架的适配自动创建，也可以通过注解的方式或调用 `SphU` API 显式创建。Entry 创建的时候，同时也会创建一系列功能插槽（slot chain），这些插槽有不同的职责，例如:

- `NodeSelectorSlot` 负责收集资源的路径，并将这些资源的调用路径，以树状结构存储起来，用于根据调用路径来限流降级；
- `ClusterBuilderSlot` 则用于存储资源的统计信息以及调用者信息，例如该资源的 RT, QPS, thread count 等等，这些信息将用作为多维度限流，降级的依据；
- `StatisticSlot` 则用于记录、统计不同纬度的 runtime 指标监控信息；
- `FlowSlot` 则用于根据预设的限流规则以及前面 slot 统计的状态，来进行流量控制；
- `AuthoritySlot` 则根据配置的黑白名单和调用来源信息，来做黑白名单控制；
- `DegradeSlot` 则通过统计信息以及预设的规则，来做熔断降级；
- `SystemSlot` 则通过系统的状态，例如 load1 等，来控制总的入口流量；



## Spring Cloud Alibaba Seata

### Seata 

Seata 是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata 将为用户提供了 AT、TCC、SAGA 和 XA 事务模式，为用户打造一站式的分布式解决方案。

### AT 模式

#### 前提

- 基于支持本地 ACID 事务的关系型数据库。
- Java 应用，通过 JDBC 访问数据库。

#### 整体机制

两阶段提交协议的演变：

- 一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。
- 二阶段：
  - 提交异步化，非常快速地完成。
  - 回滚通过一阶段的回滚日志进行反向补偿。

#### 写隔离

- 一阶段本地事务提交前，需要确保先拿到 **全局锁** 。
- 拿不到 **全局锁** ，不能提交本地事务。
- 拿 **全局锁** 的尝试被限制在一定范围内，超出范围将放弃，并回滚本地事务，释放本地锁。

以一个示例来说明：

两个全局事务 tx1 和 tx2，分别对 a 表的 m 字段进行更新操作，m 的初始值 1000。

tx1 先开始，开启本地事务，拿到本地锁，更新操作 m = 1000 - 100 = 900。本地事务提交前，先拿到该记录的 **全局锁** ，本地提交释放本地锁。 tx2 后开始，开启本地事务，拿到本地锁，更新操作 m = 900 - 100 = 800。本地事务提交前，尝试拿该记录的 **全局锁** ，tx1 全局提交前，该记录的全局锁被 tx1 持有，tx2 需要重试等待 **全局锁** 。

![Write-Isolation: Commit](https://img.alicdn.com/tfs/TB1zaknwVY7gK0jSZKzXXaikpXa-702-521.png)

如果 tx1 的二阶段全局回滚，则 tx1 需要重新获取该数据的本地锁，进行反向补偿的更新操作，实现分支的回滚。

此时，如果 tx2 仍在等待该数据的 **全局锁**，同时持有本地锁，则 tx1 的分支回滚会失败。分支的回滚会一直重试，直到 tx2 的 **全局锁** 等锁超时，放弃 **全局锁** 并回滚本地事务释放本地锁，tx1 的分支回滚最终成功。

因为整个过程 **全局锁** 在 tx1 结束前一直是被 tx1 持有的，所以不会发生 **脏写** 的问题。

#### 读隔离

在数据库本地事务隔离级别 **读已提交（Read Committed）** 或以上的基础上，Seata（AT 模式）的默认全局隔离级别是 **读未提交（Read Uncommitted）** 。

如果应用在特定场景下，必需要求全局的 **读已提交** ，目前 Seata 的方式是通过 SELECT FOR UPDATE 语句的代理。

![Read Isolation: SELECT FOR UPDATE](https://img.alicdn.com/tfs/TB138wuwYj1gK0jSZFuXXcrHpXa-724-521.png)

SELECT FOR UPDATE 语句的执行会申请 **全局锁** ，如果 **全局锁** 被其他事务持有，则释放本地锁（回滚 SELECT FOR UPDATE 语句的本地执行）并重试。这个过程中，查询是被 block 住的，直到 **全局锁** 拿到，即读取的相关数据是 **已提交** 的，才返回。

出于总体性能上的考虑，Seata 目前的方案并没有对所有 SELECT 语句都进行代理，仅针对 FOR UPDATE 的 SELECT 语句。

### TCC 模式

回顾总览中的描述：一个分布式的全局事务，整体是 **两阶段提交** 的模型。全局事务是由若干分支事务组成的，分支事务要满足 **两阶段提交** 的模型要求，即需要每个分支事务都具备自己的：

- 一阶段 prepare 行为
- 二阶段 commit 或 rollback 行为

![Overview of a global transaction](https://img.alicdn.com/tfs/TB14Kguw1H2gK0jSZJnXXaT1FXa-853-482.png)

根据两阶段行为模式的不同，我们将分支事务划分为 **Automatic (Branch) Transaction Mode** 和 **Manual (Branch) Transaction Mode**.

AT 模式（[参考链接 TBD](https://seata.io/zh-cn/docs/overview/what-is-seata.html)）基于 **支持本地 ACID 事务** 的 **关系型数据库**：

- 一阶段 prepare 行为：在本地事务中，一并提交业务数据更新和相应回滚日志记录。
- 二阶段 commit 行为：马上成功结束，**自动** 异步批量清理回滚日志。
- 二阶段 rollback 行为：通过回滚日志，**自动** 生成补偿操作，完成数据回滚。

相应的，TCC 模式，不依赖于底层数据资源的事务支持：

- 一阶段 prepare 行为：调用 **自定义** 的 prepare 逻辑。
- 二阶段 commit 行为：调用 **自定义** 的 commit 逻辑。
- 二阶段 rollback 行为：调用 **自定义** 的 rollback 逻辑。

所谓 TCC 模式，是指支持把 **自定义** 的分支事务纳入到全局事务的管理中。

### Saga 模式

Saga模式是SEATA提供的长事务解决方案，在Saga模式中，业务流程中每个参与者都提交本地事务，当出现某一个参与者失败则补偿前面已经成功的参与者，一阶段正向服务和二阶段补偿服务都由业务开发实现。

![Saga模式示意图](https://img.alicdn.com/tfs/TB1Y2kuw7T2gK0jSZFkXXcIQFXa-445-444.png)

#### Saga的实现：

基于状态机引擎的 Saga 实现：

目前SEATA提供的Saga模式是基于状态机引擎来实现的，机制是：

1. 通过状态图来定义服务调用的流程并生成 json 状态语言定义文件
2. 状态图中一个节点可以是调用一个服务，节点可以配置它的补偿节点
3. 状态图 json 由状态机引擎驱动执行，当出现异常时状态引擎反向执行已成功节点对应的补偿节点将事务回滚

> 注意: 异常发生时是否进行补偿也可由用户自定义决定

1. 可以实现服务编排需求，支持单项选择、并发、子流程、参数转换、参数映射、服务执行状态判断、异常捕获等功能

示例状态图:

![示例状态图](https://seata.io/img/saga/demo_statelang.png?raw=true)

#### 状态机引擎原理:

![状态机引擎原理](https://seata.io/img/saga/saga_engine_mechanism.png?raw=true)

- 图中的状态图是先执行stateA, 再执行stateB，然后执行stateC
- "状态"的执行是基于事件驱动的模型，stateA执行完成后，会产生路由消息放入EventQueue，事件消费端从EventQueue取出消息，执行stateB
- 在整个状态机启动时会调用Seata Server开启分布式事务，并生产xid, 然后记录"状态机实例"启动事件到本地数据库
- 当执行到一个"状态"时会调用Seata Server注册分支事务，并生产branchId, 然后记录"状态实例"开始执行事件到本地数据库
- 当一个"状态"执行完成后会记录"状态实例"执行结束事件到本地数据库, 然后调用Seata Server上报分支事务的状态
- 当整个状态机执行完成, 会记录"状态机实例"执行完成事件到本地数据库, 然后调用Seata Server提交或回滚分布式事务

#### 状态机引擎设计

![状态机引擎设计](https://seata.io/img/saga/saga_engine.png?raw=true)

状态机引擎的设计主要分成三层, 上层依赖下层，从下往上分别是：

- Eventing 层:
  - 实现事件驱动架构, 可以压入事件, 并由消费端消费事件, 本层不关心事件是什么消费端执行什么，由上层实现
- ProcessController 层:
  - 由于上层的Eventing驱动一个“空”流程引擎的执行，"state"的行为和路由都未实现, 由上层实现

> 基于以上两层理论上可以自定义扩展任何"流程"引擎

- StateMachineEngine 层:
  - 实现状态机引擎每种state的行为和路由逻辑
  - 提供 API、状态机语言仓库

#### 状态机的高可用设计

![状态机的高可用](https://seata.io/img/saga/SagaEngineHA.png?raw=true)



状态机引擎是无状态的，它是内嵌在应用中。

当应用正常运行时（图中上半部分）：

- 状态机引擎会上报状态到Seata Server；
- 状态机执行日志存储在业务的数据库中；

当一台应用实例宕机时（图中下半部分）：

- Seata Server 会感知到，并发送事务恢复请求到还存活的应用实例；
- 状态机引擎收到事务恢复请求后，从数据库里装载日志，并恢复状态机上下文继续执行