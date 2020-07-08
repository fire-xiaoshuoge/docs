# Istio

## 服务网格

### Spring Cloud

诞生于2015年的Spring Cloud应该是Service Mesh的老前辈了。事实上，时至今日，SpringCloud仍是Service Mesh的标杆。

Spring Cloud最早在功能层面为微服务治理定义了一系列标准特性，例如智能路由、熔断机制、服务注册与发现等，并提供了对应的库和组件来实现这些标准特性。到目前为止，这些库和组件已被广泛采用。

但是，Spring Cloud也有一些缺点，例如：

◎ 既博采众家之长，也导致了一种散乱的局面，即用户需要学习和熟悉各组件的“方言”并分别加以运维，这在客观上提高了应用门槛；

◎ 需要在代码级别对诸多组件进行控制，包括Sidecar在内的组件都依赖Java的实现，这和微服务的多语言协作目标是背道而驰的；

◎ 自身并没有对调度、资源、DevOps等提供相关支持，需要借助其他平台来完成，然而目前的容器编排事实标准是Kubernetes，二者的部分功能存在重合或者冲突，这在一定程度上影响了Spring Cloud的长远发展。

### Linkerd

2016年年初，原Twitter基础设施工程师William Morgan和Oliver Gould在GitHub上发布了Linkerd项目，并组建了Buoyant公司。同年，起源于Buoyant的新名词Service Mesh面世并迅速获得认可。紧接着，Buoyant官网发表博客连载A Service Mesh for Kubernetes，在渐入佳境的Kubernetes世界中打出了“The services must mesh”的口号，成为Service Mesh的第一批布道者。

Linkerd很好地结合了Kubernetes所提供的功能，以此为基础，在每个Kubernetes Node上都部署运行一个Linkerd实例，用代理的方式将加入Mesh的Pod通信转接给Linkerd，这样Linkerd就能在通信链路中完成对通信的控制和监控。

### Istio

2016年，Lyft开始了对现代网络代理软件Envoy的内部研发，并在同年9月将Envoy开源。由C++语言开发而成的Envoy在开源之后，迅速获得了大量关注。它除了具备强大的性能，还提供了众多现代服务网格所需的功能特性，并开放了大量精雕细琢的编程接口，为后面的广泛应用埋下了伏笔。

2017年5月，Google、IBM和Lyft宣布了Istio的诞生。Istio以Envoy为数据平面，通过Sidecar的方式让Envoy同业务容器一起运行，并劫持其通信，接受控制平面的统一管理，在此基础上为服务之间的通信提供丰富的连接、控制、观察、安全等特性。本书后续会对Istio进行较为详细的讲解，这里不再赘述。

Istio一经发布，便立刻获得Red Hat、F5等大牌厂商的响应，虽然立足不稳，但各个合作方都展示了对社区、行业的强大影响力。于是，Istio很快就超越了Linkerd，成为Service Mesh的代表产品。

### 服务网格的基本特性

Buoyant公司的CEO William，曾经给出对服务网格的定义：服务网格是一个独立的基础设施层，用来处理服务之间的通信。现代的云原生应用是由各种复杂技术构建的服务组成的，服务网格负责在这些组成部分之间进行可靠的请求传递。目前典型的服务网格通常提供了一组轻量级的网络代理，这些代理会在应用无感知的情况下，同应用并行部署、运行。

这里将Istio的特性总结如下。

◎ 连接：对网格内部的服务之间的调用所产生的流量进行智能管理，并以此为基础，为微服务的部署、测试和升级等操作提供有力保障。

◎ 安全：为网格内部的服务之间的调用提供认证、加密和鉴权支持，在不侵入代码的情况下，加固现有服务，提高其安全性。

◎ 策略：在控制面定制策略，并在服务中实施。

◎ 观察：对服务之间的调用进行跟踪和测量，获取服务的状态信息。

## Istio

Istio出自名门，由Google、IBM和Lyft在2017年5月合作推出，它的初始设计目标是在Kubernetes的基础上，以非侵入的方式为运行在集群中的微服务提供流量管理、安全加固、服务监控和策略管理等功能。

除了前面提到的服务网格基本功能，Istio还提供了对物理机和Consul的注册服务的支持，并提供了接口，用适配器与外界的第三方IT系统进行对接。

### Istio的核心组件

Istio总体来说分为两部分：控制面和数据面，如下所述。

◎ 数据面被称为“Sidecar”，可将其理解为旧式三轮摩托车的挂斗。Sidecar通过注入的方式和业务容器共存于一个Pod中，会劫持业务应用容器的流量，并接受控制面组件的控制，同时会向控制面输出日志、跟踪及监控数据。

◎ 控制面是Istio的核心，管理Istio的所有功能。

1 Pilot

Pilot是Istio的主要控制点，Istio的流量就是由Pilot管理的。

![image-20200405221004604](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405221004604.png)

在整个系统中，Pilot完成以下任务：

◎ 从Kubernetes或者其他平台的注册中心获取服务信息，完成服务发现过程；

◎ 读取Istio的各项控制配置，在进行转换之后，将其发给数据面进行实施。

Pilot的配置内容会被转换为数据面能够理解的格式，下发给数据面的Sidecar, Sidecar根据Pilot指令，将路由、服务、监听、集群等定义信息转换为本地配置，完成控制行为的落地。如图3-2所示为Pilot的工作示意图。

![image-20200405221045190](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405221045190.png)

了Pilot的工作流程：

（1）用户通过kubectl或istioctl（当然也可以通过API）在Kubernetes上创建CRD资源，对Istio控制平面发出指令；

（2）Pilot监听CRD中的config、rbac、networking及authentication资源，在检测到资源对象的变更之后，针对其中涉及的服务，发出指令给对应服务的Sidecar；

（3）Sidecar根据这些指令更新自身配置，根据配置修正通信行为。

2 Mixer

Mixer的职责主要有两个：预检和汇报。如图3-3所示是Mixer的一个简单工作示意图。

![image-20200405221118727](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405221118727.png)

Mixer的简单工作流程如下。

（1）用户将Mixer配置发送到Kubernetes中。

（2）Mixer通过对Kubernetes资源的监听，获知配置的变化。

（3）网格中的服务在每次调用之前，都向Mixer发出预检请求，查看调用是否允许执行。在每次调用之后，都发出报告信息，向Mixer汇报在调用过程中产生的监控跟踪数据。

3 Citadel

Citadel在Istio的早期版本中被称为Istio-CA，不难看出，它是用于证书管理的。在集群中启用了服务之间的加密之后，Citadel负责为集群中的各个服务在统一CA的条件下生成证书，并下发给各个服务中的Sidecar，服务之间的TLS就依赖这些证书完成校验过程。

4 Sidecar（Envoy）

Sidecar就是Istio中的数据面，负责控制面对网格控制的实际执行。

Istio中的默认Sidecar是由Envoy派生出来的，在理论上，只要支持Envoy的xDS协议，其他类似的反向代理软件就都可以代替Envoy来担当这一角色。

在Istio的默认实现中，Istio利用istio-init初始化容器中的iptables指令，对所在Pod的流量进行劫持，从而接管Pod中应用的通信过程，如此一来，就获得了通信的控制权，控制面的控制目的最终得以实现。

### 核心配置对象

**1 networking.istio.io**

networking.istio.io系列对象在Istio中可能是使用频率最高的，Istio的流量管理功能就是用这一组对象完成的，这里选择其中最常用的对象进行简单介绍。

在networking.istio.io的下属资源中，VirtualService是一个控制中心。它的功能简单说来就是：定义一组条件，将符合该条件的流量按照在对象中配置的对应策略进行处理，最后将路由转到匹配的目标中。下面列出几个典型的应用场景。

（1）来自服务A版本1的服务，如果要访问服务B，则要将路由指向服务B的版本2。

（2）在服务X发往服务Y的HTTP请求中，如果Header包含“canary=true”，则把服务目标指向服务Y的版本3，否则发给服务Y的版本2。

（3）为从服务M到服务N的所有访问都加入延迟，以测试在网络状况不佳时的表现。

![image-20200405222914457](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405222914457.png)

1．Gateway

在访问服务时，不论是网格内部的服务互访，还是通过Ingress进入网格的外部流量，首先要经过的设施都是Gateway。Gateway对象描述了边缘接入设备的概念，其中包含对开放端口、主机名及可能存在的TLS证书的定义。网络边缘的Ingress流量，会通过对应的Istio IngressGateway Controller进入；网格内部的服务互访，则通过虚拟的mesh网关进行（mesh网关代表网格内部的所有Sidecar）。Pilot会根据Gateway和主机名进行检索，如果存在对应的VirtualService，则交由VirtualService处理；如果是Mesh Gateway且不存在对应这一主机名的VirtualService，则尝试调用Kubernetes Service；如果不存在，则发生404错误。

2．VirtualService

VirtualService对象主要由以下部分组成。

（1）Host：该对象所负责的主机名称，如果在Kubernetes集群中，则这个主机名可以是服务名。

（2）Gateway：流量的来源网关，在后面会介绍网关的概念。如果这一字段被省略，则代表使用的网关名为“mesh”，也就是默认的网格内部服务互联所用的网关。

（3）路由对象：网格中的流量，如果符合前面的Host和Gateway的条件，就需要根据实际协议对流量的处理方式进行甄别。其原因是：HTTP是一种透明协议，可以经过对报文的解析，完成更细致的控制；而对于原始的TCP流量来说，就无法完成过于复杂的任务了。

3．TCP/TLS/HTTP Route

路由对象目前可以是HTTP、TCP或者TLS中的一个，分别针对不同的协议进行工作。每种路由对象都至少包含两部分：匹配条件和目的路由。例如，在HTTPRoute对象中就包含用于匹配的HTTPMatchRequest对象数组，以及用于描述目标服务的DestinationWeight对象，并且HTTPMatchRequest的匹配条件较为丰富，例如前面提到的http header或者uri等。除此之外，HTTP路由对象受益于HTTP的透明性，包含很多专属的额外特性，例如超时控制、重试、错误注入等。相对来说，TCPRoute简单很多，它的匹配借助资源L4MatchAttributes对象完成，其中除Istio固有的源标签和Gateway外，仅包含地址和端口。

4．DestinationWeigh

各协议路由的目标定义是一致的，都由DestinationWeight对象数组来完成。DestinationWeight指到某个目标（Destination对象）的流量权重，这就意味着，多个目标可以同时为该VirtualService提供服务，并按照权重进行流量分配。

5．Destination

目标对象（Destination）由Subset和Port两个元素组成。Subset顾名思义，就是指服务的一个子集，它在Kubernetes中代表使用标签选择器区分的不同Pod（例如两个Deployment）。Port代表的则是服务的端口。

**2 config.istio.io**

config.istio.io中的对象用于为Mixer组件提供配置。在3.1节中讲到，Mixer提供了预检和报告这两个功能，这两个功能看似简单，但是因为大量适配器的存在，变得相当复杂。图3-6简单展示了Mixer对数据的处理过程。

![image-20200405222845359](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405222845359.png)

1．Rule

Rule对象是Mixer的入口，其中包含一个match成员和一个逻辑表达式，只有符合表达式判断的数据才会被交给Action处理。逻辑表达式中的变量被称为attribute（属性），其中的内容来自Envoy提交的数据。

2．Action

Action负责解决的问题就是：将符合入口标准的数据，在用什么方式加工之后，交给哪个适配器进行处理。Action包含两个成员对象：一个是Instance，使用Template对接收到的数据进行处理；一个是Handler，代表一个适配器的实例，用于接收处理后的数据。

3．Instance

Instance主要用于为进入的数据选择一个模板，并在数据中抽取某些字段作为模板的参数，传输给模板进行处理。

4．Adapter

Adapter在Istio中只被定义为一个行为规范，而一些必要的实例化数据是需要再次进行初始化的，例如RedisQuota适配器中的Redis地址，或者listchecker中的黑白名单等，只有这些数据得到正式的初始化，Adapter才能被投入使用。经过Handler实例化之后的Adapter，就具备了工作功能。有些Adapter是Istio的自身实现，例如前面提到的listchecker或者memquota；有些Adapter是第三方服务，例如Prometheus或者Datadog等。Envoy传出的数据将会通过这些具体运行的Adapter的处理，得到预检结果，或者输出各种监控、日志及跟踪数据。

5．Template

顾名思义，Template是一个模板，用于对接收到的数据进行再加工。

进入Mixer中的数据都来自Sidecar，但是各种适配器应对的需求各有千秋，甚至同样一个适配器，也可能接收各种不同形式的数据（例如Prometheus可能会在同样一批数据中获取不同的指标）, Envoy提供的原始数据和适配器所需要的输入数据存在格式上的差别，因此需要对原始数据进行再加工。Template就是这样一种工具，在用户编制模板对象之后，经过模板处理的原始数据会被转换为符合适配器输入要求的数据格式，这样就可以在Instance字段中引用了。

6．Handler

Handler对象用于对Adapter进行实例化。这组对象的命名非常令人费解，但是从其功能列表中可以看出，Mixer管理了所有第三方资源的接入，大大扩展了Istio的作用范围，其应用难度自然水涨船高，应该说还是可以理解的。

**3 authentication.istio.io**

这一组API用于定义认证策略。它在网格级别、命名空间级别及服务级别都提供了认证策略的要求，要求在内容中包含服务间的通信认证，以及基于JWT的终端认证。这里简单介绍其中涉及的对象。

1．Policy

Policy用于指定服务一级的认证策略，如果将其命名为“default”，那么该对象所在的命名空间会默认采用这一认证策略。Policy对象由两个部分组成：策略目标和认证方法。◎ 策略目标包含服务名称（或主机名称）及服务端口号。◎ 认证方法由两个可选部分组成，分别是用于设置服务间认证的peers子对象，以及用于设置终端认证的origins子对象。

2．MeshPolicy

MeshPolicy只能被命名为“default”，它代表的是所有网格内部应用的默认认证策略，其余部分内容和Policy一致。

**4 rbac.istio.io**

在Istio中实现了一个和Kubernetes颇为相似的RBAC（基于角色的）访问控制系统，其主要对象为ServiceRole和ServiceRoleBinding。

1．ServiceRole

ServiceRole由一系列规则（rules）组成，每条规则都对应一条权限，其中描述了权限所对应的服务、服务路径及方法，还包含一组可以进行自定义的约束。

2．ServiceRoleBinding

和Kubernetes RBAC类似，该对象用于将用户主体（可能是用户或者服务）和ServiceRole进行绑定。

## Istio快速入门

### 环境介绍

围绕Kubernetes环境下的Istio安装和使用进行讲解，这里也仅给出对Kubernetes环境的要求：

◎ Kubernetes 1.9或以上版本；

◎ 具备管理权限的kubectl及其配置文件，能够操作测试集群；

◎ Kubernetes集群要有获取互联网镜像的能力（在第5章中会介绍从私有镜像库中拉取Istio镜像的方法）；

◎ 要支持Istio的自动注入功能，需要检查Kubernetes API Server的启动参数，保证其中的admission control部分按顺序启用MutatingAdmissionWebhook和ValidatingAdmissionWebhook。

### 快速部署Istio

Istio的发布页面位于https://github.com/istio/istio/releases/，其中包含各个客户端平台下Istio的各个版本，例如OS X下Istio 1.0.4版本的安装包名称为istio-1.0.4-osx.tar.gz。在下载安装包后进行解压。

### 用Helm部署Istio

Helm是目前Istio官方推荐的安装方式。除去安装后，我们还可以对输入值进行一些调整，完成对Istio的部分配置工作。Istio Chart是一个总分结构，其分级结构和设计结构是一致的，图5-1展示了Istio Chart的文件结构。

![image-20200405223742169](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405223742169.png)

该文件是Chart的基础信息文件，其中包含版本号、名称、关键字等元数据信息。

#### values-*.yaml

在Istio的发行包中包含一组values文件，提供Istio在各种场景下的关键配置范本，这些范本文件可以作为Helm的输入文件，来对Istio进行典型定制。对Istio的定制可以从对这些输入文件的改写开始，在改写完成后使用helm template命令生成最终的部署文件，这样就能用kubectl完成部署了。下面列举这些典型输入文件的作用。开网格内部的mTLS，禁用自签署证书。

◎ values-istio-auth-galley.yaml：启用控制面mTLS，默认打开网格内部的mTLS，启用Galley。

◎ values-istio-multicluster.yaml：多集群配置。

◎ values-istio-auth-multicluster.yaml：多集群配置，启用控制面mTLS，默认打开。

◎ values-istio-auth.yaml：启用控制面mTLS，默认打开网格内部的mTLS。

◎ values-istio-demo-auth.yaml：启用控制面mTLS，默认打开网格内部的mTLS，激活Grafana、Jaeger、ServiceGraph及Galley，允许自动注入。

◎ values-istio-demo.yaml：激活Grafana、Jaeger、ServiceGraph及Galley，允许自动注入。

◎ values-istio-galley.yaml：启用Galley和Prometheus。

◎ values-istio-gateways.yaml：这是一个样例，可以用这种形式定义新的Gateway。

◎ values-istio-one-namespace.yaml：单命名空间部署，默认打开网格内部的mTLS。

◎ values-istio-one-namespace-auth.yaml：单命名空间部署，启用控制面mTLS，默认打开网格内部的mTLS。

◎ values.yaml：罗列了绝大多数常用变量，也是我们做定制的基础。

#### requirements.yaml

该文件用于管理对子Chart的依赖关系，其中定义了一系列开关变量。

#### templates/_affinity.tpl

该文件会生成一组节点亲和或互斥元素，供各个组件在渲染YAML时使用。在该文件里使用了一系列变量，用于控制Istio组件的节点亲和性（也就是限制Istio在部署时对节点的选择）。

这里定义了如下两个局部模板。

◎ nodeAffinityRequiredDuringScheduling：会根据全局变量中的arch参数对部署节点进行限制。Istio组件的Pod会根据arch参数中的服务器类型列表来决定是否部署到某一台服务器上，并根据各种服务器类型的不同权重来决定优先级。

◎ nodeAffinityPreferredDuringScheduling：跟上一个变量的作用类似，不同的是，这一个软限制。

#### templates/sidecar-injector-configmap.yaml

根据文件名就可以判断出来，该文件最终会用于生成一个ConfigMap对象，在该对象中保存的配置数据被用于进行Sidecar注入。istioctl完成的手工注入，或者Istio的自动注入，都会引用这个ConfigMap，换句话说，如果希望修改Istio的Sidecar的注入过程及具体行为，就可以从该文件或者对应的ConfigMap入手了。

#### templates/configmap.yaml

该文件也会生成一个ConfigMap，名称为istio，这个对象用于为Pilot提供启动配置数据。

#### templates/crds.yaml

该文件包含了Istio所需的CRD定义，它的部署方式较为特殊：

◎ 如果使用Helm 2.10之前的版本进行安装，则需要先使用kubectl提交该文件到Kubernetes集群中；

◎ 如果使用Helm 2.10之后的版本，则其中的Helm hook会自动提前安装，无须特别注意。

#### charts

这个目录中的子目录就是Istio的组件，如下所述。

◎ certmanager：一个基于Jetstack Cert-Manager项目的ACME证书客户端，用于自动进行证书的申请、获取及分发。

◎ galley:Istio利用Galley进行配置管理工作。

◎ gateways：对Gateways Chart进行配置，可以安装多个Gateway Controller，详情参见7.11节。

◎ grafana：图形化的Istio Dashboard。

◎ ingress：一个遗留设计，默认关闭，在流量控制协议升级到network.istio.io/v1alpha3之后，已经建议弃用。

◎ kiali：带有分布式跟踪、配置校验等多项功能的Dashboard。

◎ mixer:Istio的策略实施组件。

◎ pilot:Istio的流量管理组件。

◎ prometheus：监控软件Prometheus，其中包含Istio特定的指标抓取设置。

◎ security:Citadel组件，用于证书的自动管理。

◎ servicegraph：分布式跟踪组件，用于获取和展示服务调用关系图，即将废除。

◎ sidecarInjectorWebhook：自动注入Webhook的相关配置；

◎ tracing：分布式跟踪组件，使用Jaeger实现，替代原有的Service Graph组件。

### 全局变量

我们在使用现有Chart的时候，通常都不会修改Chart的本体，仅通过对变量的控制来实现对部署过程的定制。Istio Helm Chart提供了大量的变量来帮助用户对Istio的安装进行定制。

#### hub和tag

在多数情况下，这两个变量代表所有镜像的地址，具体名称一般以{{ .Values.global.hub}}/[component]/:{{ .Values.global.tag }}的形式拼接而成。在proxy_init、Mixer、Grafana和Pilot的Deployment模板中，一旦在其image变量中包含路径符“/”，则会弃用global.hub，直接采用image的定义。

#### ingress.enabled

这个开关用来控制是否启用Istio的Ingress Controller，如果这个值被设置为True，就会启用对Kubernetes Ingress资源的支持，这是一个兼容的功能，Istio并不推荐Ingress的使用方式，建议使用Ingress Gateway取而代之。

#### Proxy相关的参数

1．proxy．resources

用于为Sidecar分配资源。用户可以根据业务Pod的负载情况，为Sidecar指定CPU和内存资源。

2．proxy．concurrency

Proxy worker的线程数量。如果被设置为0（默认值），则根据CPU线程或核的数量进行分配。

3．proxy．accessLogFile

Sidecar的访问日志位置。如果被设置为空字符串，则关闭访问日志功能。默认值为/dev/stdout。

4．proxy．privileged

istio-init、istio-proxy的特权模式开关。默认值为false。

5．proxy．enableCoreDump

如果打开，则新注入的Sidecar会启动CoreDump功能，在Pod中加入初始化容器enable-core-dump。默认值为false。

6．proxy．includeIPRanges

劫持IP范围的白名单。默认值为“*”，也就是劫持所有地址的流量。在sidecar-injector-configmap.yaml中应用了这一变量，用于生成istio-sidecar-injector这个ConfigMap，这个ConfigMap设置了istio-init的运行参数，proxy.includeIPRanges通过对istio-init的“-i”参数进行修改来完成这一任务。

7．proxy．excludeIPRanges

劫持IP范围的黑名单。默认值为空字符串，也就是仅劫持该范围以外的IP。同proxy.includeIPRanges的情况类似，它影响的是istio-init的“-x”参数。

8．proxy．includeInboundPorts

入站流量的端口劫持白名单。所有从该范围内的端口进入Pod的流量都会被劫持。它影响的是istio-init的“-b”参数。

9．proxy．excludeInboundPorts

入站流量的端口劫持黑名单。这一端口范围之外的入站流量才会被劫持。它影响的是istio-init的“-d”参数。

10．proxy．autoInject

用于控制是否自动完成Sidecar的注入工作。

11．proxy．envoyStatsd

该变量的默认值如下。◎ enabled:true。◎ host:istio-statsd-prom-bridge。◎ port:9125。它会设置Envoy的“--statsdUdpAddress”参数，在某些参数下（例如没有安装Mixer）可以关闭。

#### proxy_init.image

网格中的服务Pod在启动之前，首先会运行一个初始化镜像来完成流量劫持工作，这个变量可以用于指定初始化容器镜像。

#### imagePullPolicy

镜像的拉取策略。默认值为“IfNotPresent”。

####  controlPlaneSecurityEnabled

指定是否在Istio控制面组件上启用mTLS通信。在启用之后，Sidecar和控制平面组件之间，以及控制平面组件之间的通信，都会被改为mTLS方式。受影响的组件包括Ingress、Mixer、Pilot及Sidecar。

#### disablePolicyChecks

如果把这个开关变量设置为true，则会禁用Mixer的预检功能。预检功能是一个同步过程，有可能因为预检缓慢造成业务应用的阻塞。

####  enableTracing

是否启用分布式跟踪功能，默认值为true。

#### mtls.enabled

服务之间是否默认启用mTLS连接，如果这个值被设置为true，那么网格内部所有服务之间的通信都会使用mTLS进行安全加固。需要注意的是，这一变量的设置是全局的，对于每个服务还可以单独使用目标规则或者服务注解的方式，自行决定是否采用mTLS加固。

####  imagePullSecrets

用于为ServiceAccount分配在镜像拉取过程中所需的认证凭据。默认值为空值。

#### arch

在设置Istio组件的节点亲和性过程中，会使用这一变量的列表内容来确定可以用于部署的节点范围，并按照不同的服务器架构设置了优先顺序。

#### oneNamespace

默认值为false, Pilot会监控所有命名空间内的服务变化。如果这个变量被设置为true，则会在Pilot的服务发现参数中加入“-a”，在这种情况下，Pilot只会对Istio组件所在的命名空间进行监控。

#### configValidation

用于配置是否开启服务端的配置验证。默认值为true。该选项在开启之后，会生成一个ValidatingWebhookConfiguration对象，并被包含到Galley的配置中，从而启用校验功能。

#### meshExpansion

要将服务网格扩展到物理机或者虚拟机上，就会使用到这一变量。默认值为false。如果被设置为true，则会在Ingress Gateway上公开Pilot和Citadel的服务。

#### meshExpansionILB

是否在内部网关中公开Pilot和Citadel的端口。默认值为false，仅在服务网格扩展时会使用到这一变量。

#### defaultResources

为所有Istio组件都提供一个最小资源限制。在默认情况下，只设置一个请求10m CPU资源的值。可以在各个Chart的局部变量中分别设置资源需求。

#### hyperkube

在Istio的设置过程会使用一个镜像执行一些Job，例如在早期版本安装过程中的CRD初始化，或者现在的清理过期证书等任务。这个镜像默认使用的是quay.io/coreos:v1.7.6_coreos.0，在内网中同样可以对其进行覆盖。

#### priorityClassName

Kubernetes在1.11.0以上版本中提出了PriorityClass的概念，具有优先级的Pod不会被驱逐或抢占资源。该变量的默认值为空，可选值包括“system-cluster-critical”和“system-node-critical”。

#### crds

该变量用于决定是否包含CRD定义。如果使用helm template命令，或者是2.10以上版本的helm install命令，则应该将其设置为true；否则在安装之前首先要执行kubectl apply -finstall/kubernetes/helm/istio/templates/crds.yaml，并将该变量设置为false。

## Istio常用功能

### 在网格中部署应用

目前支持的工作负载类型包括：Job、DaemonSet、ReplicaSet、Pod及Deployment。Istio对这些工作负载的要求如下。

1．要正确命名服务端口

Service对象中的Port部分必须以“协议名”为前缀，目前支持的协议名包括http、http2、mongo、redis和grpc，例如，我们的flaskapp中的服务端口就被命名为“http”。Istio会根据这些命名来确定为这些端口提供什么样的服务，不符合命名规范的端口会被当作TCP服务，其功能支持范围会大幅缩小。目前的Istio版本对HTTP、HTTP 2及gRPC协议都提供了最大范围的支持。

2．工作负载的Pod必须有关联的Service

为了满足服务发现的需要，所有Pod都必须有关联的服务，因此我们的客户端应用sleep虽然没有开放任何端口，但还是要注册一个Service对象。

另外，官方建议为Pod模板加入两个标签：app和version，分别标注应用名称和版本。这仅仅是个建议，但是Istio的很多默认策略都会引用这两个标签；如果没有这两个标签，就会引发很多不必要的麻烦。

### 修改Istio配置

在学习和应用Istio的过程中，经常有变更Istio现有配置的需求，这时可以使用Helm的“--set”参数来完成这一任务。

### 使用Istio Dashboard

Istio Dashboard是一个包含了Istio定制模板的Grafana（https://grafana.com/）。Grafana是一个通用的Dashboard开源软件，支持Elasticsearch、Zabbix、Prometheus、InfluxDB等多种数据源，并提供了条形图、饼图、表格、折线图等丰富的可视化组件，对其中的数据源和可视化组件都可以进行二次开发，用户可以将数据源和可视化组件结合起来定制自己的Dashboard。

### 使用Prometheus

Prometheus（https://prometheus.io）是CNCF中的一个标志性的监控软件，目前已经是云原生阵营中监控系统的事实标准。在Istio中已经集成了Prometheus，将其用作系统的监控组件。

### 使用Jaeger

Jaeger（https://github.com/jaegertracing/jaeger）是一个用于分布式跟踪的开源软件。

微服务之间的调用关系往往比较复杂，在深度和广度方面都会有较长的调用路线，因此需要有跨服务的跟踪能力。在Istio中提供了Jaeger作为分布式跟踪组件。

Jaeger也是CNCF的成员，是一个分布式跟踪工具，提供了原生的OpenTracing支持，向下兼容ZipKin，同时支持多种存储后端。

### 使用Kiali

Kiali（https://www.kiali.io）也是一个用于Istio可视化的软件，同前面的Grafana、Prometheus等通用软件不同，Kiali目前是专用于Istio系统的，它除了提供了监控、可视化及跟踪等通用功能，还专门提供了Istio的配置验证、健康评估等高级功能。

## Mixer适配器

Istio除了提供了丰富的流量控制功能，还通过Mixer提供了可扩展的外接功能。Mixer“知晓”每一次服务间的调用过程，这些调用过程会为Mixer提供丰富的相关信息，Mixer通过接入的适配器对这些信息进行处理，能够在调用的预检（执行前）和报告（执行后）阶段执行多种任务；并且Mixer的适配器模型是可以扩充的，这也赋予了Mixer更大的扩展能力。

### Mixer适配器简介

Mixer中现有的适配器大致可以分为以下两类。

◎ 一类是Istio内部实现的适配器，用于完成网格的内部功能，例如Fluentd、Stdio、RedisQuota等。

◎ 另一类是第三方服务的适配器，用于和外部系统进行对接，例如DataDog、StackDriver等。

本章同样从应用场景出发，介绍在Istio中使用Mixer能够完成的各种任务，只涉及网格内部功能相关的适配器，如下所述。

◎ Denier：根据自定义条件判断是否拒绝服务。

◎ Fluentd：向Fluentd服务提交日志。

◎ List：用于执行白名单或者黑名单检查。

◎ MemQuota：以内存为存储后端，提供简易的配额控制功能。

◎ Prometheus：为Prometheus提供Istio的监控指标。

◎ RedisQuota：基于Redis存储后端，提供配额管理功能。

◎ StatsD：向StatsD发送监控指标。

◎ Stdio：用于在本地输出日志和指标。

Mixer的配置通常由以下三部分组成。

◎ Handler：声明一个适配器的配置。

◎ Instance：声明一个模板，用模板将传给Mixer的数据转换为适合特定适配器的输出格式。

◎ Rule：将Instance和Handler连接起来，确认处理关系。

## Istio安全加固

Istio为运行于不可信环境内的服务网格提供了无须代码侵入的安全加固能力。在完成微服务改造之后，在流量、监控等基本业务目标之外，安全问题会逐渐凸显出来：原本在单体应用内通过进程内访问控制框架完成的任务，被分散到各个微服务中；在容器集群中还可能出现不同命名空间及不同业务域的互访问题。

###  Istio安全加固概述

安全加固能力是Istio各个组件协作完成的，如下所述：

◎ Citadel提供证书和认证管理功能；

◎ Sidecar建立加密通道，为其代理的应用进行协议升级，为客户端和服务端之间提供基于mTLS的加密通信；

◎ Pilot负责传播加密身份和认证策略。

总体的协作关系大致如图9-1所示。

![image-20200405232129774](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200405232129774.png)

其中：

◎ Citadel会监控Kubernetes API Server，为现存和新建的Service Account签发证书，并将其保存在Secret中，在Pod启动加载时会加载这些Secret；

◎ Pilot会将认证相关的策略发送给Sidecar，并且用目标规则来保障实施过程；

◎ 业务容器和Sidecar之间的明文通信会被升级为Sidecar之间的mTLS通信。本章同样会选择两个典型场景来展示Istio的安全加固能力。