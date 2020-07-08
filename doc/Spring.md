## Spring

### Spring核心 

#### spring

Spring是一个开源框架，最早由Rod Johnson创建，并在《Expert One-on-One：J2EE Design and Development》 这本著作中进行了介绍。Spring是为了解决企业级应用开发的复杂性而创建的，使用Spring可以让简单的 JavaBean实现之前只有EJB才能完成的事情。但Spring不仅仅局限于服务器端开发，任何Java应用都能在简单性、可测试性和松耦合等方面从 Spring中获益。

#### Spring架构

![image-20200226132009311](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226132009311.png)

❑ Spring IoC：包含了最为基本的IoC容器BeanFactory的接口与实现，也就是说，在这个Spring的核心包中，不仅定义了IoC容器的最基本接口（BeanFactory），也提供了一系列这个接口的实现，如XmlBeanFactory就是一个最基本的BeanFactory（IoC容器），从名字上可以看到，它能够支持通过XML文件配置的Bean定义信息。除此之外，Spring IoC容器还提供了一个容器系列，如SimpleJndiBeanFactory、StaticListableBeanFactory等。我们知道，单纯一个IoC容器对于应用开发来说是不够的，为了让应用更方便地使用IoC容器，还需要在IoC容器的外围提供其他的支持，这些支持包括Resource访问资源的抽象和定位等，所有的这些，都是这个Spring IoC模块的基本内容。另外，在BeanFactory接口实现中，除了前面介绍的像BeanFactory那样最为基本的容器形态之外，Spring还设计了IoC容器的高级形态ApplicationContext应用上下文供用户使用，这些ApplicationContext应用上下文，如FileSystemXmlApplicationContext、ClassPathXmlApplicationContext，对应用来说，是IoC容器中更面向框架的使用方式，同样，为了便于应用开发，像国际化的消息源和应用支持事件这些特性，也都在这个模块中配合IoC容器来实现，这些功能围绕着IoC基本容器和应用上下文的实现，构成了整个Spring IoC模块设计的主要内容。

❑ Spring AOP：这也是Spring的核心模块，围绕着AOP的增强功能，Spring集成了AspectJ作为AOP的一个特定实现，同时还在JVM动态代理/CGLIB的基础上，实现了一个AOP框架，作为Spring集成其他模块的工具，比如TransactionProxyFactoryBean声明式事务处理，就是通过AOP集成到Spring中的。在这个模块中，Spring AOP实现了一个完整的建立AOP代理对象，实现AOP拦截器，直至实现各种Advice通知的过程。在对这个模块的分析中可以看到，AOP模块的完整实现是我们熟悉AOP实现技术的一个不可多得的样本。

❑ Spring MVC：对于大多数企业应用而言，Web应用已经是一种普遍的软件发布方式，而在Web应用的设计中，MVC模式已经被广泛使用了。在Java的社区中，也有很多类似的MVC框架可以选择，而且这些框架往往和Web UI设计整合在一起，对于定位于提供整体平台解决方案的Spring，这样的整合也是不可缺少的。Spring MVC就是这样一个模块，这个模块以DispatcherServlet为核心，实现了MVC模式，包括怎样与Web容器环境的集成，Web请求的拦截、分发、处理和ModelAndView数据的返回，以及如何集成各种UI视图展现和数据表现，如PDF、Excel等，通过这个模块，可以完成Web的前端设计。

❑ Spring JDBC/Spring ORM：在企业应用中，对以关系数据库为基础的数据的处理是企业应用的一个重要方面，而对于关系数据库的处理，Java提供了JDBC来进行操作，但在实际的应用中，单纯使用JDBC的方式还是有些繁琐，所以在JDBC规范的基础上，Spring对JDBC做了一层封装，使通过JDBC完成的对数据库的操作更加简洁。Spring JDBC包提供了JdbcTemplate作为模板类，封装了基本的数据库操作方法，如数据的查询、更新等；另外，SpringJDBC还提供了RDBMS的操作对象，这些操作对象可以使应用以更面向对象的方法来使用JDBC，比如可以使用MappingSqlQuery将数据库数据记录直接映射到对象集合，类似一个极为简单的ORM工具。除了通过Spring JDBC对数据库进行操作外，Spring还提供了许多对ORM工具的封装，这些封装包括了常用的ORM工具，如Hibernate、iBatis等，这一层封装的作用是让应用更方便地使用这些ORM工具，而不是替代这些ORM工具，比如可以把对这些工具的使用和Spring提供的声明式事务处理结合起来。同时，Spring还提供了许多模板对象，如HibernateTemaplate这样的工具来实现对Hibernate的驱动，这些模板对象往往包装使用Hibernate的一些通用过程，比如Session的获取和关闭、事务处理的关联等，从而把一些通用的特性实现抽象到Spring中来，更充分地体现了Spring的平台作用。

❑ Spring事务处理：Spring事务处理是一个通过Spring AOP实现自身功能增强的典型模块。在这个模块中，Spring把在企业应用开发中事务处理的主要过程抽象出来，并且简洁地通过AOP的切面增强实现了声明式事务处理的功能。这个声明式事务处理的实现，使应用只需要在IoC容器中对事务属性进行配置即可完成，同时，这些事务处理的基本过程和具体的事务处理器实现是无关的，也就是说，应用可以选择不同的具体的事务处理机制，如JTA、JDBC、Hibernate等。因为使用了声明式事务处理，这些具体的事务处理机制被纳入Spring事务处理的统一框架中完成，并完成与具体业务代码的解耦。在这个模块中，可以看到一个通用的实现声明式事务处理的基本过程，比如怎样配置事务处理的拦截器，怎样读入事务配置属性，并结合这些事务配置属性对事务对象进行处理，包括事务的创建、挂起、提交、回滚等基本过程，还可以看到具体的事务处理器（如DataSourceTransactionManager、HibernateTransactionManager、JtaTransactionManager等）是怎样封装不同的事务处理机制（JDBC、Hibernate、JTA等）的。

❑ Spring远端调用：Spring为应用带来的一个好处就是能够将应用解耦。应用解耦，一方面可以降低设计的复杂性，另一方面，可以在解耦以后将应用模块分布式地部署，从而提高系统整体的性能。在后一种应用场景下，会用到Spring的远端调用，这种远端调用是通过Spring的封装从Spring应用到Spring应用之间的端到端调用。在这个过程中，通过Spring的封装，为应用屏蔽了各种通信和调用细节的实现，同时，通过这一层的封装，使应用可以通过选择各种不同的远端调用来实现，比如可以使用HTTP调用器（以HTTP协议为基础的），可以使用第三方的二进制通信实现Hessian/Burlap，甚至还封装了传统Java技术中的RMI调用。

❑ Spring应用：从严格意义上来说，这个模块不属于Spring的范围。这部分的应用支持，往往来自一些使用得非常广泛的Spring子项目，或者该子项目本身就可以看成是一个独立的Spring应用，比如为Spring处理安全问题的Spring ACEGI后来转化为Spring子项目的Spring Security OAuth等。这个Spring应用支持的部分还有一个重要的组成，那就是包括了其他的一些模块，这些模块提供了许多Spring应用与其他技术实现的相关接口，比如与各种J2EE实现规范的接口，对JMS、JNID、JMX、JavaMail等的支持，Spring应用和Flex前端的接口，Spring应用移植到OSGi平台上运行的接口。通过这个模块的支持，使Spring应用可以便利和简洁地容纳第三方的技术实现，不但丰富了Spring应用的功能，而且丰富了整个Spring生态圈，使Spring应用得越来越广泛。

#### Spring AOP

在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

#### 应用上下文 

Spring自带了多种类型的应用上下文。下面罗列的几个是你最有可能遇到的。 

AnnotationConfigApplicationContext：从一个或多个基于Java的配置类中加载Spring应用上下文。 

AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文。 

ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类 资源。 

FileSystemXmlapplicationcontext：从文件系统下的一个或多个XML配置文件中加载上下文定义。 

XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义。 

#### bean的生命周期 

容器的实现是通过IoC管理Bean的生命周期来实现的。Spring IoC容器在对Bean的生命周期进行管理时提供了Bean生命周期各个时间点的回调。在分析Bean初始化和销毁过程的设计之前，简要介绍一下IoC容器中的Bean生命周期。

❍Bean实例的创建。

❍为Bean实例设置属性。

❍调用Bean的初始化方法。

❍应用可以通过IoC容器使用Bean。

❍当容器关闭时，调用Bean的销毁方法。

![image-20200511133935663](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511133935663.png)

![image-20200511133946920](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511133946920.png)

### 

### 装配Bean

#### Spring配置的可选方案 

Spring容器负责创建应用程序中的bean并通过DI来协调这些对象之间的关系。但是，作为开发人员，你需要告诉Spring要创 建哪些bean并且如何将其装配在一起。当描述bean如何进行装配时，Spring具有非常大的灵活性，它提供了三种主要的装配机制： 在XML中进行显式配置。 在Java中进行显式配置。 隐式的bean发现机制和自动装配。 

#### 自动化装配bean 

**自动装配优势：**

- 便利，自动化装配，隐式配置代码量少。

**自动装配限制：**

- 基本数据类型的值、字符串字面量、类字面量无法使用自动装配来注入。
- 装配依赖中若是出现匹配到多个bean（出现歧义性），装配将会失败。

**Spring实现自动装配两个步骤：**

- 组件扫描（component scanning）：Spring会扫描@Component注解的类，并会在应用上下文中为这个类创建一个bean。
- 自动装配（autowiring）：Spring自动满足bean之间的依赖。

**使用到的注解：**

- @Component：表明这个类作为组件类，并告知Spring要为这个类创建bean。默认bean的id为第一个字母为小写类名，可以用value指定bean的id。
- @Configuration：代表这个类是配置类。
- @ComponentScan：启动组件扫描，默认会扫描所在包以及包下所有子包中带有@Component注解的类，并会在Spring容器中为其创建一个bean。可以用basePackages属性指定包。
- @RunWith(SpringJUnit4ClassRunner.class)：以便在测试开始时，自动创建Spring应用上下文。
- @ContextConfiguration：告诉在哪加载配置。
- @Autowired：将一个类的依赖bean装配进来。

#### 通过Java代码装配

**优点：**

- 可以实现基本数据类型的值、字符串字面量、类字面量等注入。

　　**使用到的注解：**

- @Bean：默认情况下配置后bean的id和注解的方法名一样，可以通过name属性自定义id。
- @ImportResourse：将指定的XML配置加载进来
- @Import：将指定的Java配置加载进来。
- 注：不会用到@Component与@Autowired注解。

#### 通过XML装配 

**优点：**什么都能做。

**缺点：**配置繁琐。

　　**使用到的标签：**

- <bean>:将类装配为bean，也可以导入java配置。属性id是为bean指定id，class是导入的类。
- <constructor-arg>:构造器中声明DI，属性value是注入值，ref是注入对象引用。
- spring的c-命名空间：起着和<constructor-arg>相似的作用。
- <property>：设置属性，name是方法中参数名字，ref是注入的对象。
- Spring的p-命名空间：起着和<property>相似的作用。
- <import>：导入其他的XML配置。属性resource是导入XML配置的名称。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

#### Bean的配置

![image-20200226141821530](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226141821530.png)

下面我们先来了解Spring Bean的初始化流程，其流程如图3-3所示。

![image-20200423010024833](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200423010024833.png)

![image-20200423010048782](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200423010048782.png)



### 高级装配 

#### 环境与profile

在开发软件的时候，常常会将应用程序从一个环境迁移到另一个环境，比如在开发阶段中，某些环境相关的做法并不太适合迁移到生产环境中，甚至即便迁移过去也无法正常工作，比如数据库配置等。可以通过配置 bean profile，依据不同的环境，创建对应的bean。

profile配置的方式
（1）在配置类中的配置
@Profile即可以在类上配置，也可以在方法上配置。通常为了方便管理和维护，将不同环境下的配置放置在一个同一个配置类中。

```java
@Configuration
public class DataSourceConfig{
	@Bean
	@Profile("dev")
	public DataSource devDataSource(){
	....
	}
	@Bean
	@Profile("prod")
	public DataSource prodDataSource(){
	....
	}
}
```

（2）在xml中配置，也有两种方式，第一种是在根元素里面配置profile=“dev”,这就需要有几个不同的环境，就需要定义几个不同的xml。第二种方法是在根元素里面，定义一个子，然后在子中声明profile的配置。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd"
		profile="dev">
```

```xml
	<beans profile="dev">
			...
	</beans>
	 
	<beans profile="prod">
		...
	</beans>
```

#### bean的作用域

![image-20200226141847199](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226141847199.png)

#### 运行时值注入

- 在Spring中实现运行时注入值的方法有三种：
  - 通过`Environment`类来检索属性
  - 通过属性占位符
  - 通过Spring表达式语句SpEL

1) 通过`Environment`检索属性

步骤：

1. 通过@PropertySource指定属性源
2. 通过Environment检索属性

```java
@Configuration
# 1. 指定属性源
@PropertySource("classpath:application.properties") 
public class ConfigTest {
    private int maximumSize;
    private int expireTime;

    # 2. 注入Environment
    @Autowired
    private Environment environment;

    @Bean
    public LoadingCache<Integer, Integer> testLoadingCache() {
        # 通过getProperty获取值
        maximumSize = environment.getProperty("guava.loadingcache.maximumsize", Integer.class);
        expireTime = environment.getProperty("guava.loadingcache.expiretime", Integer.class);
        return CacheBuilder.newBuilder()
                .maximumSize(maximumSize)
                .expireAfterAccess(expireTime, TimeUnit.SECONDS)
                .build(
                        new CacheLoader<Integer, Integer>() {
                            @Override
                            public Integer load(Integer key) throws Exception {
                                return key + 1;
                            }
                        }
                );
    }
}
```

2) 通过属性占位符

属性占位符需要用到@Value属性，形如`@Value("${property.name}")`
代码如下：

```java
@Configuration
public class ConfigTest {
    @Value("${guava.loadingcache.maximumsize}")
    private int maximumSize;
    
    @Value("${guava.loadingcache.expiretime}")
    private int expireTime;

    @Autowired
    private Environment environment;

    @Bean
    public LoadingCache<Integer, Integer> testLoadingCache() {
        return CacheBuilder.newBuilder()
                .maximumSize(maximumSize)
                .expireAfterAccess(expireTime, TimeUnit.SECONDS)
                .build(
                        new CacheLoader<Integer, Integer>() {
                            @Override
                            public Integer load(Integer key) throws Exception {
                                return key + 1;
                            }
                        }
                );
    }
}
```

需要注意的是，采用这种方法必须提供`PropertyPlaceholderConfigurer`bean或者`PropertySourcesPlaceholderConfigurer`bean。有如下两种方式：

1. JavaConfig中配置`PropertySourcesPlaceholderConfigurer`

2. 配置文件根节点下添加

   ```xml
   <context:property-placeholder/>
   ```

3) 通过Spring表达式SpEL

SpEL功能很强大，形如@Value("#{expression}")。它和属性占位符的区别在于：

1. 不需要`PropertySourcesPlaceholderConfigurer`bean的帮助
2. expression能够执行简单的运算，调用其他对象或者bean中的方法

### IoC容器的实现

#### 注入实现

具体的注入实现中，接口注入（Type 1 IoC）、setter注入（Type 2 IoC）、构造器注入（Type 3 IoC）是主要的注入方式。在Spring的IoC设计中，setter注入和构造器注入是主要的注入方式；相对而言，使用Spring时setter注入是常见的注入方式，而且为了防止注入异常，Spring IoC容器还提供了对特定依赖的检查。

#### 依赖注入实现方式

依赖注入是时下最流行的IoC实现方式，依赖注入分为接口注入（Interface Injection），Setter方法注入（Setter Injection）和构造器注入（Constructor Injection）三种方式。其中接口注入由于在灵活性和易用性比较差，现在从Spring4开始已被废弃。

构造器依赖注入：构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。

Setter方法注入：Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。

#### 构造器依赖注入和 Setter方法注入的区别

![img](https://pic2.zhimg.com/80/v2-95c72dadfe21c671b0ec1621c2085f45_720w.jpg)

#### Spring IOC容器的设计

**控制反转**（Inversion of Control，缩写为**IoC**），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做**依赖注入**（Dependency Injection，简称**DI**），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

![image-20200315222015103](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200315222015103.png)

在使用IoC容器时，需要如下几个步骤：

1）创建IoC配置文件的抽象资源，这个抽象资源包含了BeanDefinition的定义信息。

2）创建一个BeanFactory，这里使用DefaultListableBeanFactory。

3）创建一个载入BeanDefinition的读取器，这里使用XmlBeanDefinitionReader来载入XML文件形式的BeanDefinition，通过一个回调配置给BeanFactory。

4）从定义好的资源位置读入配置信息，具体的解析过程由XmlBeanDefinitionReader来完成。完成整个载入和注册Bean定义之后，需要的IoC容器就建立起来了。这个时候就可以直接使用IoC容器了。

#### IoC容器的初始化过程

第一个过程是Resource定位过程。这个Resource定位指的是BeanDefinition的资源定位，它由ResourceLoader通过统一的Resource接口来完成，这个Resource对各种形式的BeanDefinition的使用都提供了统一接口。对于这些BeanDefinition的存在形式，相信大家都不会感到陌生。比如，在文件系统中的Bean定义信息可以使用FileSystemResource来进行抽象；在类路径中的Bean定义信息可以使用前面提到的ClassPathResource来使用，等等。这个定位过程类似于容器寻找数据的过程，就像用水桶装水先要把水找到一样。

第二个过程是BeanDefinition的载入。这个载入过程是把用户定义好的Bean表示成IoC容器内部的数据结构，而这个容器内部的数据结构就是BeanDefinition。下面介绍这个数据结构的详细定义。具体来说，这个BeanDefinition实际上就是POJO对象在IoC容器中的抽象，通过这个BeanDefinition定义的数据结构，使IoC容器能够方便地对POJO对象也就是Bean进行管理。

第三个过程是向IoC容器注册这些BeanDefinition的过程。这个过程是通过调用BeanDefinitionRegistry接口的实现来完成的。这个注册过程把载入过程中解析得到的BeanDefinition向IoC容器进行注册。通过分析，我们可以看到，在IoC容器内部将BeanDefinition注入到一个HashMap中去，IoC容器就是通过这个HashMap来持有这些BeanDefinition数据的。

#### IoC容器的依赖注入

![image-20200322013429824](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322013429824.png)

重点来说，getBean是依赖注入的起点，之后会调用createBean，下面通过createBean代码来了解这个实现过程。在这个过程中，Bean对象会依据BeanDefinition定义的要求生成。在AbstractAutowireCapableBeanFactory中实现了这个createBean，createBean不但生成了需要的Bean，还对Bean初始化进行了处理，比如实现了在BeanDefinition中的init-method属性定义，Bean后置处理器等。

#### autowiring的实现

在对autowiring类型做了一些简单的逻辑判断以后，通过调用autowireByName和autowireByType来完成自动依赖装配。

对autowireByName来说，它首先需要得到当前Bean的属性名，这些属性名已经在BeanWrapper和BeanDefinition中封装好了，然后是对这一系列属性名进行匹配的过程。在匹配的过程中，因为已经有了属性的名字，所以可以直接使用属性名作为Bean名字向容器索取Bean，这个getBean会触发当前Bean的依赖Bean的依赖注入，从而得到属性对应的依赖Bean。在执行完这个getBean后，把这个依赖Bean注入到当前Bean的属性中去，这样就完成了通过这个依赖属性名自动完成依赖注入的过程。

### 面向切面编程

#### 面向切面编程

在软件业，AOP为Aspect Oriented Programming的缩写，意为：[面向切面编程](https://baike.baidu.com/item/面向切面编程/6016335)，通过[预编译](https://baike.baidu.com/item/预编译/3191547)方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是[OOP](https://baike.baidu.com/item/OOP)的延续，是软件开发中的一个热点，也是[Spring](https://baike.baidu.com/item/Spring)框架中的一个重要内容，是[函数式编程](https://baike.baidu.com/item/函数式编程/4035031)的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](https://baike.baidu.com/item/耦合度/2603938)降低，提高程序的可重用性，同时提高了开发的效率。

#### AOP术语 

1）连接点（Joinpoint）
程序执行的某个特定位置：如类开始初始化前、类初始化后、类某个方法调用前、调用后、方法抛出异常后。一个类或一段程序代码拥有一些具有边界性质的特定点，这些点中的特定点就称为“连接点”。Spring仅支持方法的连接点，即仅能在方法调用前、方法调用后、方法抛出异常时以及方法调用前后这些程序执行点织入增强。连接点由两个信息确定：第一是用方法表示的程序执行点；第二是用相对点表示的方位。

2）切点（Pointcut）
每个程序类都拥有多个连接点，如一个拥有两个方法的类，这两个方法都是连接点，即连接点是程序类中客观存在的事物。AOP通过“切点”定位特定的连接点。连接点相当于数据库中的记录，而切点相当于查询条件。切点和连接点不是一对一的关系，一个切点可以匹配多个连接点。在Spring中，切点通过org.springframework.aop.Pointcut接口进行描述，它使用类和方法作为连接点的查询条件，Spring AOP的规则解析引擎负责切点所设定的查询条件，找到对应的连接点。其实确切地说，不能称之为查询连接点，因为连接点是方法执行前、执行后等包括方位信息的具体程序执行点，而切点只定位到某个方法上，所以如果希望定位到具体连接点上，还需要提供方位信息。

3）通知（Advice）
通知是织入到目标类连接点上的一段程序代码，在Spring中，通知除用于描述一段程序代码外，还拥有另一个和连接点相关的信息，这便是执行点的方位。结合执行点方位信息和切点信息，我们就可以找到特定的连接点。

Spring切面可以应用5种类型的通知：

​    前置通知（Before）：在目标方法被调用之前调用通知功能；

​    后置通知（After）：在目标方法完成之后调用通知，此时不会关心方法的输出是什么；

​    返回通知（After-returning）：在目标方法成功执行之后调用通知；

​    异常通知（After-throwing）：在目标方法抛出异常后调用通知；

​    环绕通知（Around）：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。

4）目标对象（Target）
增强逻辑的织入目标类。如果没有AOP，目标业务类需要自己实现所有逻辑，而在AOP的帮助下，目标业务类只实现那些非横切逻辑的程序逻辑，而性能监视和事务管理等这些横切逻辑则可以使用AOP动态织入到特定的连接点上。

5）引介（Introduction）
引介是一种特殊的增强，它为类添加一些属性和方法。这样，即使一个业务类原本没有实现某个接口，通过AOP的引介功能，我们可以动态地为该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。

6）织入（Weaving）
织入是将增强添加对目标类具体连接点上的过程。AOP像一台织布机，将目标类、增强或引介通过AOP这台织布机天衣无缝地编织到一起。根据不同的实现技术，AOP有三种织入的方式：
a、编译期织入，这要求使用特殊的Java编译器。
b、类装载期织入，这要求使用特殊的类装载器。
c、动态代理织入，在运行期为目标类添加增强生成子类的方式。
Spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入。

7）代理（Proxy）
一个类被AOP织入增强后，就产出了一个结果类，它是融合了原类和增强逻辑的代理类。根据不同的代理方式，代理类既可能是和原类具有相同接口的类，也可能就是原类的子类，所以我们可以采用调用原类相同的方式调用代理类。

8）切面（Aspect）
切面由切点和增强（引介）组成，它既包括了横切逻辑的定义，也包括了连接点的定义，Spring AOP就是负责实施切面的框架，它将切面所定义的横切逻辑织入到切面所指定的连接点中。

#### Spring对 对AOP的支持

 Spring提供了4种类型的AOP支持：

- 基于代理的经典Spring AOP；
- 纯POJO切面；
- @AspectJ注解驱动的切面；
- 注入式AspectJ切面（适用于Spring各版本）。 

#### 通过切点来选择连接点

![image-20200322021044596](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322021044596.png)

在Spring中尝试使用AspectJ其他指示器时，将会抛出IllegalArgument-Exception异常。 当我们查看如上所展示的这些Spring支持的指示器时，注意只有execution指示器是实际执行匹配的，而其他的指示器都是用来限制匹配 的。这说明execution指示器是我们在编写切点定义时最主要使用的指示器。在此基础上，我们使用其他指示器来限制所匹配的切点。 

#### 使用注解创建切面

![img](https://images2018.cnblogs.com/blog/1233668/201805/1233668-20180503142536052-579162207.png)

#### 在XML中声明切面

![img](https://img-blog.csdn.net/20180418112620504)

```xml
<aop:config>					<!-- AOP定义开始 -->
		<aop:pointcut/>				<!-- 定义切入点 -->
		<aop:advisor/>				<!-- 定义AOP通知器 -->
		<aop:aspect>				<!-- 定义切面开始 -->
			<aop:pointcut/>			<!-- 定义切入点 -->
			<aop:before/>			<!-- 前置通知 -->
			<aop:after-returning/>	        <!-- 后置返回通知 -->
			<aop:after-throwing/>           <!-- 后置异常通知 -->
			<aop:after/>			<!-- 后置通知（不管通知的方法是否执行成功） -->
			<aop:around/>			<!-- 环绕通知 -->
			<aop:declare-parents/>          <!-- 引入通知  -->
		</aop:aspect>				<!-- 定义切面结束 -->
	</aop:config>					<!-- AOP定义结束 -->
```

#### 注入AspectJ切面

AspectJ 切面与 Spring 是相互独立的。但是如果在执行通知时，切面依赖一个或多个类，我们可以借助 Spring 的依赖注入把 bean 装配紧 AspectJ 切面中。

### Spring AOP的实现

#### 动态代理特性

在Spring AOP实现中，使用的核心技术是动态代理，而这种动态代理实际上是JDK的一个特性（在JDK 1.3以上的版本里，实现了动态代理模式）。通过JDK的动态代理特性，可以为任意Java对象创建代理对象，对于具体使用来说，这个特性是通过Java Reflection API来完成的。

#### 代理模式实现方式

- 静态代理，工程师编辑代理类代码，实现代理模式；在编译期就生成了代理类。
- 基于 JDK 实现动态代理，通过jdk提供的工具方法Proxy.newProxyInstance动态构建全新的代理类(继承Proxy类，并持有InvocationHandler接口引用 )字节码文件并实例化对象返回。(jdk动态代理是由java内部的反射机制来实例化代理对象，并代理的调用委托类方法)
- 基于CGlib 动态代理模式 基于继承被代理类生成代理子类，不用实现接口。只需要被代理类是非final 类即可。(cglib动态代理底层是借助asm字节码技术
- 基于 Aspectj 实现动态代理（修改目标类的字节，织入代理的字节，在程序编译的时候 插入动态代理的字节码，不会生成全新的Class ）
- 基于 instrumentation 实现动态代理（修改目标类的字节码、类装载的时候动态拦截去修改，基于javaagent） `-javaagent:spring-instrument-4.3.8.RELEASE.jar` （类装载的时候 插入动态代理的字节码，不会生成全新的Class ）

#### 建立AopProxy代理对象

在Spring的AOP模块中，一个主要的部分是代理对象的生成，而对于Spring应用，可以看到，是通过配置和调用Spring的ProxyFactoryBean来完成这个任务的。在ProxyFactoryBean中，封装了主要代理对象的生成过程。在这个生成过程中，可以使用JDK的Proxy和CGLIB两种生成方式。

以ProxyFactory的设计为中心，可以看到相关的类继承关系如图3-11所示。

![image-20200322014508529](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322014508529.png)

#### 配置ProxyFactoryBean

在基于XML配置Spring的Bean时，往往需要一系列的配置步骤来使用ProxyFactoryBean和AOP。

1）定义使用的通知器Advisor，这个通知器应该作为一个Bean来定义。很重要的一点是，这个通知器的实现定义了需要对目标对象进行增强的切面行为，也就是Advice通知。

2）定义ProxyFactoryBean，把它作为另一个Bean来定义，它是封装AOP功能的主要类。在配置ProxyFactoryBean时，需要设定与AOP实现相关的重要属性，比如proxyInterface、interceptorNames和target等。从属性名称可以看出，interceptorNames属性的值往往设置为需要定义的通知器，因为这些通知器在ProxyFactoryBean的AOP配置下，是通过使用代理对象的拦截器机制起作用的。所以，这里依然沿用了拦截器这个名字，也算是旧瓶装新酒吧。

3）定义target属性，作为target属性注入的Bean，是需要用AOP通知器中的切面应用来增强的对象，也就是前面提到的base对象。

#### 生成AopProxy代理对象

在ProxyFactoryBean中，通过interceptorNames属性来配置已经定义好的通知器Advisor。虽然名字为interceptorNames，但实际上却是供AOP应用配置通知器的地方。在ProxyFactoryBean中，需要为target目标对象生成Proxy代理对象，从而为AOP横切面的编织做好准备工作。

![image-20200322014753321](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322014753321.png)

在AopProxy代理对象的生成过程中，首先要从AdvisedSupport对象中取得配置的目标对象，这个目标对象是实现AOP功能所必需的，道理很简单，AOP完成的是切面应用对目标对象的增强，皮之不存，毛将焉附，这个目标对象可以看做是“皮”，而AOP切面增强就是依附于这块皮上的“毛”。如果这里没有配置目标对象，会直接抛出异常，提醒AOP应用, 需要提供正确的目标对象的配置。在对目标对象配置的检查完成以后，需要根据配置的情况来决定使用什么方式来创建AopProxy代理对象，一般而言，默认的方式是使用JDK来产生AopProxy代理对象，但是如果遇到配置的目标对象不是接口类的实现，会使用CGLIB来产生AopProxy代理对象；在使用CGLIB来产生AopProxy代理对象时，因为CGLIB是一个第三方的类库，本身不在JDK的基本类库中，所以需要在CLASSPATH路径中进行正确的配置，以便能够加载和使用。

#### JDK生成AopProxy代理对象

![image-20200322015002393](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322015002393.png)

在JdkDynamicAopProxy中，使用了JDK的Proxy类来生成代理对象，在生成Proxy对象之前，首先需要从advised对象中取得代理对象的代理接口配置，然后调用Proxy的newProxyInstance方法，最终得到对应的Proxy代理对象。在生成代理对象时，需要指明三个参数，一个是类装载器，一个是代理接口，另外一个就是Proxy回调方法所在的对象，这个对象需要实现InvocationHandler接口。这个InvocationHandler接口定义了invoke方法，提供代理对象的回调入口。对于JdkDynamicAopProxy，它本身实现了InvocationHandler接口和invoke方法，这个invoke方法是Proxy代理对象的回调方法，所以可以使用this来把JdkDynamicAopProxy指派给Proxy对象，也就是说JdkDynamicAopProxy对象本身，在Proxy代理的接口方法被调用时，会触发invoke方法的回调，这个回调方法完成了AOP编织实现的封装。

#### JDK动态代理特点总结

- 生成的代理类：$Proxy0 extends Proxy implements Person，我们看到代理类继承了Proxy类，Java的继承机制决定了JDK动态代理类们无法实现对 类 的动态代理。所以也就决定了java动态代理只能对接口进行代理
- 每个生成的动态代理实例都会关联一个调用处理器对象，可以通过 Proxy 提供的静态方法 getInvocationHandler 去获得代理类实例的调用处理器对象。在代理类实例上调用其代理的接口中所声明的方法时，这些方法最终都会由调用处理器的 invoke 方法执行
- 代理类的根类 java.lang.Object 中有三个方法也同样会被分派到调用处理器的 invoke 方法执行，它们是 hashCode，equals 和 toString，可能的原因有：一是因为这些方法为 public 且非 final 类型，能够被代理类覆盖； 二是因为这些方法往往呈现出一个类的某种特征属性，具有一定的区分度，所以为了保证代理类与委托类对外的一致性，这三个方法也应该被调用处理器分派到委托类执行。

#### JDK动态代理不足

JDK动态代理的代理类字节码在创建时，需要实现业务实现类所实现的接口作为参数。如果业务实现类是没有实现接口而是直接定义业务方法的话，就无法使用JDK动态代理了。(JDK动态代理重要特点是代理接口) 并且，如果业务实现类中新增了接口中没有的方法，这些方法是无法被代理的（因为无法被调用）。

动态代理只能对接口产生代理，不能对类产生代理。

#### 基于CGlib 技术

Cglib是针对类来实现代理的，他的原理是对代理的目标类生成一个子类，并覆盖其中方法实现增强，因为底层是基于创建被代理类的一个子类，所以它避免了JDK动态代理类的缺陷。

但因为采用的是继承，所以不能对final修饰的类进行代理。final修饰的类不可继承。

导入maven 依赖

cglib 是基于asm 字节修改技术。导入 cglib 会间接导入 asm, ant, ant-launcher 三个jar 包。

```javascript
<!-- cglib 动态代理依赖 begin -->
 <dependency>
   <groupId>cglib</groupId>
   <artifactId>cglib</artifactId>
   <version>3.2.5</version></dependency><!-- cglib 动态代理依赖 stop -->
```

业务类实现

cglib是针对类来实现代理的，原理是对指定的业务类生成他的一个子类，并覆盖其中的业务方法来实现代理。因为采用的是继承，所以不能对final修饰的类进行代理。

```javascript
package org.vincent.proxy.cglibproxy;/**
* @Package: org.vincent.proxy.cglibproxy <br/>
* @Description： Cglib 代理模式中 被代理的委托类 <br/>
* @author: lenovo <br/>
* @Company: PLCC <br/>
* @Copyright: Copyright (c) 2019 <br/>
* @Version: 1.0 <br/>
* @Modified By: <br/>
* @Created by lenovo on 2018/12/26-17:55 <br/>
*/public class Dog {    public String  call() {
       System.out.println("wang wang wang");        return "Dog ..";
   }
}
```

方法拦截器 实现 MethodInterceptor 接口

```javascript
package org.vincent.proxy.cglibproxy;import net.sf.cglib.proxy.Enhancer;import net.sf.cglib.proxy.MethodInterceptor;import net.sf.cglib.proxy.MethodProxy;import java.lang.reflect.Method;/**
* @Package: org.vincent.proxy.cglibproxy <br/>
* @Description： Cglib 方法拦截器,不用依赖被代理业务类的引用。  <br/>
* @author: lenovo <br/>
* @Company: PLCC <br/>
* @Copyright: Copyright (c) 2019 <br/>
* @Version: 1.0 <br/>
* @Modified By: <br/>
* @Created by lenovo on 2018/12/26-17:56 <br/>
*/public class CglibMethodInterceptor implements MethodInterceptor {    /**
    * 用于生成 Cglib 动态代理类工具方法
    * @param target 代表需要 被代理的 委托类的 Class 对象
    * @return
    */
   public Object CglibProxyGeneratory(Class target) {        /** 创建cglib 代理类 start */
       // 创建加强器，用来创建动态代理类
       Enhancer enhancer = new Enhancer();        // 为代理类指定需要代理的类，也即是父类
       enhancer.setSuperclass(target);        // 设置方法拦截器回调引用，对于代理类上所有方法的调用，都会调用CallBack，而Callback则需要实现intercept() 方法进行拦截
       enhancer.setCallback(this);        // 获取动态代理类对象并返回
       return enhancer.create();        /** 创建cglib 代理类 end */
   }    /**
    * 功能主要是在调用业务类方法之前 之后添加统计时间的方法逻辑.
    * intercept 因为  具有 MethodProxy proxy 参数的原因 不再需要代理类的引用对象了,直接通过proxy 对象访问被代理对象的方法(这种方式更快)。
    * 当然 也可以通过反射机制，通过 method 引用实例    Object result = method.invoke(target, args); 形式反射调用被代理类方法，
    * target 实例代表被代理类对象引用, 初始化 CglibMethodInterceptor 时候被赋值 。但是Cglib不推荐使用这种方式
    * @param obj    代表Cglib 生成的动态代理类 对象本身
    * @param method 代理类中被拦截的接口方法 Method 实例
    * @param args   接口方法参数
    * @param proxy  用于调用父类真正的业务类方法。可以直接调用被代理类接口方法
    * @return
    * @throws Throwable
    */
   @Override
   public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
       System.out.println("before");
       MonitorUtil.start();
       Object result = proxy.invokeSuper(obj, args);        //Object result = method.invoke(target, args);
       System.out.println("after");
       MonitorUtil.finish(method.getName());        return result;
   }
}
```

一个切面，用于在方法拦截器中intercept 方法中调用真正业务方法之前 之后处理逻辑

```javascript
package org.vincent.proxy.cglibproxy;/**
* Created by PengRong on 2018/12/25.
* 方法用时监控类,作为一个切面 ，具有两个方法
*/public class MonitorUtil {    private static ThreadLocal<Long> tl = new ThreadLocal<>();    public static void start() {
       tl.set(System.currentTimeMillis());
   }    /**
    * 结束时打印耗时
    * @param methodName 方法名
    */
   public static void finish(String methodName) {        long finishTime = System.currentTimeMillis();
       System.out.println(methodName + "方法执行耗时" + (finishTime - tl.get()) + "ms");
   }
}
```

Cglib测试类

```javascript
package org.vincent.proxy.cglibproxy;import net.sf.cglib.core.DebuggingClassWriter;import net.sf.cglib.proxy.Enhancer;import org.junit.Test;import java.lang.reflect.Field;import java.util.Properties;/**
* @Package: org.vincent.proxy.cglibproxy <br/>
* @Description： TODO <br/>
* @author: lenovo <br/>
* @Company: PLCC <br/>
* @Copyright: Copyright (c) 2019 <br/>
* @Version: 1.0 <br/>
* @Modified By: <br/>
* @Created by lenovo on 2018/12/26-18:05 <br/>
*/public class CglibTest {    @Test
   public void testCglib() throws Exception {       System.out.println(System.getProperty("user.dir"));        /** 开启 保存cglib生成的动态代理类类文件*/
       saveGeneratedCGlibProxyFiles(System.getProperty("user.dir"));        /** 第一种方法: 创建cglib 代理类 start */
       // 创建加强器，用来创建动态代理类
       Enhancer enhancer = new Enhancer();        // 为代理类指定需要代理的类，也即是父类
       enhancer.setSuperclass(Dog.class);        // new 一个新的方法拦截器
       CglibMethodInterceptor cglibMethodInterceptor = new CglibMethodInterceptor();        // 设置方法拦截器回调引用，对于代理类上所有方法的调用，都会调用CallBack，而Callback则需要实现intercept() 方法进行拦截
       enhancer.setCallback(cglibMethodInterceptor);        // 获取动态代理类对象并返回
       Dog dog = (Dog) enhancer.create();        /** 创建cglib 代理类 end */
       System.out.println(dog.call());        // 对于上面这几步，可以新增一个工具方法 放置在 CglibMethodInterceptor 里面；也就有了第二种方法
       // new 一个新的方法拦截器，该拦截器还顺带一个用于创建代理类的工具方法。看起来简单很多
       cglibMethodInterceptor = new CglibMethodInterceptor();
       dog = (Dog) cglibMethodInterceptor.CglibProxyGeneratory(Dog.class);
       System.out.println(dog.call());   }    /**
    * 设置保存Cglib代理生成的类文件。
    *
    * @throws Exception
    */
   public void saveGeneratedCGlibProxyFiles(String dir) throws Exception {
       Field field = System.class.getDeclaredField("props");
       field.setAccessible(true);
       Properties props = (Properties) field.get(null);
       System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, dir);//dir为保存文件路径
       props.put("net.sf.cglib.core.DebuggingClassWriter.traceEnabled", "true");
   }
}
```

#### Cglib 总结

- CGlib可以传入接口也可以传入普通的类，接口使用实现的方式,普通类使用会使用继承的方式生成代理类.
- 由于是继承方式,如果是 static方法,private方法,final方法等描述的方法是不能被代理的
- 做了方法访问优化，使用建立方法索引的方式避免了传统JDK动态代理需要通过Method方法反射调用.
- 提供callback 和filter设计，可以灵活地给不同的方法绑定不同的callback。编码更方便灵活。
- CGLIB会默认代理Object中equals,toString,hashCode,clone等方法。比JDK代理多了clone。

#### 区别

静态代理是通过在代码中显式编码定义一个业务实现类的代理类，在代理类中对同名的业务方法进行包装，用户通过代理类调用被包装过的业务方法；

JDK动态代理是通过接口中的方法名，在动态生成的代理类中调用业务实现类的同名方法；

CGlib动态代理是通过继承业务类，生成的动态代理类是业务类的子类，通过重写业务方法进行代理；

静态代理在编译时产生class字节码文件，可以直接使用，效率高。动态代理必须实现InvocationHandler接口，通过invoke调用被委托类接口方法是通过反射方式，比较消耗系统性能，但可以减少代理类的数量，使用更灵活。 cglib代理无需实现接口，通过生成类字节码实现代理，比反射稍快，不存在性能问题，但cglib会继承目标对象，需要重写方法，所以目标对象不能为final类。

#### CGLIB生成AopProxy代理对象

在这个生成代理对象的过程中，需要注意的是对Enhancer对象callback回调的设置，正是这些回调封装了Spring AOP的实现，就像前面介绍的JDK的Proxy对象的invoke回调方法一样。在Enhancer的callback回调设置中，实际上是通过设置DynamicAdvisedInterceptor拦截器来完成AOP功能的。

通过使用AopProxy对象封装target目标对象之后，ProxyFactoryBean的getObject方法得到的对象就不是一个普通的Java对象了，而是一个AopProxy代理对象。在ProxyFactoryBean中配置的target目标对象，这时已经不会让应用直接调用其方法实现，而是作为AOP实现的一部分。对target目标对象的方法调用会首先被AopProxy代理对象拦截，对于不同的AopProxy代理对象生成方式，会使用不同的拦截回调入口。例如，对于JDK的AopProxy代理对象，使用的是InvocationHandler的invoke回调入口；而对于CGLIB的AopProxy代理对象，使用的是设置好的callback回调，这是由对CGLIB的使用来决定的。在这些callback回调中，对于AOP实现，是通过DynamicAdvisedInterceptor来完成的，而DynamicAdvisedInterceptor的回调入口是intercept方法。通过这一系列的准备，已经为实现AOP的横切机制奠定了基础，在这个基础上，AOP的Advisor已经可以通过AopProxy代理对象的拦截机制，对需要它进行增强的target目标对象发挥切面的强大威力了。

#### Spring AOP拦截器设计原理

在Spring AOP通过JDK的Proxy方式或CGLIB方式生成代理对象的时候，相关的拦截器已经配置到代理对象中去了，拦截器在代理对象中起作用是通过对这些方法的回调来完成的。如果使用JDK的Proxy来生成代理对象，那么需要通过InvocationHandler来设置拦截器回调；而如果使用CGLIB来生成代理对象，就需要根据CGLIB的使用要求，通过Dynamic-AdvisedInterceptor来完成回调。

#### JdkDynamicAopProxy的invoke拦截

在JdkDynamicAopProxy中实现了InvocationHandler接口，也就是说当Proxy对象的代理方法被调用时，JdkDynamicAopProxy的invoke方法作为Proxy对象的回调函数被触发，从而通过invoke的具体实现，来完成对目标对象方法调用的拦截或者说功能增强的工作。

对Proxy对象的代理设置是在invoke方法中完成的，这些设置包括获取目标对象、拦截器链，同时把这些对象作为输入，创建了ReflectiveMethodInvocation对象，通过这个ReflectiveMethodInvocation对象来完成对AOP功能实现的封装。在这个invoke方法中，包含了一个完整的拦截器链对目标对象的拦截过程，比如获得拦截器链并对拦截器链中的拦截器进行配置，逐个运行拦截器链里的拦截增强，直到最后对目标对象方法的运行等。

#### Cglib2AopProxy的intercept拦截

Cglib2AopProxy的intercept回调方法的实现和JdkDynamic-AopProxy的回调实现是非常类似的，只是在Cglib2AopProxy中构造CglibMethodInvocation对象来完成拦截器链的调用，而在JdkDynamicAopProxy中是通过构造ReflectiveMethod-Invocation对象来完成这个功能的。

#### 目标对象方法的调用

如果没有设置拦截器，那么会对目标对象的方法直接进行调用。对于JdkDynamic-AopProxy代理对象，这个对目标对象的方法调用是通过AopUtils使用反射机制在AopUtils.invokeJoinpointUsingReflection的方法中实现的，在这个调用中，首先得到调用方法的反射对象，然后使用invoke启动对方法反射对象的调用。

对于使用Cglib2AopProxy的代理对象，它对目标对象的调用是通过CGLIB的MethodProxy对象来直接完成的，这个对象的使用是由CGLIB的设计决定的。具体的调用在DynamicAdvisedInterceptor的intercept方法中可以看到，使用的是CGLIB封装好的功能，相对JdkDynamicAopProxy的实现来说，形式上看起来较为简单，但它们的功能却是一样的，都是完成对目标对象方法的调用。

#### AOP拦截器链的调用

前面介绍了使用JDK和CGLIB会生成不同的AopProxy代理对象，从而构造了不同的回调方法来启动对拦截器链的调用，比如在JdkDynamicAopProxy中的invoke方法，以及在Cglib2AopProxy中使用DynamicAdvisedInterceptor的intercept方法。虽然它们使用了不同的AopProxy代理对象，但最终对AOP拦截的处理可谓殊途同归：它们对拦截器链的调用都是在ReflectiveMethodInvocation中通过proceed方法实现的。在proceed方法中，会逐个运行拦截器的拦截方法。在运行拦截器的拦截方法之前，需要对代理方法完成一个匹配判断，通过这个匹配判断来决定拦截器是否满足切面增强的要求。大家一定还记得前面提到的，在Pointcut切点中需要进行matches的匹配过程，即matches调用对方法进行匹配判断，来决定是否需要实行通知增强。以下看到的调用就是进行matches的地方，具体的处理过程在ReflectiveMethodInvocation的proceed方法中。在proceed方法中，先进行判断，如果现在已经运行到拦截器链的末尾，那么就会直接调用目标对象的实现方法；否则，沿着拦截器链继续进行，得到下一个拦截器，通过这个拦截器进行matches判断，判断是否是适用于横切增强的场合，如果是，从拦截器中得到通知器，并启动通知器的invoke方法进行切面增强。在这个过程结束以后，会迭代调用proceed方法，直到拦截器链中的拦截器都完成以上的拦截过程为止。

####  Advice通知的实现

在DefaultAdvisorAdapterRegistry中，设置了一系列的adapter适配器，正是这些adapter适配器的实现，为Spring AOP的advice提供编织能力。

具体说来，对它们的使用主要体现在以下两个方面：一是调用adapter的support方法，通过这个方法来判断取得的advice属于什么类型的advice通知，从而根据不同的advice类型来注册不同的AdviceInterceptor，也就是前面看到的那些拦截器；另一方面，这些AdviceInterceptor都是Spring AOP框架设计好了的，是为实现不同的advice功能提供服务的。有了这些AdviceInterceptor，可以方便地使用由Spring提供的各种不同的advice来设计AOP应用。也就是说，正是这些AdviceInterceptor最终实现了advice通知在AopProxy代理对象中的织入功能。

### Spring事务

#### Spring事务的配置方式

Spring支持编程式事务管理以及声明式事务管理两种方式。

1. 编程式事务管理

编程式事务管理是侵入性事务管理，使用TransactionTemplate或者直接使用PlatformTransactionManager，对于编程式事务管理，Spring推荐使用TransactionTemplate。

2. 声明式事务管理

声明式事务管理建立在AOP之上，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，执行完目标方法之后根据执行的情况提交或者回滚。
编程式事务每次实现都要单独实现，但业务量大功能复杂时，使用编程式事务无疑是痛苦的，而声明式事务不同，声明式事务属于无侵入式，不会影响业务逻辑的实现，只需要在配置文件中做相关的事务规则声明或者通过注解的方式，便可以将事务规则应用到业务逻辑中。
显然声明式事务管理要优于编程式事务管理，这正是Spring倡导的非侵入式的编程方式。唯一不足的地方就是声明式事务管理的粒度是方法级别，而编程式事务管理是可以到代码块的，但是可以通过提取方法的方式完成声明式事务管理的配置。

#### Spring事务传播行为

事务的传播性一般用在事务嵌套的场景，比如一个事务方法里面调用了另外一个事务方法，那么两个方法是各自作为独立的方法提交还是内层的事务合并到外层的事务一起提交，这就是需要事务传播机制的配置来确定怎么样执行。
常用的事务传播机制如下：

- PROPAGATION_REQUIRED
  Spring默认的传播机制，能满足绝大部分业务需求，如果外层有事务，则当前事务加入到外层事务，一块提交，一块回滚。如果外层没有事务，新建一个事务执行
- PROPAGATION_REQUES_NEW
  该事务传播机制是每次都会新开启一个事务，同时把外层事务挂起，当当前事务执行完毕，恢复上层事务的执行。如果外层没有事务，执行当前新开启的事务即可
- PROPAGATION_SUPPORT
  如果外层有事务，则加入外层事务，如果外层没有事务，则直接使用非事务方式执行。完全依赖外层的事务
- PROPAGATION_NOT_SUPPORT
  该传播机制不支持事务，如果外层存在事务则挂起，执行完当前代码，则恢复外层事务，无论是否异常都不会回滚当前的代码
- PROPAGATION_NEVER
  该传播机制不支持外层事务，即如果外层有事务就抛出异常
- PROPAGATION_MANDATORY
  与NEVER相反，如果外层没有事务，则抛出异常
- PROPAGATION_NESTED
  该传播机制的特点是可以保存状态保存点，当前事务回滚到某一个点，从而避免所有的嵌套事务都回滚，即各自回滚各自的，如果子事务没有把异常吃掉，基本还是会引起全部回滚的。

#### 声明式事务管理

1 基于XML方式的声明式事务

![image-20200226143654350](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226143654350.png)

2 基于Annotation方式的声明式事务

![image-20200226143734289](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226143734289.png)



### Spring MVC

#### MVC模式

![image-20200322031309253](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322031309253.png)

#### SpringMVC的执行流程

![image-20200221155040854](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200221155040854.png)

![img](https://img-blog.csdnimg.cn/20190611150417678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3c1ODQyMTIxNzk=,size_16,color_FFFFFF,t_70)

DispatcherServlet：前端控制器

​      用户请求到达前端控制器，它就相当于mvc模式中的c，dispatcherServlet是整个流程控制的中心，

​      由它调用其它组件处理用户的请求，dispatcherServlet的存在降低了组件之间的耦合性。

HandlerMapping：处理器映射器

　　　HandlerMapping负责根据用户请求url找到Handler即处理器，springmvc提供了不同的映射器实现不同的映射方式，

　　  例如：配置文件方式，实现接口方式，注解方式等。

Handler：处理器

　　 Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。

​    由于Handler涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发Handler。

HandlAdapter：处理器适配器

　　通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

ViewResolver：视图解析器

　　View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，

　　再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。

View：视图

　　springmvc框架提供了很多的View视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是jsp。

　　一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面。**

#### DispacherServlet类的继承关系

![image-20200322031439273](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322031439273.png)



![image-20200322031618107](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322031618107.png)

#### DispatcherServlet的启动和初始化

在初始化开始时，需要读取配置在ServletContext中的Bean属性参数，这些属性参数设置在web.xml的Web容器初始化参数中。使用编程式的方式来设置这些Bean属性，在这里可以看到对PropertyValues和BeanWrapper的使用。对于这些和依赖注入相关的类的使用，在分析IoC容器的初始化时，尤其是在依赖注入实现分析时，有过“亲密接触”。只是这里的依赖注入是与Web容器初始化相关的，初始化过程由HttpServletBean来完成。

接着会执行DispatcherServlet持有的IoC容器的初始化过程，在这个初始化过程中，一个新的上下文被建立起来，这个DispatcherServlet持有的上下文被设置为根上下文的子上下文。可以认为，根上下文是和Web应用相对应的一个上下文，而DispatcherServlet持有的上下文是和Servlet对应的一个上下文。在一个Web应用中，往往可以容纳多个Servlet存在；与此相对应，对于应用在Web容器中的上下体系，一个根上下文可以作为许多Servlet上下文的双亲上下文。

#### MVC处理HTTP分发请求

在初始化完成时，在上下文环境中已定义的所有HandlerMapping都已经被加载了，这些加载的handlerMappings被放在一个List中并被排序，存储着HTTP请求对应的映射数据。这个List中的每一个元素都对应着一个具体handlerMapping的配置，一般每一个handlerMapping可以持有一系列从URL请求到Controller的映射，而Spring MVC提供了一系列的HandlerMapping实现。

取得handler的具体过程在getHandlerInternal方法中实现，这个方法接受HTTP请求作为参数，它的实现在AbstractHandlerMapping的子类AbstractUrlHandlerMapping中，这个实现过程包括从HTTP请求中得到URL，并根据URL到urlMapping中获得handler。

在MVC框架初始化完成以后，对HTTP请求的处理是在doService()方法中完成的。DispatcherServlet是HttpServlet的子类，与其他HttpServlet一样，可以通过doService()来响应HTTP的请求。然而，依照Spring MVC的使用，业务逻辑的调用入口是在handler的handler函数中实现的，这里是连接Spring MVC和应用业务逻辑实现的地方。

#### Spring MVC视图的呈现

为了完成视图的呈现工作，需要从ModelAndView对象中取得视图对象，然后调用视图对象的render方法，由这个视图对象来完成特定的视图呈现工作。同时，由于是在Web的环境中，因此视图对象的呈现往往需要完成与HTTP请求和响应相关的处理，这些对象会作为参数传到视图对象的render方法中，供render方法使用。

在ModelAndView中寻找视图对象的逻辑名，如果已经在ModelAndView中设置了视图对象的名称，就对这个名称进行解析，从而得到实际需要使用的视图对象。还有一种可能是ModelAndView中已经有了最终完成视图呈现的视图对象，如果这样，那么这个视图对象是可以直接使用的。不管如何，得到视图对象以后，都是通过调用这个视图对象的render方法来完成数据的显示过程的。不同的视图类型，往往对应着不同视图对象的实现。

#### Spring MVC的实现

Spring MVC的实现大致由以下几个步骤完成：

1）需要建立Controller控制器和HTTP请求之间的映射关系，即在Spring MVC实现中是如何根据请求得到对应的Controller的？通过分析可以看到，在Spring MVC中，这个工作是由在handlerMapping中封装的HandlerExecutionChain对象来完成的，而对Controller控制器和HTTP请求的映射关系的配置是在Bean定义中描述，并在IoC容器初始化时，通过初始化HandlerMapping来完成的，这些定义的映射关系会被载入到一个handlerMap中使用。

2）在初始化过程中，Controller对象和HTTP请求之间的映射关系建立好以后，为Spring MVC接收HTTP请求并完成响应处理做好了准备。在MVC框架接收到HTTP请求的时候，DispatcherServlet会根据具体的URL请求信息，在HandlerMapping中进行查询，从而得到对应的HandlerExecutionChain。在这个HandlerExecutionChain中封装了配置的Controller，这个请求对应的Controller会完成请求的响应动作，生成需要的ModelAndView对象，这个对象就像它的名字所表示的一样，可以从该对象中获得Model模型数据和视图对象。

3）得到ModelAndView以后，DispatcherServlet把获得的模型数据交给特定的视图对象，从而完成这些数据的视图呈现工作。这个视图呈现由视图对象的render方法来完成。毫无疑问，对应于不同的视图对象，render方法会完成不同的视图呈现处理，为用户提供丰富的Web UI表现。

### 缓存数据 

#### 启用对缓存的支持

Spring对缓存的支持有两种方式：
注解驱动的缓存 XML声明的缓存 使用Spring的缓存抽象时，最为通用的方式就是在方法上添加@Cacheable和@CacheEvict注解。

如果以XML的方式配置应用的话，那么可以使用Spring cache命名空间中的<cache:annotation-driven>元素来启用注解驱动的缓存。

#### 配置缓存管理器

Spring 3.1内置了五个缓存管理器实现，如下所示： 

SimpleCacheManager

 NoOpCacheManager 

ConcurrentMapCacheManager 

CompositeCacheManager 

EhCacheCacheManager 

Spring 3.2引入了另外一个缓存管理器，这个管理器可以用在基于JCache（JSR-107）的缓存提供商之中。除了核心的Spring框架，Spring Data又提供了两个缓存管理器： RedisCacheManager（来自于Spring Data Redis项目） GemfireCacheManager（来自于Spring Data GemFire项目） 

#### 添加注解以支持缓存

![image-20200322033205559](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322033205559.png)

#### 填充缓存

@Cacheable和@CachePut注解都可以填充缓存，但是它们的工作方式略有差异。 @Cacheable首先在缓存中查找条目，如果找到了匹配的条目，那么就不会对方法进行调用了。如果没有找到匹配的条目，方法会被调用并且 返回值要放到缓存之中。而@CachePut并不会在缓存中检查匹配的值，目标方法总是会被调用，并将返回值添加到缓存之中。 

@Cacheable和@CachePut有一些属性是共有的。

![image-20200322033249983](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322033249983.png)

#### 自定义缓存key 

@Cacheable和@CachePut都有一个名为key属性，这个属性能够替换默认的key，它是通过一个SpEL表达式计算得到的。任意的SpEL表达 式都是可行的，但是更常见的场景是所定义的表达式与存储在缓存中的值有关，据此计算得到key。 

![image-20200322033329983](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322033329983.png)

#### 使用XML声明缓存

![image-20200322033431517](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322033431517.png)

cache命名空间定义了在Spring XML配置文件中声明缓存的配置元素。表13.5列出了cache命名空间所提供的所有元素。

![image-20200322033449607](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200322033449607.png)

### Spring MVC组件概览

#### HandlerMapping

#### HandlerAdapter

#### HandlerExceptionResolver

#### ViewResolver

#### RequestToViewNameTranslator

#### LocaleResolver

#### ThemeResolver

#### MultipartResolver

#### FlashMapManager

### Spring 4.0新特性

当编写本书时，Spring 4.0是最新的发布版本。在Spring 4.0中包含了很多令人兴奋的新特性，包括： 

Spring提供了对WebSocket编程的支持，包括支持JSR-356——Java API for WebSocket； 

鉴于WebSocket仅仅提供了一种低层次的API，急需高层次的抽象，因此Spring 4.0在WebSocket之上提供了一个高层次的面向消息的编 程模型，该模型基于SockJS，并且包含了对STOMP协议的支持； 

新的消息（messaging）模块，很多的类型来源于Spring Integration项目。这个消息模块支持Spring的SockJS/STOMP功能，同时提供了 基于模板的方式发布消息； 

Spring是第一批（如果不说是第一个的话）支持Java 8特性的Java框架，比如它所支持的lambda表达式。别的暂且不说，这首先能够让 使用特定的回调接口（如RowMapper和JdbcTemplate）更加简洁，代码更加易读； 

与Java 8同时得到支持的是JSR-310——Date与Time API，在处理日期和时间时，它为开发者提供了 比java.util.Date或java.util.Calendar更丰富的API； 

为Groovy开发的应用程序提供了更加顺畅的编程体验，尤其是支持非常便利地完全采用Groovy开发Spring应用程序。随这些一起提供的 是来自于Grails的BeanBuilder，借助它能够通过Groovy配置Spring应用； 

添加了条件化创建bean的功能，在这里只有开发人员定义的条件满足时，才会创建所声明的bean； 

Spring 4.0包含了Spring RestTemplate的一个新的异步实现，它会立即返回并且允许在操作完成后执行回调；

 添加了对多项JEE规范的支持，包括JMS 2.0、JTA 1.2、JPA 2.1和Bean Validation 1.1。 