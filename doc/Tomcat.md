# Tomcat

### Tomcat的顶层结构

Tomcat中最顶层的容器叫Server，代表整个服务器，Server中包含至少一个Service，用于具体提供服务。Service主要包含两部分：Connector和Container。Connector用于处理连接相关的事情，并提供Socket与request、response的转换，Container用于封装和管理Servlet，以及具体处理request请求。一个Tomcat中只有一个Server，一个Server可以包含多个Service，一个Service只有一个Container，但可以有多个Connectors（因为一个服务可以有多个连接，如同时提供http和https连接，也可以提供相同协议不同端口的连接），结构图如图7-1所示。

![image-20200330024244976](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330024244976.png)

### Tomcat的生命周期管理

#### Lifecycle接口

Tomcat通过org.apache.catalina.Lifecycle接口统一管理生命周期，所有有生命周期的组件都要实现Lifecycle接口。Lifecycle接口一共做了4件事：

□定义了13个String类型常量，用于LifecycleEvent事件的type属性中，作用是区分组件发出的LifecycleEvent事件时的状态（如初始化前、启动前、启动中等）。这种设计方式可以让多种状态都发送同一种类型的事件（LifecycleEvent）然后用其中的一个属性来区分状态而不用定义多种事件，我们要学习和借鉴这种方式。

□定义了三个管理监听器的方法addLifecycleListener、findLifecycleListeners和remove-LifecycleListener，分别用来添加、查找和删除LifecycleListener类型的监听器。

□定义了4个生命周期的方法：init、start、stop和destroy，用于执行生命周期的各个阶段的操作。

□定义了获取当前状态的两个方法getState和getStateName，用来获取当前的状态，getState的返回值LifecycleState是枚举类型，里边列举了生命周期的各个节点，getStateName方法返回String类型的状态的名字，主要用于JMX中。

#### LifecycleBase

Lifecycle的默认实现是org.apache.catalina.util.LifecycleBase，所有实现了生命周期的组件都直接或间接地继承自LifecycleBase，LifecycleBase为Lifecycle里的接口方法提供了默认实现：监听器管理是专门使用了一个LifecycleSupport类来完成的，LifecycleSupport中定义了一个LifecycleListener数组类型的属性来保存所有的监听器，然后并定义了添加、删除、查找和执行监听器的方法；生命周期方法中设置了相应的状态并调用了相应的模板方法，init、start、stop和destroy所对应的模板方法分别是initInternal、startInternal、stopInternal和destroyInternal方法，这四个方法由子类具体实现，所以对于子类来说，执行生命周期处理的方法就是initInternal、startInternal、stopInternal和destroyInternal；组件当前的状态在生命周期的四个方法中已经设置好了，所以这时直接返回去就可以了。

### Container

Container是Tomcat中容器的接口，通常使用的Servlet就封装在其子接口Wrapper中。Container一共有4个子接口Engine、Host、Context、Wrapper和一个默认实现类ContainerBase，每个子接口都是一个容器，这4个子容器都有一个对应的StandardXXX实现类，并且这些实现类都继承ContainerBase类。另外Container还继承Lifecycle接口，而且ContainerBase间接继承LifecycleBase，所以Engine、Host、Context、Wrapper 4个子容器都符合前面讲过的Tomcat生命周期管理模式，结构图如图7-3所示。

![image-20200330025841187](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330025841187.png)

#### Container的4个子容器

Container的子容器Engine、Host、Context、Wrapper是逐层包含的关系，其中Engine是最顶层，每个service最多只能有一个Engine，Engine里面可以有多个Host，每个Host下可以有多个Context，每个Context下可以有多个Wrapper，它们的装配关系如图7-4所示。

![image-20200330025933997](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330025933997.png)

□Engine：引擎，用来管理多个站点，一个Service最多只能有一个Engine。

□Host：代表一个站点，也可以叫虚拟主机，通过配置Host就可以添加站点。

□Context：代表一个应用程序，对应着平时开发的一套程序，或者一个WEB-INF目录以及下面的web.xml文件。

□Wrapper：每个Wrapper封装着一个Servlet。

#### Container的启动

Container的启动是通过init和start方法来完成的，在前面分析过这两个方法会在Tomcat启动时被Service调用。Container也是按照Tomcat的生命周期来管理的，init和start方法也会调用initInternal和startInternal方法来具体处理，不过Container和前面讲的Tomcat整体结构启动的过程稍微有点不一样，主要有三点区别：

□Container的4个子容器有一个共同的父类ContainerBase，这里定义了Container容器的initInternal和startInternal方法通用处理内容，具体容器还可以添加自己的内容；

□除了最顶层容器的init是被Service调用的，子容器的init方法并不是在容器中逐层循环调用的，而是在执行start方法的时候通过状态判断还没有初始化才会调用；

□start方法除了在父容器的startInternal方法中调用，还会在父容器的添加子容器的addChild方法中调用，这主要是因为Context和Wrapper是动态添加的，我们在站点目录下放一个应用的文件夹或者war包就可以添加一个Context，在web.xml文件中配置一个Servlet就可以添加一个Wrapper，所以Context和Wrapper是在容器启动的过程中才动态查找出来添加到相应的父容器中的。