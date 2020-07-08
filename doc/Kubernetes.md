# Kubernetes

## Kubernetes

### Kubernetes

[**Kubernetes**](https://www.kubernetes.org.cn/)是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。

Kubernetes一个核心的特点就是能够自主的管理容器来保证云平台中的容器按照用户的期望状态运行着（比如用户想让apache一直运行，用户不需要关心怎么去做，Kubernetes会自动去监控，然后去重启，新建，总之，让apache一直提供服务），管理员可以加载一个微型服务，让规划器来找到合适的位置，同时，Kubernetes也系统提升工具以及人性化方面，让用户能够方便的部署自己的应用（就像canary deployments）。

### Kubernetes中文社区  

https://www.kubernetes.org.cn/docs

### Kubernetes中文社区 | 中文文档

http://docs.kubernetes.org.cn/

## kubernetes概念

### Kubernetes

kubernetes，简称K8s，是用8代替8个字符“ubernete”而成的缩写。是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。

### Cluster

Cluster是计算、存储和网络资源的集合，Kubernetes利用这些资源运行各种基于容器的应用。

### Master

Kubernetes里的Master指的是集群控制节点，在每个Kubernetes集群里都需要有一个Master来负责整个集群的管理和控制，基本上Kubernetes的所有控制命令都发给它，它负责具体的执行过程，我们后面执行的所有命令基本都是在Master上运行的。Master通常会占据一个独立的服务器（高可用部署建议用3台服务器），主要原因是它太重要了，是整个集群的“首脑”，如果它宕机或者不可用，那么对集群内容器应用的管理都将失效。

在Master上运行着以下关键进程。

◎ Kubernetes API Server（kube-apiserver）：提供了HTTP Rest接口的关键服务进程，是Kubernetes里所有资源的增、删、改、查等操作的唯一入口，也是集群控制的入口进程。

◎ Kubernetes Controller Manager（kube-controller-manager）：Kubernetes里所有资源对象的自动化控制中心，可以将其理解为资源对象的“大总管”。

◎ Kubernetes Scheduler（kube-scheduler）：负责资源调度（Pod调度）的进程，相当于公交公司的“调度室”。

![image-20200325010739848](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325010739848.png)

1．API Server（kube-apiserver）

API Server提供HTTP/HTTPS RESTful API，即Kubernetes API。API Server是KubernetesCluster的前端接口，各种客户端工具（CLI或UI）以及Kubernetes其他组件可以通过它管理Cluster的各种资源。

2．Scheduler（kube-scheduler）

Scheduler负责决定将Pod放在哪个Node上运行。Scheduler在调度时会充分考虑Cluster的拓扑结构，当前各个节点的负载，以及应用对高可用、性能、数据亲和性的需求。

3．Controller Manager（kube-controller-manager）

Controller Manager负责管理Cluster各种资源，保证资源处于预期的状态。ControllerManager由多种controller组成，包括replication controller、endpoints controller、namespace controller、serviceaccounts controller等。不同的controller管理不同的资源。例如，replication controller管理Deployment、StatefulSet、DaemonSet的生命周期，namespace controller管理Namespace资源。

4．etcd

etcd负责保存Kubernetes Cluster的配置信息和各种资源的状态信息。当数据发生变化时，etcd会快速地通知Kubernetes相关组件。

5．Pod网络

Pod要能够相互通信，Kubernetes Cluster必须部署Pod网络，flannel是其中一个可选方案。

### Node

除了Master，Kubernetes集群中的其他机器被称为Node，在较早的版本中也被称为Minion。与Master一样，Node可以是一台物理主机，也可以是一台虚拟机。Node是Kubernetes集群中的工作负载节点，每个Node都会被Master分配一些工作负载（Docker容器），当某个Node宕机时，其上的工作负载会被Master自动转移到其他节点上。

在每个Node上都运行着以下关键进程。

◎ kubelet：负责Pod对应的容器的创建、启停等任务，同时与Master密切协作，实现集群管理的基本功能。

◎ kube-proxy：实现Kubernetes Service的通信与负载均衡机制的重要组件。

◎ Docker Engine（docker）：Docker引擎，负责本机的容器创建和管理工作。

![image-20200325010851435](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325010851435.png)

1．kubelet

kubelet是Node的agent，当Scheduler确定在某个Node上运行Pod后，会将Pod的具体配置信息（image、volume等）发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向Master报告运行状态。

2．kube-proxy

service在逻辑上代表了后端的多个Pod，外界通过service访问Pod。service接收到的请求是如何转发到Pod的呢？这就是kube-proxy要完成的工作。每个Node都会运行kube-proxy服务，它负责将访问service的TCP/UPD数据流转发到后端的容器。如果有多个副本，kube-proxy会实现负载均衡。

3．Pod网络

Pod要能够相互通信，Kubernetes Cluster必须部署Pod网络，flannel是其中一个可选方案。

### Pod

Pod是Kubernetes的最小工作单元。每个Pod包含一个或多个容器。Pod中的容器会作为一个整体被Master调度到一个Node上运行。Kubernetes引入Pod主要基于下面两个目的：

（1）可管理性。有些容器天生就是需要紧密联系，一起工作。Pod提供了比容器更高层次的抽象，将它们封装到一个部署单元中。Kubernetes以Pod为最小单位进行调度、扩展、共享资源、管理生命周期。

（2）通信和资源共享。Pod中的所有容器使用同一个网络namespace，即相同的IP地址和Port空间。它们可以直接用localhost通信。同样的，这些容器可以共享存储，当Kubernetes挂载volume到Pod，本质上是将volume挂载到Pod中的每一个容器。

![image-20200325023325018](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325023325018.png)

Kubernetes为每个Pod都分配了唯一的IP地址，称之为Pod IP，一个Pod里的多个容器共享PodIP地址。Kubernetes要求底层网络支持集群内任意两个Pod之间的TCP/IP直接通信，这通常采用虚拟二层网络技术来实现，例如Flannel、Open vSwitch等，因此我们需要牢记一点：在Kubernetes里，一个Pod里的容器与另外主机上的Pod容器能够直接通信。

Pod其实有两种类型：普通的Pod及静态Pod（Static Pod）。后者比较特殊，它并没被存放在Kubernetes的etcd存储里，而是被存放在某个具体的Node上的一个具体文件中，并且只在此Node上启动、运行。而普通的Pod一旦被创建，就会被放入etcd中存储，随后会被KubernetesMaster调度到某个具体的Node上并进行绑定（Binding），随后该Pod被对应的Node上的kubelet进程实例化成一组相关的Docker容器并启动。在默认情况下，当Pod里的某个容器停止时，Kubernetes会自动检测到这个问题并且重新启动这个Pod（重启Pod里的所有容器），如果Pod所在的Node宕机，就会将这个Node上的所有Pod重新调度到其他节点上。

Pods有两种使用方式：

（1）运行单一容器。one-container-per-Pod是Kubernetes最常见的模型，这种情况下，只是将单个容器简单封装成Pod。即便是只有一个容器，Kubernetes管理的也是Pod而不是直接管理容器。

2）运行多个容器。

为什么Kubernetes会设计出一个全新的Pod的概念并且Pod有这样特殊的组成结构？

原因之一：在一组容器作为一个单元的情况下，我们难以简单地对“整体”进行判断及有效地行动。比如，一个容器死亡了，此时算是整体死亡么？是N/M的死亡率么？引入业务无关并且不易死亡的Pause容器作为Pod的根容器，以它的状态代表整个容器组的状态，就简单、巧妙地解决了这个难题。

原因之二：Pod里的多个业务容器共享Pause容器的IP，共享Pause容器挂接的Volume，这样既简化了密切关联的业务容器之间的通信问题，也很好地解决了它们之间的文件共享问题。

Kubernetes为每个Pod都分配了唯一的IP地址，称之为Pod IP，一个Pod里的多个容器共享PodIP地址。Kubernetes要求底层网络支持集群内任意两个Pod之间的TCP/IP直接通信，这通常采用虚拟二层网络技术来实现，例如Flannel、Open vSwitch等，因此我们需要牢记一点：在Kubernetes里，一个Pod里的容器与另外主机上的Pod容器能够直接通信。



### Controller

Kubernetes通常不会直接创建Pod，而是通过Controller来管理Pod的。Controller中定义了Pod的部署特性，比如有几个副本、在什么样的Node上运行等。为了满足不同的业务场景，Kubernetes提供了多种Controller，包括Deployment、ReplicaSet、DaemonSet、StatefuleSet、Job等。

（1）Deployment是最常用的Controller，比如在线教程中就是通过创建Deployment来部署应用的。Deployment可以管理Pod的多个副本，并确保Pod按照期望的状态运行。

（2）ReplicaSet实现了Pod的多副本管理。使用Deployment时会自动创建ReplicaSet，也就是说Deployment是通过ReplicaSet来管理Pod的多个副本的，我们通常不需要直接使用ReplicaSet。

（3）DaemonSet用于每个Node最多只运行一个Pod副本的场景。正如其名称所揭示的，DaemonSet通常用于运行daemon。

（4）StatefuleSet能够保证Pod的每个副本在整个生命周期中名称是不变的，而其他Controller不提供这个功能。当某个Pod发生故障需要删除并重新启动时，Pod的名称会发生变化，同时StatefuleSet会保证副本按照固定的顺序启动、更新或者删除。

（5）Job用于运行结束就删除的应用，而其他Controller中的Pod通常是长期持续运行。

### Service

Service服务也是Kubernetes里的核心资源对象之一，Kubernetes里的每个Service其实就是我们经常提起的微服务架构中的一个微服务，之前讲解Pod、RC等资源对象其实都是为讲解Kubernetes Service做铺垫的。

![image-20200325023902136](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325023902136.png)

从图1.12中可以看到，Kubernetes的Service定义了一个服务的访问入口地址，前端的应用（Pod）通过这个入口地址访问其背后的一组由Pod副本组成的集群实例，Service与其后端Pod副本集群之间则是通过Label Selector来实现无缝对接的。RC的作用实际上是保证Service的服务能力和服务质量始终符合预期标准。

通过分析、识别并建模系统中的所有服务为微服务——Kubernetes Service，我们的系统最终由多个提供不同业务能力而又彼此独立的微服务单元组成的，服务之间通过TCP/IP进行通信，从而形成了强大而又灵活的弹性网格，拥有强大的分布式能力、弹性扩展能力、容错能力，程序架构也变得简单和直观许多，如图1.13所示。

![image-20200325023917283](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325023917283.png)

### Namespace

Namespace（命名空间）是Kubernetes系统中的另一个非常重要的概念，Namespace在很多情况下用于实现多租户的资源隔离。Namespace通过将集群内部的资源对象“分配”到不同的Namespace中，形成逻辑上分组的不同项目、小组或用户组，便于不同的分组在共享使用整个集群的资源的同时还能被分别管理。

###  Label

Label（标签）是Kubernetes系统中另外一个核心概念。一个Label是一个key=value的键值对，其中key与value由用户自己指定。Label可以被附加到各种资源对象上，例如Node、Pod、Service、RC等，一个资源对象可以定义任意数量的Label，同一个Label也可以被添加到任意数量的资源对象上。Label通常在资源对象定义时确定，也可以在对象创建后动态添加或者删除。

我们可以通过给指定的资源对象捆绑一个或多个不同的Label来实现多维度的资源分组管理功能，以便灵活、方便地进行资源分配、调度、配置、部署等管理工作。例如，部署不同版本的应用到不同的环境中；监控和分析应用（日志记录、监控、告警）等。一些常用的Label示例如下。

◎ 版本标签："release" : "stable"、"release" : "canary"。

◎ 环境标签："environment":"dev"、"environment":"qa"、"environment":"production"。

◎ 架构标签："tier" : "frontend"、"tier" : "backend"、"tier" : "middleware"。

◎ 分区标签："partition" : "customerA"、"partition" : "customerB"。

◎ 质量管控标签："track" : "daily"、"track" : "weekly"。

### Deployment

Deployment是Kubernetes在1.2版本中引入的新概念，用于更好地解决Pod的编排问题。为此，Deployment在内部使用了Replica Set来实现目的，无论从Deployment的作用与目的、YAML定义，还是从它的具体命令行操作来看，我们都可以把它看作RC的一次升级，两者的相似度超过90%。

Deployment的典型使用场景有以下几个。

◎ 创建一个Deployment对象来生成对应的Replica Set并完成Pod副本的创建。

◎ 检查Deployment的状态来看部署动作是否完成（Pod副本数量是否达到预期的值）。

◎ 更新Deployment以创建新的Pod（比如镜像升级）。

◎ 如果当前Deployment不稳定，则回滚到一个早先的Deployment版本。

◎ 暂停Deployment以便于一次性修改多个PodTemplateSpec的配置项，之后再恢复Deployment，进行新的发布。

◎ 扩展Deployment以应对高负载。

◎ 查看Deployment的状态，以此作为发布是否成功的指标。

◎ 清理不再需要的旧版本ReplicaSets。

以nginx-deployment为例，配置文件如图。

![image-20200325011120056](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325011120056.png)

①apiVersion是当前配置格式的版本。

②kind是要创建的资源类型，这里是Deployment。

③metadata是该资源的元数据，name是必需的元数据项。

④spec部分是该Deployment的规格说明。

⑤replicas指明副本数量，默认为1。

⑥template定义Pod的模板，这是配置文件的重要部分。

⑦metadata定义Pod的元数据，至少要定义一个label。label的key和value可以任意指定。

⑧spec描述Pod的规格，此部分定义Pod中每一个容器的属性，name和image是必需的。

### Job

容器按照持续运行的时间可分为两类：服务类容器和工作类容器。

服务类容器通常持续提供服务，需要一直运行，比如HTTP Server、Daemon等。工作类容器则是一次性任务，比如批处理程序，完成后容器就退出。

Kubernetes的Deployment、ReplicaSet和DaemonSet都用于管理服务类容器；对于工作类容器，我们使用Job。

Job配置文件myjob.yml。

![image-20200325011636038](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325011636038.png)

①batch/v1是当前Job的apiVersion。

②指明当前资源的类型为Job。

③restartPolicy指定什么情况下需要重启容器。对于Job，只能设置为Never或者OnFailure。对于其他controller（比如Deployment），可以设置为Always。

### Volume

Volume（存储卷）是Pod中能够被多个容器访问的共享目录。Kubernetes的Volume概念、用途和目的与Docker的Volume比较类似，但两者不能等价。首先，Kubernetes中的Volume被定义在Pod上，然后被一个Pod里的多个容器挂载到具体的文件目录下；其次，Kubernetes中的Volume与Pod的生命周期相同，但与容器的生命周期不相关，当容器终止或者重启时，Volume中的数据也不会丢失。最后，Kubernetes支持多种类型的Volume，例如GlusterFS、Ceph等先进的分布式文件系统。

Kubernetes提供了非常丰富的Volume类型，下面逐一进行说明。

1．emptyDir

一个emptyDir Volume是在Pod分配到Node时创建的。从它的名称就可以看出，它的初始内容为空，并且无须指定宿主机上对应的目录文件，因为这是Kubernetes自动分配的一个目录，当Pod从Node上移除时，emptyDir中的数据也会被永久删除。emptyDir的一些用途如下。

◎ 临时空间，例如用于某些应用程序运行时所需的临时目录，且无须永久保留。

◎ 长时间任务的中间过程CheckPoint的临时保存目录。

◎ 一个容器需要从另一个容器中获取数据的目录（多容器共享目录）。

目前，用户无法控制emptyDir使用的介质种类。如果kubelet的配置是使用硬盘，那么所有emptyDir都将被创建在该硬盘上。Pod在将来可以设置emptyDir是位于硬盘、固态硬盘上还是基于内存的tmpfs上，上面的例子便采用了emptyDir类的Volume。

2．hostPath

hostPath为在Pod上挂载宿主机上的文件或目录，它通常可以用于以下几方面。

◎ 容器应用程序生成的日志文件需要永久保存时，可以使用宿主机的高速文件系统进行存储。

◎ 需要访问宿主机上Docker引擎内部数据结构的容器应用时，可以通过定义hostPath为宿主机/var/lib/docker目录，使容器内部应用可以直接访问Docker的文件系统。

在使用这种类型的Volume时，需要注意以下几点。

◎ 在不同的Node上具有相同配置的Pod，可能会因为宿主机上的目录和文件不同而导致对Volume上目录和文件的访问结果不一致。

◎ 如果使用了资源配额管理，则Kubernetes无法将hostPath在宿主机上使用的资源纳入管理。

3．gcePersistentDisk

使用这种类型的Volume表示使用谷歌公有云提供的永久磁盘（Persistent Disk，PD）存放Volume的数据，它与emptyDir不同，PD上的内容会被永久保存，当Pod被删除时，PD只是被卸载（Unmount），但不会被删除。需要注意的是，你需要先创建一个PD，才能使用gcePersistentDisk。

使用gcePersistentDisk时有以下一些限制条件。

◎ Node（运行kubelet的节点）需要是GCE虚拟机。

◎ 这些虚拟机需要与PD存在于相同的GCE项目和Zone中。

4．awsElasticBlockStore

与GCE类似，该类型的Volume使用亚马逊公有云提供的EBS Volume存储数据，需要先创建一个EBS Volume才能使用awsElasticBlockStore。

使用awsElasticBlockStore的一些限制条件如下。

◎ Node（运行kubelet的节点）需要是AWS EC2实例。

◎ 这些AWS EC2实例需要与EBS Volume存在于相同的region和availability-zone中。

◎ EBS只支持单个EC2实例挂载一个Volume。

6．其他类型的Volume

◎ iscsi：使用iSCSI存储设备上的目录挂载到Pod中。

◎ flocker：使用Flocker管理存储卷。

◎ glusterfs：使用开源GlusterFS网络文件系统的目录挂载到Pod中。

◎ rbd：使用Ceph块设备共享存储（Rados Block Device）挂载到Pod中。

◎ gitRepo：通过挂载一个空目录，并从Git库clone一个git repository以供Pod使用。

◎ secret：一个Secret Volume用于为Pod提供加密的信息，你可以将定义在Kubernetes中的Secret直接挂载为文件让Pod访问。Secret Volume是通过TMFS（内存文件系统）实现的，这种类型的Volume总是不会被持久化的。

###  Persistent Volume

PV可以被理解成Kubernetes集群中的某个网络存储对应的一块存储，它与Volume类似，但有以下区别。

◎ PV只能是网络存储，不属于任何Node，但可以在每个Node上访问。

◎ PV并不是被定义在Pod上的，而是独立于Pod之外定义的。

◎ PV目前支持的类型包括：gcePersistentDisk、AWSElasticBlockStore、AzureFile、AzureDisk、FC（Fibre Channel）、Flocker、NFS、iSCSI、RBD（Rados BlockDevice）、CephFS、Cinder、GlusterFS、VsphereVolume、Quobyte Volumes、VMware Photon、Portworx Volumes、ScaleIO Volumes和HostPath（仅供单机测试）。

### Annotation

Annotation（注解）与Label类似，也使用key/value键值对的形式进行定义。不同的是Label具有严格的命名规则，它定义的是Kubernetes对象的元数据（Metadata），并且用于LabelSelector。Annotation则是用户任意定义的附加信息，以便于外部工具查找。在很多时候，Kubernetes的模块自身会通过Annotation标记资源对象的一些特殊信息。

通常来说，用Annotation来记录的信息如下。

◎ build信息、release信息、Docker镜像信息等，例如时间戳、release id号、PR号、镜像Hash值、Docker Registry地址等。

◎ 日志库、监控库、分析库等资源库的地址信息。

◎ 程序调试工具信息，例如工具名称、版本号等。

◎ 团队的联系信息，例如电话号码、负责人名称、网址等。

### ConfigMap

Kubernetes提供了一种内建机制，将存储在etcd中的ConfigMap通过Volume映射的方式变成目标Pod内的配置文件，不管目标Pod被调度到哪台服务器上，都会完成自动映射。进一步地，如果ConfigMap中的key-value数据被修改，则映射到Pod中的“配置文件”也会随之自动更新。于是，Kubernetes ConfigMap就成了分布式系统中最为简单（使用方法简单，但背后实现比较复杂）且对应用无侵入的配置中心。ConfigMap配置集中化的一种简单方案如图1.16所示。

![image-20200325024820991](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325024820991.png)

使用ConfigMap的限制条件如下。

◎ ConfigMap必须在Pod之前创建。

◎ ConfigMap受Namespace限制，只有处于相同Namespace中的Pod才可以引用它。

◎ ConfigMap中的配额管理还未能实现。

◎ kubelet只支持可以被API Server管理的Pod使用ConfigMap。kubelet在本Node上通过--manifest-url或--config自动创建的静态Pod将无法引用ConfigMap。

◎ 在Pod对ConfigMap进行挂载（volumeMount）操作时，在容器内部只能挂载为“目录”，无法挂载为“文件”。在挂载到容器内部后，在目录下将包含ConfigMap定义的每个item，如果在该目录下原来还有其他文件，则容器内的该目录将被挂载的ConfigMap覆盖。如果应用程序需要保留原来的其他文件，则需要进行额外的处理。可以将ConfigMap挂载到容器内部的临时目录，再通过启动脚本将配置文件复制或者链接到（cp或link命令）应用所用的实际配置目录下。

## Kubernetes安装配置指南

### 系统要求

![image-20200325025058275](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325025058275.png)

### kubeadm工具安装Kubernetes集群

1 安装kubeadm和相关工具

首先配置yum源，官方yum源的地址为https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64。如果无法访问官方yum源的地址，则也可以使用国内的一个yum源，地址为http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/，yum源的配置文件/etc/yum.repos.d/kubernetes.repo的内容如下：

![image-20200325025239362](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325025239362.png)

然后运行yum install命令安装kubeadm和相关工具：

![image-20200325025254302](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325025254302.png)

运行下面的命令，启动Docker服务（如果已安装Docker，则无须再次启动）和kubelet服务，并设置为开机自动启动：

![image-20200325025306425](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325025306425.png)

kubeadm config

。kubeadm config子命令提供了对这一组功能的支持：

◎ kubeadm config upload from-file：由配置文件上传到集群中生成ConfigMap。

◎ kubeadm config upload from-flags：由配置参数生成ConfigMap。

◎ kubeadm config view：查看当前集群中的配置值。

◎ kubeadm config print init-defaults：输出kubeadm init默认参数文件的内容。

◎ kubeadm config print join-defaults：输出kubeadm join默认参数文件的内容。

◎ kubeadm config migrate：在新旧版本之间进行配置转换。

◎ kubeadm config images list：列出所需的镜像列表。

◎ kubeadm config images pull：拉取镜像到本地。

2 下载Kubernetes的相关镜像

3 运行kubeadm init命令安装Master

4 安装Node，加入集群

5 安装网络插件

6 验证Kubernetes集群是否安装完成

###  Kubernetes核心服务配置

![image-20200325025656972](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325025656972.png)

### CRI

归根结底，Kubernetes Node（kubelet）的主要功能就是启动和停止容器的组件，我们称之为容器运行时（Container Runtime），其中最知名的就是Docker了。为了更具扩展性，Kubernetes从1.5版本开始就加入了容器运行时插件API，即Container Runtime Interface，简称CRI。

每个容器运行时都有特点，因此不少用户希望Kubernetes能够支持更多的容器运行时。Kubernetes从1.5版本开始引入了CRI接口规范，通过插件接口模式，Kubernetes无须重新编译就可以使用更多的容器运行时。CRI包含Protocol Buffers、gRPC API、运行库支持及开发中的标准规范和工具。Docker的CRI实现在Kubernetes 1.6中被更新为Beta版本，并在kubelet启动时默认启动。

### kubectl命令行工具

kubectl作为客户端CLI工具，可以让用户通过命令行对Kubernetes集群进行操作。

kubectl命令行的语法如下：

![image-20200325030300578](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325030300578.png)

其中，command、TYPE、NAME、flags的含义如下。

（1）command：子命令，用于操作Kubernetes集群资源对象的命令，例如create、delete、describe、get、apply等。

（2）TYPE：资源对象的类型，区分大小写，能以单数、复数或者简写形式表示。例如以下3种TYPE是等价的。

（3）NAME：资源对象的名称，区分大小写。如果不指定名称，系统则将返回属于TYPE的全部对象的列表，例如$ kubectl get pods将返回所有Pod的列表。

（4）flags：kubectl子命令的可选参数，例如使用“-s”指定API Server的URL地址而不用默认值。

## Pod

### Pod定义详解

```yml
apiVersion: v1 # 【必须】版本号
kind: Pod # 【必选】Pod
metadata: # 【必选-Object】元数据
  name: String # 【必选】 Pod的名称
  namespace: String # 【必选】 Pod所属的命名空间
  labels: # 【List】 自定义标签列表
   - name: String
  annotations: # 【List】 自定义注解列表
   - name: String
spec: # 【必选-Object】 Pod中容器的详细定义
  containers: # 【必选-List】 Pod中容器的详细定义
  - name: String # 【必选】 容器的名称
    image: String # 【必选】 容器的镜像名称
    imagePullPolicy: [Always | Never | IfNotPresent] # 【String】 每次都尝试重新拉取镜像 | 仅使用本地镜像 | 如果本地有镜像则使用，没有则拉取
    command: [String] # 【List】 容器的启动命令列表，如果不指定，则使用镜像打包时使用的启动命令
    args: [String] # 【List】 容器的启动命令参数列表
    workingDir: String # 容器的工作目录
    volumeMounts: # 【List】 挂载到容器内部的存储卷配置
    - name: String # 引用Pod定义的共享存储卷的名称，需使用volumes[]部分定义的共享存储卷名称
      mountPath: Sting # 存储卷在容器内mount的绝对路径，应少于512个字符
      readOnly: Boolean # 是否为只读模式，默认为读写模式
    ports: # 【List】 容器需要暴露的端口号列表
    - name: String  # 端口的名称
      containerPort: Int # 容器需要监听的端口号
      hostPort: Int # 容器所在主机需要监听的端口号，默认与containerPort相同。设置hostPort时，同一台宿主机将无法启动该容器的第二份副本
      protocol: String # 端口协议，支持TCP和UDP，默认值为TCP
    env: # 【List】 容器运行前需设置的环境变量列表
    - name: String # 环境变量的名称
      value: String # 环境变量的值
    resources: # 【Object】 资源限制和资源请求的设置
      limits: # 【Object】 资源限制的设置
        cpu: String # CPU限制，单位为core数，将用于docker run --cpu-shares参数
        memory: String # 内存限制，单位可以为MB，GB等，将用于docker run --memory参数
      requests: # 【Object】 资源限制的设置
        cpu: String # cpu请求，单位为core数，容器启动的初始可用数量
        memory: String # 内存请求，单位可以为MB，GB等，容器启动的初始可用数量
    livenessProbe: # 【Object】 对Pod内各容器健康检查的设置，当探测无响应几次之后，系统将自动重启该容器。可以设置的方法包括：exec、httpGet和tcpSocket。对一个容器只需要设置一种健康检查的方法
      exec: # 【Object】 对Pod内各容器健康检查的设置，exec方式
        command: [String] # exec方式需要指定的命令或者脚本
      httpGet: # 【Object】 对Pod内各容器健康检查的设置，HTTGet方式。需要指定path、port
        path: String
        port: Number
        host: String
        scheme: String
        httpHeaders:
        - name: String
          value: String
      tcpSocket: # 【Object】 对Pod内各容器健康检查的设置，tcpSocket方式
        port: Number
      initialDelaySeconds: Number # 容器启动完成后首次探测的时间，单位为s
      timeoutSeconds: Number  # 对容器健康检查的探测等待响应的超时时间设置，单位为s，默认值为1s。若超过该超时时间设置，则将认为该容器不健康，会重启该容器。
      periodSeconds: Number # 对容器健康检查的定期探测时间设置，单位为s，默认10s探测一次
      successThreshold: 0
      failureThreshold: 0
    securityContext:
      privileged: Boolean
  restartPolicy: [Always | Never | OnFailure] # Pod的重启策略 一旦终止运行，都将重启 | 终止后kubelet将报告给master，不会重启 | 只有Pod以非零退出码终止时，kubelet才会重启该容器。如果容器正常终止（退出码为0），则不会重启。
  nodeSelector: object # 设置Node的Label，以key:value格式指定，Pod将被调度到具有这些Label的Node上
  imagePullSecrets: # 【Object】 pull镜像时使用的Secret名称，以name:secretkey格式指定
  - name: String
  hostNetwork: Boolean # 是否使用主机网络模式，默认值为false。设置为true表示容器使用宿主机网络，不再使用docker网桥，该Pod将无法在同一台宿主机上启动第二个副本
  volumes: # 【List】 在该Pod上定义的共享存储卷列表
  - name: String # 共享存储卷的名称，volume的类型有很多emptyDir，hostPath，secret，nfs，glusterfs，cephfs，configMap
    emptyDir: {} # 【Object】 类型为emptyDir的存储卷，表示与Pod同生命周期的一个临时目录，其值为一个空对象：emptyDir: {}
    hostPath: # 【Object】 类型为hostPath的存储卷，表示挂载Pod所在宿主机的目录
      path: String # Pod所在主机的目录，将被用于容器中mount的目录
    secret: # 【Object】类型为secret的存储卷，表示挂载集群预定义的secret对象到容器内部
      secretName: String
      items:
      - key: String
        path: String
    configMap: # 【Object】 类型为configMap的存储卷，表示挂载集群预定义的configMap对象到容器内部
      name: String
      items:
      - key: String
        path: String
```

### 静态Pod

静态Pod是由kubelet进行管理的仅存在于特定Node上的Pod。它们不能通过API Server进行管理，无法与ReplicationController、Deployment或者DaemonSet进行关联，并且kubelet无法对它们进行健康检查。静态Pod总是由kubelet创建的，并且总在kubelet所在的Node上运行。创建静态Pod有两种方式：配置文件方式和HTTP方式。

### 在容器内获取Pod信息

每个Pod在被成功创建出来之后，都会被系统分配唯一的名字、IP地址，并且处于某个Namespace中，那么我们如何在Pod的容器内获取Pod的这些重要信息呢？答案就是使用Downward API。Downward API可以通过以下两种方式将Pod信息注入容器内部。

（1）环境变量：用于单个变量，可以将Pod信息和Container信息注入容器内部。

（2）Volume挂载：将数组类信息生成为文件并挂载到容器内部。

### Pod生命周期和重启策略

![image-20200325030942547](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325030942547.png)

Pod的重启策略（RestartPolicy）应用于Pod内的所有容器，并且仅在Pod所处的Node上由kubelet进行判断和重启操作。当某个容器异常退出或者健康检查（详见下节）失败时，kubelet将根据RestartPolicy的设置来进行相应的操作。

Pod的重启策略包括Always、OnFailure和Never，默认值为Always。

◎ Always：当容器失效时，由kubelet自动重启该容器。

◎ OnFailure：当容器终止运行且退出码不为0时，由kubelet自动重启该容器。

◎ Never：不论容器运行状态如何，kubelet都不会重启该容器。

Pod的重启策略与控制方式息息相关，当前可用于管理Pod的控制器包括ReplicationController、Job、DaemonSet及直接通过kubelet管理（静态Pod）。每种控制器对Pod的重启策略要求如下。

◎ RC和DaemonSet：必须设置为Always，需要保证该容器持续运行。

◎ Job：OnFailure或Never，确保容器执行完成后不再重启。

◎ kubelet：在Pod失效时自动重启它，不论将RestartPolicy设置为什么值，也不会对Pod进行健康检查。

结合Pod的状态和重启策略，表3.3列出一些常见的状态转换场景。

![image-20200325031045038](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325031045038.png)

### Pod可用性检查

Kubernetes对Pod的健康状态可以通过两类探针来检查： LivenessProbe和ReadinessProbe，kubelet定期执行这两类探针来诊断容器的健康状况。

（1）LivenessProbe探针：用于判断容器是否存活（Running状态），如果LivenessProbe探针探测到容器不健康，则kubelet将杀掉该容器，并根据容器的重启策略做相应的处理。如果一个容器不包含LivenessProbe探针，那么kubelet认为该容器的LivenessProbe探针返回的值永远是Success。

（2）ReadinessProbe探针：用于判断容器服务是否可用（Ready状态），达到Ready状态的Pod才可以接收请求。对于被Service管理的Pod，Service与Pod Endpoint的关联关系也将基于Pod是否Ready进行设置。如果在运行过程中Ready状态变为False，则系统自动将其从Service的后端Endpoint列表中隔离出去，后续再把恢复到Ready状态的Pod加回后端Endpoint列表。这样就能保证客户端在访问Service时不会被转发到服务不可用的Pod实例上。

LivenessProbe和ReadinessProbe均可配置以下三种实现方式。

（1）ExecAction：在容器内部执行一个命令，如果该命令的返回码为0，则表明容器健康。

（2）TCPSocketAction：通过容器的IP地址和端口号执行TCP检查，如果能够建立TCP连接，则表明容器健康。

（3）HTTPGetAction：通过容器的IP地址、端口号及路径调用HTTP Get方法，如果响应的状态码大于等于200且小于400，则认为容器健康。

对于每种探测方式，都需要设置initialDelaySeconds和timeoutSeconds两个参数，它们的含义分别如下。

◎ initialDelaySeconds：启动容器后进行首次健康检查的等待时间，单位为s。

◎ timeoutSeconds：健康检查发送请求后等待响应的超时时间，单位为s。当超时发生时，kubelet会认为容器已经无法提供服务，将会重启该容器。

### Pod调度

#### Deployment或RC：全自动调度 

Deployment或RC的主要功能之一就是自动部署一个容器应用的多份副本，以及持续监控副本的数量，在集群内始终维持用户指定的副本数量。

#### NodeSelector：定向调度

Kubernetes Master上的Scheduler服务（kube-scheduler进程）负责实现Pod的调度，整个调度过程通过执行一系列复杂的算法，最终为每个Pod都计算出一个最佳的目标节点，这一过程是自动完成的，通常我们无法知道Pod最终会被调度到哪个节点上。在实际情况下，也可能需要将Pod调度到指定的一些Node上，可以通过Node的标签（Label）和Pod的nodeSelector属性相匹配，来达到上述目的。

#### NodeAffinity：Node亲和性调度

NodeAffinity意为Node亲和性的调度策略，是用于替换NodeSelector的全新调度策略。目前有两种节点亲和性表达。

◎ RequiredDuringSchedulingIgnoredDuringExecution：必须满足指定的规则才可以调度Pod到Node上（功能与nodeSelector很像，但是使用的是不同的语法），相当于硬限制。

◎ PreferredDuringSchedulingIgnoredDuringExecution：强调优先满足指定规则，调度器会尝试调度Pod到Node上，但并不强求，相当于软限制。多个优先级规则还可以设置权重（weight）值，以定义执行的先后顺序。

#### PodAffinity：Pod亲和与互斥调度策略

Pod间的亲和与互斥从Kubernetes 1.4版本开始引入。这一功能让用户从另一个角度来限制Pod所能运行的节点：根据在节点上正在运行的Pod的标签而不是节点的标签进行判断和调度，要求对节点和Pod两个条件进行匹配。这种规则可以描述为：如果在具有标签X的Node上运行了一个或者多个符合条件Y的Pod，那么Pod应该（如果是互斥的情况，那么就变成拒绝）运行在这个Node上。

#### Taints和Tolerations（污点和容忍）

Taint需要和Toleration配合使用，让Pod避开那些不合适的Node。在Node上设置一个或多个Taint之后，除非Pod明确声明能够容忍这些污点，否则无法在这些Node上运行。Toleration是Pod的属性，让Pod能够（注意，只是能够，而非必须）运行在标注了Taint的Node上。

#### Pod Priority Preemption：Pod优先级调度

优先级抢占调度策略的核心行为分别是驱逐（Eviction）与抢占（Preemption），这两种行为的使用场景不同，效果相同。Eviction是kubelet进程的行为，即当一个Node发生资源不足（under resource pressure）的情况时，该节点上的kubelet进程会执行驱逐动作，此时Kubelet会综合考虑Pod的优先级、资源申请量与实际使用量等信息来计算哪些Pod需要被驱逐；当同样优先级的Pod需要被驱逐时，实际使用的资源量超过申请量最大倍数的高耗能Pod会被首先驱逐。对于QoS等级为“Best Effort”的Pod来说，由于没有定义资源申请（CPU/Memory Request），所以它们实际使用的资源可能非常大。Preemption则是Scheduler执行的行为，当一个新的Pod因为资源无法满足而不能被调度时，Scheduler可能（有权决定）选择驱逐部分低优先级的Pod实例来满足此Pod的调度目标，这就是Preemption机制。

#### DaemonSet：在每个Node上都调度一个Pod

![image-20200325031540010](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325031540010.png)

这种用法适合有这种需求的应用。

◎ 在每个Node上都运行一个GlusterFS存储或者Ceph存储的Daemon进程。

◎ 在每个Node上都运行一个日志采集程序，例如Fluentd或者Logstach。

◎ 在每个Node上都运行一个性能监控程序，采集该Node的运行性能数据，例如Prometheus Node Exporter、collectd、New Relic agent或者Ganglia gmond等。

#### Job：批处理调度

Kubernetes从1.2版本开始支持批处理类型的应用，我们可以通过Kubernetes Job资源对象来定义并启动一个批处理任务。批处理任务通常并行（或者串行）启动多个计算进程去处理一批工作项（work item），处理完成后，整个批处理任务结束。按照批处理任务实现方式的不同，批处理任务可以分为如图3.4所示的几种模式。

![image-20200325031623593](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325031623593.png)

◎ Job Template Expansion模式：一个Job对象对应一个待处理的Work item，有几个Work item就产生几个独立的Job，通常适合Work item数量少、每个Work item要处理的数据量比较大的场景，比如有一个100GB的文件作为一个Work item，总共有10个文件需要处理。

◎ Queue with Pod Per Work Item模式：采用一个任务队列存放Work item，一个Job对象作为消费者去完成这些Work item，在这种模式下，Job会启动N个Pod，每个Pod都对应一个Work item。

◎ Queue with Variable Pod Count模式：也是采用一个任务队列存放Work item，一个Job对象作为消费者去完成这些Work item，但与上面的模式不同，Job启动的Pod数量是可变的。

#### Cronjob：定时任务

Kubernetes从1.5版本开始增加了一种新类型的Job，即类似Linux Cron的定时任务Cron Job，下面看看如何定义和使用这种类型的Job。首先，确保Kubernetes的版本为1.8及以上。

![image-20200325031738942](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325031738942.png)

其中每个域都可出现的字符如下。

其中每个域都可出现的字符如下。

◎ Minutes：可出现“,”“-”“”“/”这4个字符，有效范围为0～59的整数。

◎ Hours：可出现“,”“-”“”“/”这4个字符，有效范围为0～23的整数。

◎ DayofMonth：可出现“,”“-”“”“/”“?”“L”“W”“C”这8个字符，有效范围为0～31的整数。

◎ Month：可出现“,”“-”“”“/”这4个字符，有效范围为1～12的整数或JAN～DEC。

◎ DayofWeek：可出现“,”“-”“”“/”“?”“L”“C”“#”这8个字符，有效范围为1～7的整数或SUN～SAT。1表示星期天，2表示星期一，以此类推。表达式中的特殊字符“”与“/”的含义如下。

◎ ：表示匹配该域的任意值，假如在Minutes域使用“”，则表示每分钟都会触发事件。

◎ /：表示从起始时间开始触发，然后每隔固定时间触发一次，例如在Minutes域设置为5/20，则意味着第1次触发在第5min时，接下来每20min触发一次，将在第25min、第45min等时刻分别触发。

### Init Container（初始化容器）

Kubernetes 1.3引入了一个Alpha版本的新特性init container（初始化容器，在Kubernetes1.5时被更新为Beta版本），用于在启动应用容器（app container）之前启动一个或多个初始化容器，完成应用容器所需的预置条件，如图3.7所示。init container与应用容器在本质上是一样的，但它们是仅运行一次就结束的任务，并且必须在成功执行完成后，系统才能继续执行下一个容器。根据Pod的重启策略（RestartPolicy），当init container执行失败，而且设置了RestartPolicy=Never时，Pod将会启动失败；而设置RestartPolicy=Always时，Pod将会被系统自动重启。

### Pod的升级和回滚

对更新策略的说明如下。在Deployment的定义中，可以通过spec.strategy指定Pod更新的策略，目前支持两种策略：Recreate（重建）和RollingUpdate（滚动更新），默认值为RollingUpdate。在前面的例子中使用的就是RollingUpdate策略。

◎ Recreate：设置spec.strategy.type=Recreate，表示Deployment在更新Pod时，会先杀掉所有正在运行的Pod，然后创建新的Pod。

◎ RollingUpdate：设置spec.strategy.type=RollingUpdate，表示Deployment会以滚动更新的方式来逐个更新Pod。同时，可以通过设置spec.strategy.rollingUpdate下的两个参数（maxUnavailable和maxSurge）来控制滚动更新的过程。

下面对滚动更新时两个主要参数的说明如下。

◎ spec.strategy.rollingUpdate.maxUnavailable：用于指定Deployment在更新过程中不可用状态的Pod数量的上限。该maxUnavailable的数值可以是绝对值（例如5）或Pod期望的副本数的百分比（例如10%），如果被设置为百分比，那么系统会先以向下取整的方式计算出绝对值（整数）。而当另一个参数maxSurge被设置为0时，maxUnavailable则必须被设置为绝对数值大于0（从Kubernetes 1.6开始，maxUnavailable的默认值从1改为25%）。举例来说，当maxUnavailable被设置为30%时，旧的ReplicaSet可以在滚动更新开始时立即将副本数缩小到所需副本总数的70%。一旦新的Pod创建并准备好，旧的ReplicaSet会进一步缩容，新的ReplicaSet又继续扩容，整个过程中系统在任意时刻都可以确保可用状态的Pod总数至少占Pod期望副本总数的70%。

◎ spec.strategy.rollingUpdate.maxSurge：用于指定在Deployment更新Pod的过程中Pod总数超过Pod期望副本数部分的最大值。该maxSurge的数值可以是绝对值（例如5）或Pod期望副本数的百分比（例如10%）。如果设置为百分比，那么系统会先按照向上取整的方式计算出绝对数值（整数）。从Kubernetes 1.6开始，maxSurge的默认值从1改为25%。举例来说，当maxSurge的值被设置为30%时，新的ReplicaSet可以在滚动更新开始时立即进行副本数扩容，只需要保证新旧ReplicaSet的Pod副本数之和不超过期望副本数的130%即可。一旦旧的Pod被杀掉，新的ReplicaSet就会进一步扩容。在整个过程中系统在任意时刻都能确保新旧ReplicaSet的Pod副本总数之和不超过所需副本数的130%。

### Pod的扩缩容

Kubernetes从1.1版本开始，新增了名为Horizontal Pod Autoscaler（HPA）的控制器，用于实现基于CPU使用率进行自动Pod扩缩容的功能。HPA控制器基于Master的kube-controller-manager服务启动参数--horizontal-pod-autoscaler-sync-period定义的探测周期（默认值为15s），周期性地监测目标Pod的资源性能指标，并与HPA资源对象中的扩缩容条件进行对比，在满足条件时对Pod副本数量进行调整。

## Service

### Service定义详解

```yml
apiVersion: v1             
kind: Service              # string     Required    资源类型
metadata:                  # Object     Required    元数据
  name: string             # string     Required    Service名称
  namespace: string        # string     Required    命名空间，默认default
  labels:                  # list       Required    自定义标签属性列表
    - name: string
  annotations:             # list       Required    自定义注解属性列表
    - name: string
spec:                      # Object     Required    详细描述
  selector: []             # list       Required    Label Selector配置，将选择具有指定Label标签的Pod作为管理范围
  type: string             # string     Required    Service的类型，指定Service的访问方式，默认为ClusterIP
                           # ClusterIP：虚拟的服务IP地址，改地址用于Kubernetes集群内部的Pod访问，在Node上kube-proxy通过设置iptables规则进行转发
                           # NodePort:  使用宿主机的端口，使能够访问个Node的外部客户端通过Node的IP地址和端口号就能访问服务
                           # LoadBalancer: 使用外接负载均衡完成到服务的负载分发，需要在spec.status.loadBalancer字段指定外部负载均衡器的IP地址，
                           #               并同时定义nodePort和clusterIP，用于公有云环境
  clusterIP: string        # string     虚拟服务IP地址，当type=ClusterIP时，如果不指定，则系统自动分配，也可以手动指定；当type=LoaderBalancer需要指定
  sessionAffinity: string  # string     是否支持Session，可选值为ClientIP，默认为空
                           # ClientIP:  表示将同一个客户端（根据IP地址决定）的访问请求都转发到同一个后端Pod
  ports:                   # list       需要暴露的端口列表
    - name: string         # 端口名称
      protocol: string     # 端口协议    支持tcp和udp，默认为tcp
      port: int            # 服务监听的端口号
      targetPort: int      # 需要转发到后端Pod的端口号
      nodePort: int        # 当spec.type=NodePort时，指定映射到物理机的端口号
  status:                  # 当spec.type=LoadBalancer时，设置外部负载均衡器的地址，用于公有云环境
    loadBalancer:          # 外部负载均衡器
      ingress:             # 外部负载均衡器
        ip: string         # 外部负载均衡器的IP地址
        hostname: string   # 外部负载均衡器的主机名
```

### 外网如何访问Service

除了Cluster内部可以访问Service，很多情况下我们也希望应用的Service能够暴露给Cluster外部。Kubernetes提供了多种类型的Service，默认是ClusterIP。

（1）ClusterIP

Service通过Cluster内部的IP对外提供服务，只有Cluster内的节点和Pod可访问，这是默认的Service类型，前面实验中的Service都是ClusterIP。

（2）NodePort

Service通过Cluster节点的静态端口对外提供服务。Cluster外部可以通过<NodeIP>:<NodePort>访问Service。

（3）LoadBalancer

Service利用cloud provider特有的load balancer对外提供服务，cloud provider负责将loadbalancer的流量导向Service。目前支持的cloud provider有GCP、AWS、Azur等。

### DNS服务搭建和配置指南

从Kubernetes 1.11版本开始，Kubernetes集群的DNS服务由CoreDNS提供。CoreDNS是CNCF基金会的一个项目，是用Go语言实现的高性能、插件式、易扩展的DNS服务端。CoreDNS解决了KubeDNS的一些问题，例如dnsmasq的安全漏洞、externalName不能使用stubDomains设置，等等。

CoreDNS支持自定义DNS记录及配置upstream DNS Server，可以统一管理Kubernetes基于服务的内部DNS和数据中心的物理DNS。

![image-20200325033104127](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325033104127.png)

CoreDNS的主要功能是通过插件系统实现的。CoreDNS实现了一种链式插件结构，将DNS的逻辑抽象成了一个个插件，能够灵活组合使用。常用的插件如下。

◎ loadbalance：提供基于DNS的负载均衡功能。

◎ loop：检测在DNS解析过程中出现的简单循环问题。

◎ cache：提供前端缓存功能。

◎ health：对Endpoint进行健康检查。

◎ kubernetes：从Kubernetes中读取zone数据。

◎ etcd：从etcd读取zone数据，可以用于自定义域名记录。

◎ file：从RFC1035格式文件中读取zone数据。

◎ hosts：使用/etc/hosts文件或者其他文件读取zone数据，可以用于自定义域名记录。

◎ auto：从磁盘中自动加载区域文件。

◎ reload：定时自动重新加载Corefile配置文件的内容。

◎ forward：转发域名查询到上游DNS服务器。

◎ proxy：转发特定的域名查询到多个其他DNS服务器，同时提供到多个DNS服务器的负载均衡功能。

◎ prometheus：为Prometheus系统提供采集性能指标数据的URL。

◎ pprof：在URL路径/debug/pprof下提供运行时的性能数据。

◎ log：对DNS查询进行日志记录。

◎ errors：对错误信息进行日志记录。

目前可以设置的DNS策略如下。

◎ Default：继承Pod所在宿主机的DNS设置。

◎ ClusterFirst：优先使用Kubernetes环境的DNS服务（如CoreDNS提供的域名解析服务），将无法解析的域名转发到从宿主机继承的DNS服务器。

◎ ClusterFirstWithHostNet：与ClusterFirst相同，对于以hostNetwork模式运行的Pod，应明确指定使用该策略。

◎ None：忽略Kubernetes环境的DNS配置，通过spec.dnsConfig自定义DNS配置。这个选项从Kubernetes 1.9版本开始引入，到Kubernetes 1.10版本升级为Beta版，到Kubernetes 1.14版本升级为稳定版。

### Ingress：HTTP 7层路由机制

根据前面对Service的使用说明，我们知道Service的表现形式为IP:Port，即工作在TCP/IP层。而对于基于HTTP的服务来说，不同的URL地址经常对应到不同的后端服务或者虚拟服务器（Virtual Host），这些应用层的转发机制仅通过Kubernetes的Service机制是无法实现的。从Kubernetes 1.1版本开始新增Ingress资源对象，用于将不同URL的访问请求转发到后端不同的Service，以实现HTTP层的业务路由机制。Kubernetes使用了一个Ingress策略定义和一个具体的Ingress Controller，两者结合并实现了一个完整的Ingress负载均衡器。

使用Ingress进行负载分发时，Ingress Controller基于Ingress规则将客户端请求直接转发到Service对应的后端Endpoint（Pod）上，这样会跳过kube-proxy的转发功能，kube-proxy不再起作用。如果Ingress Controller提供的是对外服务，则实际上实现的是边缘路由器的功能。

### Ingress的策略配置技巧

1．转发到单个后端服务上

2．同一域名下，不同的URL路径被转发到不同的服务上

3．不同的域名（虚拟主机名）被转发到不同的服务上

4．不使用域名的转发规则

## 核心组件运行机制

### Kubernetes API Server

总体来看，Kubernetes API Server的核心功能是提供Kubernetes各类资源对象（如Pod、RC、Service等）的增、删、改、查及Watch等HTTP Rest接口，成为集群内各个功能模块之间数据交互和通信的中心枢纽，是整个系统的数据总线和数据中心。除此之外，它还有以下一些功能特性。（1）是集群管理的API入口。（2）是资源配额控制的入口。（3）提供了完备的集群安全机制。

API Server的架构从上到下可以分为以下几层，如图5.2所示。![image-20200325034027322](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325034027322.png)

（1）API层：主要以REST方式提供各种API接口，除了有Kubernetes资源对象的CRUD和Watch等主要API，还有健康检查、UI、日志、性能指标等运维监控相关的API。Kubernetes从1.11版本开始废弃Heapster监控组件，转而使用Metrics Server提供Metrics API接口，进一步完善了自身的监控能力。

（2）访问控制层：当客户端访问API接口时，访问控制层负责对用户身份鉴权，验明用户身份，核准用户对Kubernetes资源对象的访问权限，然后根据配置的各种资源访问许可逻辑（Admission Control），判断是否允许访问。

（3）注册表层：Kubernetes把所有资源对象都保存在注册表（Registry）中，针对注册表中的各种资源对象都定义了：资源对象的类型、如何创建资源对象、如何转换资源的不同版本，以及如何将资源编码和解码为JSON或ProtoBuf格式进行存储。

（4）etcd数据库：用于持久化存储Kubernetes资源对象的KV数据库。etcd的watch API接口对于API Server来说至关重要，因为通过这个接口，API Server创新性地设计了List-Watch这种高性能的资源对象实时同步机制，使Kubernetes可以管理超大规模的集群，及时响应和快速处理集群中的各种事件。

### Controller Manager原理解析

![image-20200325034124392](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325034124392.png)

如图5.7所示，Controller Manager内部包含Replication Controller、Node Controller、ResourceQuota Controller、Namespace Controller、ServiceAccount Controller、TokenController、Service Controller及Endpoint Controller这8种Controller，每种Controller都负责一种特定资源的控制流程，而Controller Manager正是这些Controller的核心管理者。

#### Replication Controller

Replication Controller的核心作用是确保在任何时候集群中某个RC关联的Pod副本数量都保持预设值。如果发现Pod的副本数量超过预期值，则Replication Controller会销毁一些Pod副本；反之，Replication Controller会自动创建新的Pod副本，直到符合条件的Pod副本数量达到预设值。需要注意：只有当Pod的重启策略是Always时（RestartPolicy=Always），Replication Controller才会管理该Pod的操作（例如创建、销毁、重启等）。在通常情况下，Pod对象被成功创建后不会消失，唯一的例外是当Pod处于succeeded或failed状态的时间过长（超时参数由系统设定）时，该Pod会被系统自动回收，管理该Pod的副本控制器将在其他工作节点上重新创建、运行该Pod副本。

总结一下Replication Controller的职责，如下所述。

（1）确保在当前集群中有且仅有N个Pod实例，N是在RC中定义的Pod副本数量。

（2）通过调整RC的spec.replicas属性值来实现系统扩容或者缩容。

（3）通过改变RC中的Pod模板（主要是镜像版本）来实现系统的滚动升级。

最后总结一下Replication Controller的典型使用场景，如下所述。

（1）重新调度（Rescheduling）。如前面所述，不管想运行1个副本还是1000个副本，副本控制器都能确保指定数量的副本存在于集群中，即使发生节点故障或Pod副本被终止运行等意外状况。

（2）弹性伸缩（Scaling）。手动或者通过自动扩容代理修改副本控制器的spec.replicas属性值，非常容易实现增加或减少副本的数量。

（3）滚动更新（Rolling Updates）。副本控制器被设计成通过逐个替换Pod的方式来辅助服务的滚动更新。推荐的方式是创建一个只有一个副本的新RC，若新RC副本数量加1，则旧RC的副本数量减1，直到这个旧RC的副本数量为0，然后删除该旧RC。通过上述模式，即使在滚动更新的过程中发生了不可预料的错误，Pod集合的更新也都在可控范围内。在理想情况下，滚动更新控制器需要将准备就绪的应用考虑在内，并保证在集群中任何时刻都有足够数量的可用Pod。

#### Node Controller

Node Controller通过API Server实时获取Node的相关信息，实现管理和监控集群中的各个Node的相关控制功能。

对流程中关键点的解释如下。

（1）Controller Manager在启动时如果设置了--cluster-cidr参数，那么为每个没有设置Spec.PodCIDR的Node都生成一个CIDR地址，并用该CIDR地址设置节点的Spec.PodCIDR属性，这样做的目的是防止不同节点的CIDR地址发生冲突。

（2）逐个读取Node信息，多次尝试修改nodeStatusMap中的节点状态信息，将该节点信息和Node Controller的nodeStatusMap中保存的节点信息做比较。如果判断出没有收到kubelet发送的节点信息、第1次收到节点kubelet发送的节点信息，或在该处理过程中节点状态变成非“健康”状态，则在nodeStatusMap中保存该节点的状态信息，并用Node Controller所在节点的系统时间作为探测时间和节点状态变化时间。如果判断出在指定时间内收到新的节点信息，且节点状态发生变化，则在nodeStatusMap中保存该节点的状态信息，并用Node Controller所在节点的系统时间作为探测时间和节点状态变化时间。如果判断出在指定时间内收到新的节点信息，但节点状态没发生变化，则在nodeStatusMap中保存该节点的状态信息，并用NodeController所在节点的系统时间作为探测时间，将上次节点信息中的节点状态变化时间作为该节点的状态变化时间。如果判断出在某段时间（gracePeriod）内没有收到节点状态信息，则设置节点状态为“未知”，并且通过API Server保存节点状态。

（3）逐个读取节点信息，如果节点状态变为非“就绪”状态，则将节点加入待删除队列，否则将节点从该队列中删除。如果节点状态为非“就绪”状态，且系统指定了Cloud Provider，则Node Controller调用Cloud Provider查看节点，若发现节点故障，则删除etcd中的节点信息，并删除和该节点相关的Pod等资源的信息。

#### ResourceQuota Controller

作为完备的企业级的容器集群管理平台，Kubernetes也提供了ResourceQuota Controller（资源配额管理）这一高级功能，资源配额管理确保了指定的资源对象在任何时候都不会超量占用系统物理资源，避免了由于某些业务进程的设计或实现的缺陷导致整个系统运行紊乱甚至意外宕机，对整个集群的平稳运行和稳定性有非常重要的作用。

目前Kubernetes支持如下三个层次的资源配额管理。

（1）容器级别，可以对CPU和Memory进行限制。

（2）Pod级别，可以对一个Pod内所有容器的可用资源进行限制。

（3）Namespace级别，为Namespace（多租户）级别的资源限制。

#### Namespace Controller

用户通过API Server可以创建新的Namespace并将其保存在etcd中，Namespace Controller定时通过API Server读取这些Namespace的信息。如果Namespace被API标识为优雅删除（通过设置删除期限实现，即设置DeletionTimestamp属性），则将该NameSpace的状态设置成Terminating并保存到etcd中。同时Namespace Controller删除该Namespace下的ServiceAccount、RC、Pod、Secret、PersistentVolume、ListRange、ResourceQuota和Event等资源对象。

#### Service Controller

Service Controller的作用，它其实是属于Kubernetes集群与外部的云平台之间的一个接口控制器。Service Controller监听Service的变化，如果该Service是一个LoadBalancer类型的Service（externalLoadBalancers=true），则Service Controller确保在外部的云平台上该Service对应的LoadBalancer实例被相应地创建、删除及更新路由转发表（根据Endpoints的条目）。

#### Endpoints Controller

EndpointsController就是负责生成和维护所有Endpoints对象的控制器。

### Scheduler原理解析

Kubernetes Scheduler在整个系统中承担了“承上启下”的重要功能，“承上”是指它负责接收Controller Manager创建的新Pod，为其安排一个落脚的“家”——目标Node；“启下”是指安置工作完成后，目标Node上的kubelet服务进程接管后继工作，负责Pod生命周期中的“下半生”。

具体来说，Kubernetes Scheduler的作用是将待调度的Pod（API新创建的Pod、ControllerManager为补足副本而创建的Pod等）按照特定的调度算法和调度策略绑定（Binding）到集群中某个合适的Node上，并将绑定信息写入etcd中。在整个调度过程中涉及三个对象，分别是待调度Pod列表、可用Node列表，以及调度算法和策略。简单地说，就是通过调度算法调度为待调度Pod列表中的每个Pod从Node列表中选择一个最适合的Node。

随后，目标节点上的kubelet通过API Server监听到Kubernetes Scheduler产生的Pod绑定事件，然后获取对应的Pod清单，下载Image镜像并启动容器。

![image-20200325034705434](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325034705434.png)

Kubernetes Scheduler当前提供的默认调度流程分为以下两步。

（1）预选调度过程，即遍历所有目标Node，筛选出符合要求的候选节点。为此，Kubernetes内置了多种预选策略（xxx Predicates）供用户选择。

（2）确定最优节点，在第1步的基础上，采用优选策略（xxx Priority）计算出每个候选节点的积分，积分最高者胜出。

Scheduler中可用的预选策略包含： NoDiskConflict 、 PodFitsResources 、PodSelectorMatches 、 PodFitsHost 、 CheckNodeLabelPresence 、CheckServiceAffinity和PodFitsPorts策略等。其默认的AlgorithmProvider加载的预选策略Predicates包括：PodFitsPorts（PodFitsPorts）、PodFitsResources（PodFitsResources）、 NoDiskConflict（NoDiskConflict）、MatchNodeSelector（PodSelectorMatches）和HostName（PodFitsHost），即每个节点只有通过前面提及的5个默认预选策略后，才能初步被选中，进入下一个流程。

1）NoDiskConflict

判断备选Pod的gcePersistentDisk或AWSElasticBlockStore和备选的节点中已存在的Pod是否存在冲突。

2）PodFitsResources

判断备选节点的资源是否满足备选Pod的需求。

3）PodSelectorMatches

判断备选节点是否包含备选Pod的标签选择器指定的标签。

4）PodFitsHost

判断备选Pod的spec.nodeName域所指定的节点名称和备选节点的名称是否一致，如果一致，则返回true，否则返回false。

5）CheckNodeLabelPresence

如果用户在配置文件中指定了该策略，则Scheduler会通过RegisterCustomFitPredicate方法注册该策略。该策略用于判断策略列出的标签在备选节点中存在时，是否选择该备选节点。

6）CheckServiceAffinity

如果用户在配置文件中指定了该策略，则Scheduler会通过RegisterCustomFitPredicate方法注册该策略。该策略用于判断备选节点是否包含策略指定的标签，或包含和备选Pod在相同Service和Namespace下的Pod所在节点的标签列表。如果存在，则返回true，否则返回false。

7）PodFitsPorts

判断备选Pod所用的端口列表中的端口是否在备选节点中已被占用，如果被占用，则返回false，否则返回true。

Scheduler中的优选策略包含：LeastRequestedPriority、CalculateNodeLabelPriority和BalancedResourceAllocation等。每个节点通过优先选择策略时都会算出一个得分，计算各项得分，最终选出得分值最大的节点作为优选的结果（也是调度算法的结果）。

下面是对所有优选策略的详细说明。

1）LeastRequestedPriority

该优选策略用于从备选节点列表中选出资源消耗最小的节点。

2）CalculateNodeLabelPriority

如果用户在配置文件中指定了该策略，则scheduler会通过RegisterCustomPriorityFunction方法注册该策略。该策略用于判断策略列出的标签在备选节点中存在时，是否选择该备选节点。如果备选节点的标签在优选策略的标签列表中且优选策略的presence值为true，或者备选节点的标签不在优选策略的标签列表中且优选策略的presence值为false，则备选节点score=10，否则备选节点score=0。

3）BalancedResourceAllocation

该优选策略用于从备选节点列表中选出各项资源使用率最均衡的节点。

### kubelet运行机制解析

在Kubernetes集群中，在每个Node（又称Minion）上都会启动一个kubelet服务进程。该进程用于处理Master下发到本节点的任务，管理Pod及Pod中的容器。每个kubelet进程都会在APIServer上注册节点自身的信息，定期向Master汇报节点资源的使用情况，并通过cAdvisor监控容器和节点资源。

####  节点管理

节点通过设置kubelet的启动参数“--register-node”，来决定是否向API Server注册自己。如果该参数的值为true，那么kubelet将试着通过API Server注册自己。在自注册时，kubelet启动时还包含下列参数。

◎ --api-servers：API Server的位置。

◎ --kubeconfig：kubeconfig文件，用于访问API Server的安全配置文件。

◎ --cloud-provider：云服务商（IaaS）地址，仅用于公有云环境。

#### Pod管理

kubelet通过以下几种方式获取自身Node上要运行的Pod清单。

（1）文件：kubelet启动参数“--config”指定的配置文件目录下的文件（默认目录为“/etc/kubernetes/manifests/”）。通过--file-check-frequency设置检查该文件目录的时间间隔，默认为20s。

（2）HTTP端点（URL）：通过“--manifest-url”参数设置。通过--http-check-frequency设置检查该HTTP端点数据的时间间隔，默认为20s。

（3）API Server：kubelet通过API Server监听etcd目录，同步Pod列表。

#### 容器健康检查

Pod通过两类探针来检查容器的健康状态。一类是LivenessProbe探针，用于判断容器是否健康并反馈给kubelet。如果LivenessProbe探针探测到容器不健康，则kubelet将删除该容器，并根据容器的重启策略做相应的处理。如果一个容器不包含LivenessProbe探针，那么kubelet认为该容器的LivenessProbe探针返回的值永远是Success；另一类是ReadinessProbe探针，用于判断容器是否启动完成，且准备接收请求。如果ReadinessProbe探针检测到容器启动失败，则Pod的状态将被修改，Endpoint Controller将从Service的Endpoint中删除包含该容器所在Pod的IP地址的Endpoint条目。

kubelet定期调用容器中的LivenessProbe探针来诊断容器的健康状况。LivenessProbe包含以下3种实现方式。

（1）ExecAction：在容器内部执行一个命令，如果该命令的退出状态码为0，则表明容器健康。

（2）TCPSocketAction：通过容器的IP地址和端口号执行TCP检查，如果端口能被访问，则表明容器健康。

（3）HTTPGetAction：通过容器的IP地址和端口号及路径调用HTTP Get方法，如果响应的状态码大于等于200且小于等于400，则认为容器状态健康。

### kube-proxy运行机制

kube-proxy针对Service和Pod创建的一些主要的iptables规则如下。

◎ KUBE-CLUSTER-IP：在masquerade-all=true或clusterCIDR指定的情况下对ServiceCluster IP地址进行伪装，以解决数据包欺骗问题。

◎ KUBE-EXTERNAL-IP：将数据包伪装成Service的外部IP地址。

◎ KUBE-LOAD-BALANCER 、 KUBE-LOAD-BALANCER-LOCAL ：伪装Load Balancer类型的Service流量。

◎ KUBE-NODE-PORT-TCP、KUBE-NODE-PORT-LOCAL-TCP、KUBE-NODE-PORT-UDP、KUBE-NODE-PORT-LOCAL-UDP：伪装NodePort类型的Service流量。

## 集群安全机制

Kubernetes通过一系列机制来实现集群的安全控制，其中包括API Server的认证授权、准入控制机制及保护敏感信息的Secret机制等。集群的安全性必须考虑如下几个目标。

（1）保证容器与其所在宿主机的隔离。

（2）限制容器给基础设施或其他容器带来的干扰。

（3）最小权限原则——合理限制所有组件的权限，确保组件只执行它被授权的行为，通过限制单个组件的能力来限制它的权限范围。

（4）明确组件间边界的划分。

（5）划分普通用户和管理员的角色。

（6）在必要时允许将管理员权限赋给普通用户。

（7）允许拥有Secret数据（Keys、Certs、Passwords）的应用在集群中运行。

### API Server认证管理

Kubernetes集群提供了3种级别的客户端身份认证方式。

◎ 最严格的HTTPS证书认证：基于CA根证书签名的双向数字证书认证方式。

◎ HTTP Token认证：通过一个Token来识别合法用户。

◎ HTTP Base认证：通过用户名+密码的方式认证。

### API Server授权管理

当客户端发起API Server调用时，API Server内部要先进行用户认证，然后执行用户授权流程，即通过授权策略来决定一个API调用是否合法。对合法用户进行授权并且随后在用户访问时进行鉴权，是权限与安全系统的重要一环。简单地说，授权就是授予不同的用户不同的访问权限。API Server目前支持以下几种授权策略（通过API Server的启动参数“--authorization-mode”设置）。

◎ AlwaysDeny：表示拒绝所有请求，一般用于测试。

◎ AlwaysAllow：允许接收所有请求，如果集群不需要授权流程，则可以采用该策略，这也是Kubernetes的默认配置。

◎ ABAC（Attribute-Based Access Control）：基于属性的访问控制，表示使用用户配置的授权规则对用户请求进行匹配和控制。

◎ Webhook：通过调用外部REST服务对用户进行授权。

◎ RBAC：Role-Based Access Control，基于角色的访问控制。

◎ Node：是一种专用模式，用于对kubelet发出的请求进行访问控制。

###  Admission Control

Admission Control配备了一个准入控制器的插件列表，发送给API Server的任何请求都需要通过列表中每个准入控制器的检查，检查不通过，则API Server拒绝此调用请求。此外，准入控制器插件能够修改请求参数以完成一些自动化任务，比如ServiceAccount这个控制器插件。当前可配置的准入控制器插件如下。

◎ AlwaysAdmit：已弃用，允许所有请求。

◎ AlwaysPullImages：在启动容器之前总是尝试重新下载镜像。这对于多租户共享一个集群的场景非常有用，系统在启动容器之前可以保证总是使用租户的密钥去下载镜像。如果不设置这个控制器，则在Node上下载的镜像的安全性将被削弱，只要知道该镜像的名称，任何人便都可以使用它们了。

◎ AlwaysDeny：已弃用，禁止所有请求，用于测试。

◎ DefaultStorageClass：会关注PersistentVolumeClaim资源对象的创建，如果其中没有包含任何针对特定Storage class的请求，则为其指派指定的Storage class。在这种情况下，用户无须在PVC中设置任何特定的Storage class就能完成PVC的创建了。如果没有设置默认的Storage class，该控制器就不会进行任何操作；如果设置了超过一个的默认Storage class，该控制器就会拒绝所有PVC对象的创建申请，并返回错误信息。管理员必须检查StorageClass对象的配置，确保只有一个默认值。该控制器仅关注PVC的创建过程，对更新过程无效。

◎ DefaultTolerationSeconds：针对没有设置容忍node.kubernetes.io/not-ready:NoExecute或者node.alpha.kubernetes.io/unreachable:NoExecute的Pod，设置5min的默认容忍时间。

◎ DenyExecOnPrivileged：已弃用，拦截所有想在Privileged Container上执行命令的请求。如果你的集群支持Privileged Container，又希望限制用户在这些PrivilegedContainer上执行命令，那么强烈推荐使用它。其功能已被合并到DenyEscalatingExec中。

◎ DenyEscalatingExec：拦截所有exec和attach到具有特权的Pod上的请求。如果你的集群支持运行有escalated privilege权限的容器，又希望限制用户在这些容器内执行命令，那么强烈推荐使用它。

◎ EventReateLimit：Alpha版本，用于应对事件密集情况下对API Server造成的洪水攻击。

◎ ExtendedResourceToleration：如果运维人员要创建带有特定资源（例如GPU、FPGA等）的独立节点，则可能会对节点进行Taint处理来进行特别配置。该控制器能够自动为申请这些特别资源的Pod加入Toleration定义，无须人工干预。

◎ ImagePolicyWebhook：这个插件将允许后端的一个Webhook程序来完成admissioncontroller的功能。ImagePolicyWebhook需要使用一个配置文件（通过kube-apiserver的启动参数--admission-control-config-file设置）定义后端Webhook的参数。目前是Alpha版本的功能。

◎ Initializers：Alpha。用于为动态准入控制提供支持，通过修改待创建资源的元数据来完成对该资源的修改。

◎ LimitPodHardAntiAffinityTopology：该插件启用了Pod的反亲和性调度策略设置，在设置亲和性策略参数requiredDuringSchedulingRequiredDuringExecution时要求将topologyKey的值设置为“kubernetes.io/hostname”，否则Pod会被拒绝创建。

◎ LimitRanger：这个插件会监控进入的请求，确保请求的内容符合在Namespace中定义的LimitRange对象里的资源限制。如果要在Kubernetes集群中使用LimitRange对象，则必须启用该插件才能实施这一限制。LimitRanger还能用于为没有设置资源请求的Pod自动设置默认的资源请求，该插件会为default命名空间中的所有Pod设置0.1CPU的资源请求。请参考第10章对LimitRange原理和用法的详细说明。

◎ MutatingAdmissionWebhook ： Beta。这一插件会变更符合要求的请求的内容，Webhook以串行的方式顺序执行。

◎ NamespaceAutoProvision：这一插件会检测所有进入的具备命名空间的资源请求，如果其中引用的命名空间不存在，就会自动创建命名空间。

◎ NamespaceExists：这一插件会检测所有进入的具备命名空间的资源请求，如果其中引用的命名空间不存在，就会拒绝这一创建过程。

◎ NamespaceLifecycle：如果尝试在一个不存在的Namespace中创建资源对象，则该创建请求将被拒绝。当删除一个Namespace时，系统将会删除该Namespace中的所有对象，包括Pod、Service等，并阻止删除default、kube-system和kube-public这三个命名空间。

◎ NodeRestriction：该插件会限制kubelet对Node和Pod的修改行为。为了实现这一限制，kubelet必须使用system:nodes组中用户名为system:node:<nodeName>的Token来运行。符合条件的kubelet只能修改自己的Node对象，也只能修改分配到各自Node上的Pod对象。在Kubernetes 1.11以后的版本中，kubelet无法修改或者更新自身Node的taint属性。在Kubernetes 1.13以后，这一插件还会阻止kubelet删除自己的Node资源，并限制对有kubernetes.io/或k8s.io/前缀的标签的修改。

◎ OnwerReferencesPermissionEnforcement：在该插件启用后，一个用户要想修改对象的metadata.ownerReferences，就必须具备delete权限。该插件还会保护对象的metadata.ownerReferences[x].blockOwnerDeletion字段，用户只有在对finalizers子资源拥有update权限的时候才能进行修改。

◎ PersistentVolumeLabel：弃用。这一插件自动根据云供应商（例如GCE或AWS）的定义，为PersistentVolume对象加入region或zone标签，以此来保障PersistentVolume和Pod同处一区。如果插件不为PV自动设置标签，则需要用户手动保证Pod和其加载卷的相对位置。该插件正在被Cloud controller manager替换，从Kubernetes 1.11版本开始默认被禁止。

◎ PodNodeSelector：该插件会读取命名空间的annotation字段及全局配置，来对一个命名空间中对象的节点选择器设置默认值或限制其取值。

◎ PersistentVolumeClaimResize：该插件实现了对PersistentVolumeClaim发起的resize请求的额外校验。

◎ PodPreset：该插件会使用PodSelector选择Pod，为符合条件的Pod进行注入。

◎ PodSecurityPolicy：在创建或修改Pod时决定是否根据Pod的security context和可用的PodSecurityPolicy对Pod的安全策略进行控制。

◎ PodTolerationRestriction：该插件首先会在Pod和其命名空间的Toleration中进行冲突检测，如果其中存在冲突，则拒绝该Pod的创建。它会把命名空间和Pod的Toleration进行合并，然后将合并的结果与命名空间中的白名单进行比较，如果合并的结果不在白名单内，则拒绝创建。如果不存在命名空间级的默认Toleration和白名单，则会采用集群级别的默认Toleration和白名单。

◎ Priority：这一插件使用priorityClassName字段来确定优先级，如果没有找到对应的Priority Class，该Pod就会被拒绝。

◎ ResourceQuota：用于资源配额管理目的，作用于Namespace。该插件拦截所有请求，以确保在Namespace上的资源配额使用不会超标。推荐在Admission Control参数列表中将这个插件排最后一个，以免可能被其他插件拒绝的Pod被过早分配资源。在10.4节将详细介绍ResourceQuota的原理和用法。

◎ SecurityContextDeny：这个插件将在Pod中定义的SecurityContext选项全部失效。SecurityContext在Container中定义了操作系统级别的安全设定（uid 、 gid 、capabilities、SELinux等）。在未设置PodSecurityPolicy的集群中建议启用该插件，以禁用容器设置的非安全访问权限。

◎ ServiceAccount ：这个插件将ServiceAccount实现了自动化，如果想使用ServiceAccount对象，那么强烈推荐使用它，在后面讲述ServiceAccount的章节会详细说明其作用。

◎ StorageObjectInUseProtection ：这一插件会在新创建的PVC或PV中加入kubernetes.io/pvc-protection或kubernetes.io/pv-protection的finalizer。如果想要删除PVC或者PV，则直到所有finalizer的工作都完成，删除动作才会执行。

◎ ValidatingAdmissionWebhook：在Kubernetes 1.8中为Alpha版本，在Kubernetes1.9中为Beta版本。该插件会针对符合其选择要求的请求调用校验Webhook。目标Webhook会以并行方式运行；如果其中任何一个Webhook拒绝了该请求，该请求就会失败。

### Service Account

Service Account也是一种账号，但它并不是给Kubernetes集群的用户（系统管理员、运维人员、租户用户等）用的，而是给运行在Pod里的进程用的，它为Pod里的进程提供了必要的身份证明。

### Secret私密凭据

Secret的主要作用是保管私密数据，比如密码、OAuth Tokens、SSHKeys等信息。将这些私密信息放在Secret对象中比直接放在Pod或Docker Image中更安全，也更便于使用和分发。

### Pod的安全策略配置

在PodSecurityPolicy对象中可以设置下列字段来控制Pod运行时的各种安全策略。

1．特权模式相关配置

privileged：是否允许Pod以特权模式运行。

2．宿主机资源相关配置

（1）hostPID：是否允许Pod共享宿主机的进程空间。

（2）hostIPC：是否允许Pod共享宿主机的IPC命名空间。

（3）hostNetwork：是否允许Pod使用宿主机网络的命名空间。

（4）hostPorts：是否允许Pod使用宿主机的端口号，可以通过hostPortRange字段设置允许使用的端口号范围，以[min, max]设置最小端口号和最大端口号。

（5）Volumes：允许Pod使用的存储卷Volume类型，设置为“*”表示允许使用任意Volume类型，建议至少允许Pod使用下列Volume类型。

6）AllowedHostPaths：允许Pod使用宿主机的hostPath路径名称，可以通过pathPrefix字段设置路径的前缀，并可以设置是否为只读属性。

（7）FSGroup：设置允许访问某些Volume的Group ID范围，可以将规则（rule字段）设置为MustRunAs、MayRunAs或RunAsAny。

◎ MustRunAs ：需要设置Group ID的范围，例如1～65535，要求Pod的securityContext.fsGroup设置的值必须属于该Group ID的范围。

◎ MayRunAs：需要设置Group ID的范围，例如1～65535，不强制要求Pod设置securityContext.fsGroup。

◎ RunAsAny：不限制Group ID的范围，任何Group都可以访问Volume。

（8）ReadOnlyRootFilesystem：要求容器运行的根文件系统（root filesystem）必须是只读的。

（9）allowedFlexVolumes：对于类型为flexVolume的存储卷，设置允许使用的驱动类型。

3．用户和组相关配置

（1）RunAsUser：设置运行容器的用户ID（User ID）范围，规则字段（rule）的值可以被设置为MustRunAs、MustRunAsNonRoot或RunAsAny。

（2）RunAsGroup：设置运行容器的Group ID范围，规则字段的值可以被设置为MustRunAs、MustRunAsNonRoot或RunAsAny。

（3）SupplementalGroups：设置容器可以额外添加的Group ID范围，可以将规则（rule字段）设置为MustRunAs、MayRunAs或RunAsAny。

4．提升权限相关配置

（1）AllowPrivilegeEscalation：设置容器内的子进程是否可以提升权限，通常在设置非root用户（MustRunAsNonRoot）时进行设置。

（2）DefaultAllowPrivilegeEscalation：设置AllowPrivilegeEscalation的默认值，设置为disallow时，管理员还可以显式设置AllowPrivilegeEscalation来指定是否允许提升权限。

5．Linux能力相关配置

（1）AllowedCapabilities：设置容器可以使用的Linux能力列表，设置为“*”表示允许使用Linux的所有能力（如NET_ADMIN、SYS_TIME等）。

（2）RequiredDropCapabilities：设置不允许容器使用的Linux能力列表。

（3）DefaultAddCapabilities：设置默认为容器添加的Linux能力列表，例如SYS_TIME等，Docker建议默认设置的Linux能力请查看https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities。

6．SELinux相关配置

seLinux：设置SELinux参数，可以将规则字段（rule）的值设置为MustRunAs或RunAsAny。

7．其他Linux相关配置

（1）AllowedProcMountTypes ：设置允许的ProcMountTypes类型列表，可以设置allowedProcMountTypes或DefaultProcMount。

（2）AppArmor ：设置对容器可执行程序的访问控制权限，详情请参考https://kubernetes.io/docs/tutorials/clusters/apparmor/#podsecuritypolicy-annotations。

（3）Seccomp：设置允许容器使用的系统调用（System Calls）的profile。

（4）Sysctl：设置允许调整的内核参数，详情请参考https://kubernetes.io/docs/concepts/cluster-administration/sysctl-cluster/#podsecuritypolicy。

###  Pod的安全设置

在系统管理员对Kubernetes集群中设置了PodSecurityPolicy策略之后，系统将对Pod和Container级别的安全设置进行校验，对于不满足PodSecurityPolicy安全策略的Pod，系统将拒绝创建。

Pod和容器的安全策略可以在Pod或Container的securityContext字段中进行设置，如果在Pod和Container级别都设置了相同的安全类型字段，容器将使用Container级别的设置。在Pod级别可以设置的安全策略类型如下。

◎ runAsUser：容器内运行程序的用户ID。

◎ runAsGroup：容器内运行程序的用户组ID。

◎ runAsNonRoot：是否必须以非root用户运行程序。

◎ fsGroup：SELinux相关设置。

◎ seLinuxOptions：SELinux相关设置。

◎ supplementalGroups：允许容器使用的其他用户组ID。

◎ sysctls：设置允许调整的内核参数。在Container级别可以设置的安全策略类型如下。

◎ runAsUser：容器内运行程序的用户ID。

◎ runAsGroup：容器内运行程序的用户组ID。

◎ runAsNonRoot：是否必须以非root用户运行程序。

◎ privileged：是否以特权模式运行。

◎ allowPrivilegeEscalation：是否允许提升权限。

◎ readOnlyRootFilesystem：根文件系统是否为只读属性。

◎ capabilities：Linux能力列表。

◎ seLinuxOptions：SELinux相关设置。

## 网络原理

### Kubernetes网络模型

Kubernetes网络模型设计的一个基础原则是：每个Pod都拥有一个独立的IP地址，并假定所有Pod都在一个可以直接连通的、扁平的网络空间中。所以不管它们是否运行在同一个Node（宿主机）中，都要求它们可以直接通过对方的IP进行访问。设计这个原则的原因是，用户不需要额外考虑如何建立Pod之间的连接，也不需要考虑如何将容器端口映射到主机端口等问题。

Kubernetes对集群网络有如下要求。

（1）所有容器都可以在不用NAT的方式下同别的容器通信。

（2）所有节点都可以在不用NAT的方式下同所有容器通信，反之亦然。

（3）容器的地址和别人看到的地址是同一个地址。

### Docker网络基础

Docker本身的技术依赖于近年来Linux内核虚拟化技术的发展，所以Docker对Linux内核的特性有很强的依赖。这里将Docker使用到的与Linux网络有关的主要技术进行简要介绍，这些技术有：网络命名空间（Network Namespace）、Veth设备对、网桥、ipatables和路由。

#### 网络命名空间

为了支持网络协议栈的多个实例，Linux在网络栈中引入了网络命名空间，这些独立的协议栈被隔离到不同的命名空间中。处于不同命名空间中的网络栈是完全隔离的，彼此之间无法通信，就好像两个“平行宇宙”。通过对网络资源的隔离，就能在一个宿主机上虚拟多个不同的网络环境。Docker正是利用了网络的命名空间特性，实现了不同容器之间的网络隔离。

#### Veth设备对

引入Veth设备对是为了在不同的网络命名空间之间通信，利用它可以直接将两个网络命名空间连接起来。由于要连接两个网络命名空间，所以Veth设备都是成对出现的，很像一对以太网卡，并且中间有一根直连的网线。既然是一对网卡，那么我们将其中一端称为另一端的peer。在Veth设备的一端发送数据时，它会将数据直接发送到另一端，并触发另一端的接收操作。

#### 网桥

Linux可以支持多个不同的网络，它们之间能够相互通信，如何将这些网络连接起来并实现各网络中主机的相互通信呢？可以用网桥。网桥是一个二层的虚拟网络设备，把若干个网络接口“连接”起来，以使得网络接口之间的报文能够互相转发。网桥能够解析收发的报文，读取目标MAC地址的信息，和自己记录的MAC表结合，来决策报文的转发目标网络接口。为了实现这些功能，网桥会学习源MAC地址（二层网桥转发的依据就是MAC地址）。在转发报文时，网桥只需要向特定的网口进行转发，来避免不必要的网络交互。如果它遇到一个自己从未学习到的地址，就无法知道这个报文应该向哪个网络接口转发，就将报文广播给所有的网络接口（报文来源的网络接口除外）。

#### iptables和Netfilter

在Linux网络协议栈中有一组回调函数挂接点，通过这些挂接点挂接的钩子函数可以在Linux网络栈处理数据包的过程中对数据包进行一些操作，例如过滤、修改、丢弃等。整个挂接点技术叫作Netfilter和iptables。Netfilter负责在内核中执行各种挂接的规则，运行在内核模式中；而iptables是在用户模式下运行的进程，负责协助和维护内核中Netfilter的各种规则表。二者互相配合来实现整个Linux网络协议栈中灵活的数据包处理机制。

#### 路由

Linux系统包含一个完整的路由功能。当IP层在处理数据发送或者转发时，会使用路由表来决定发往哪里。在通常情况下，如果主机与目的主机直接相连，那么主机可以直接发送IP报文到目的主机，这个过程比较简单。例如，通过点对点的链接或网络共享，如果主机与目的主机没有直接相连，那么主机会将IP报文发送给默认的路由器，然后由路由器来决定往哪里发送IP报文。

路由功能由IP层维护的一张路由表来实现。当主机收到数据报文时，它用此表来决策接下来应该做什么操作。当从网络侧接收到数据报文时，IP层首先会检查报文的IP地址是否与主机自身的地址相同。如果数据报文中的IP地址是主机自身的地址，那么报文将被发送到传输层相应的协议中。如果报文中的IP地址不是主机自身的地址，并且主机配置了路由功能，那么报文将被转发，否则，报文将被丢弃。

路由表中的数据一般是以条目形式存在的。一个典型的路由表条目通常包含以下主要的条目项。

（1）目的IP地址：此字段表示目标的IP地址。这个IP地址可以是某主机的地址，也可以是一个网络地址。如果这个条目包含的是一个主机地址，那么它的主机ID将被标记为非零；如果这个条目包含的是一个网络地址，那么它的主机ID将被标记为零。

（2）下一个路由器的IP地址：这里采用“下一个”的说法，是因为下一个路由器并不总是最终的目的路由器，它很可能是一个中间路由器。条目给出的下一个路由器的地址用来转发在相应接口接收到的IP数据报文。

（3）标志：这个字段提供了另一组重要信息，例如，目的IP地址是一个主机地址还是一个网络地址。此外，从标志中可以得知下一个路由器是一个真实路由器还是一个直接相连的接口。

（4）网络接口规范：为一些数据报文的网络接口规范，该规范将与报文一起被转发。

###  Docker的网络实现

标准的Docker支持以下4类网络模式。

◎ host模式：使用--net=host指定。

◎ container模式：使用--net=container:NAME_or_ID指定。

◎ none模式：使用--net=none指定。

◎ bridge模式：使用--net=bridge指定，为默认设置。

### Kubernetes的网络实现

在实际的业务场景中，业务组件之间的关系十分复杂，特别是随着微服务理念逐步深入人心，应用部署的粒度更加细小和灵活。为了支持业务应用组件的通信，Kubernetes网络的设计主要致力于解决以下问题。

（1）容器到容器之间的直接通信。

（2）抽象的Pod到Pod之间的通信。

（3）Pod到Service之间的通信。

（4）集群外部与内部组件之间的通信。

#### 容器到容器的通信

同一个Pod内的容器（Pod内的容器是不会跨宿主机的）共享同一个网络命名空间，共享同一个Linux协议栈。所以对于网络的各类操作，就和它们在同一台机器上一样，它们甚至可以用localhost地址访问彼此的端口。

#### Pod之间的通信

们看了同一个Pod内的容器之间的通信情况，再看看Pod之间的通信情况。每一个Pod都有一个真实的全局IP地址，同一个Node内的不同Pod之间可以直接采用对方Pod的IP地址通信，而且不需要采用其他发现机制，例如DNS、Consul或者etcd。Pod容器既有可能在同一个Node上运行，也有可能在不同的Node上运行，所以通信也分为两类：同一个Node内Pod之间的通信和不同Node上Pod之间的通信。

### CNM模型

CNM模型是由Docker公司提出的容器网络模型，现在已经被Cisco Contiv、Kuryr、OpenVirtual Networking（OVN）、Project Calico、VMware、Weave和Plumgrid等项目所采纳。另外，Weave、Project Calico、Kuryr和Plumgrid等项目也为CNM提供了网络插件的具体实现。

![image-20200325162939128](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325162939128.png)

◎ Network Sandbox：容器内部的网络栈，包括网络接口、路由表、DNS等配置的管理。Sandbox可用Linux网络命名空间、FreeBSD Jail等机制进行实现。一个Sandbox可以包含多个Endpoint。

◎ Endpoint：用于将容器内的Sandbox与外部网络相连的网络接口。可以使用veth对、Open vSwitch的内部port等技术进行实现。一个Endpoint仅能够加入一个Network。

◎ Network：可以直接互连的Endpoint的集合。可以通过Linux网桥、VLAN等技术进行实现。一个Network包含多个Endpoint。

### CNI模型

CNI是由CoreOS公司提出的另一种容器网络规范，现在已经被Kubernetes、rkt、ApacheMesos、Cloud Foundry和Kurma等项目采纳。另外，Contiv Networking, Project Calico、Weave、SR-IOV、Cilium、Infoblox、Multus、Romana、Plumgrid和Midokura等项目也为CNI提供网络插件的具体实现。图7.18描述了容器运行环境与各种网络插件通过CNI进行连接的模型。

![image-20200325163104385](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325163104385.png)

CNI定义的是容器运行环境与网络插件之间的简单接口规范，通过一个JSON Schema定义CNI插件提供的输入和输出参数。一个容器可以通过绑定多个网络插件加入多个网络中。

CNI提供了一种应用容器的插件化网络解决方案，定义对容器网络进行操作和配置的规范，通过插件的形式对CNI接口进行实现。CNI是由rkt Networking Proposal发展而来的，试图提供一种普适的容器网络解决方案。CNI仅关注在创建容器时分配网络资源，和在销毁容器时删除网络资源，这使得CNI规范非常轻巧、易于实现，得到了广泛的支持。

CNI模型中只涉及两个概念：容器和网络。

◎ 容器（Container）：是拥有独立Linux网络命名空间的环境，例如使用Docker或rkt创建的容器。关键之处是容器需要拥有自己的Linux网络命名空间，这是加入网络的必要条件。

◎ 网络（Network）：表示可以互连的一组实体，这些实体拥有各自独立、唯一的IP地址，可以是容器、物理机或者其他网络设备（比如路由器）等。

### Kubernetes网络策略

为了实现细粒度的容器间网络访问隔离策略，Kubernetes从1.3版本开始，由SIG-Network小组主导研发了Network Policy机制，目前已升级为networking.k8s.io/v1稳定版本。NetworkPolicy的主要功能是对Pod间的网络通信进行限制和准入控制，设置方式为将Pod的Label作为查询条件，设置允许访问或禁止访问的客户端Pod列表。目前查询条件可以作用于Pod和Namespace级别。

为了使用Network Policy，Kubernetes引入了一个新的资源对象NetworkPolicy，供用户设置Pod间网络访问的策略。但仅定义一个网络策略是无法完成实际的网络隔离的，还需要一个策略控制器（Policy Controller）进行策略的实现。策略控制器由第三方网络组件提供，目前Calico、Cilium、Kube-router、Romana、Weave Net等开源项目均支持网络策略的实现。

Network Policy的工作原理如图7.19所示，policy controller需要实现一个API Listener，监听用户设置的NetworkPolicy定义，并将网络访问规则通过各Node的Agent进行实际设置（Agent则需要通过CNI网络插件实现）。

![image-20200325163207184](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325163207184.png)

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

主要的参数说明如下。

◎ podSelector ：用于定义该网络策略作用的Pod范围，本例的选择条件为包含“role=db”标签的Pod。

◎ policyTypes：网络策略的类型，包括ingress和egress两种，用于设置目标Pod的入站和出站的网络限制。

◎ ingress：定义允许访问目标Pod的入站白名单规则，满足from条件的客户端才能访问ports定义的目标Pod端口号。

- from：对符合条件的客户端Pod进行网络放行，规则包括基于客户端Pod的Label、基于客户端Pod所在的Namespace的Label或者客户端的IP范围。
- ports：允许访问的目标Pod监听的端口号。

◎ egress：定义目标Pod允许访问的“出站”白名单规则，目标Pod仅允许访问满足to条件的服务端IP范围和ports定义的端口号。

- to：允许访问的服务端信息，可以基于服务端Pod的Label、基于服务端Pod所在的Namespace的Label或者服务端IP范围。
- - ports：允许访问的服务端的端口号。

在Namespace级别还可以设置一些默认的全局网络策略，以方便管理员对整个Namespace进行统一的网络策略设置。

默认禁止任何客户端访问该Namespace中的所有Pod

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

默认允许任何客户端访问该Namespace中的所有Pod

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all
spec:
  podSelector: {}
  ingress:
  - {}
```

默认禁止任何客户端访问该Namespace中的所有Pod，同时禁止访问外部服务：

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: default-deny
spec:
 podSelector: {}
 policyTypes:
 - Egress
```

默认允许该Namespace中的所有Pod访问外部服务：

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: allow-all
spec:
 podSelector: {}
 egress:
 - {}
 policyTypes:
 - Egress
```

### 开源的网络组件

Kubernetes的网络模型假定了所有Pod都在一个可以直接连通的扁平网络空间中。这在GCE里面是现成的网络模型，Kubernetes假定这个网络已经存在。而在私有云里搭建Kubernetes集群，就不能假定这种网络已经存在了。我们需要自己实现这个网络假设，将不同节点上的Docker容器之间的互相访问先打通，然后运行Kubernetes。

目前已经有多个开源组件支持容器网络模型。介绍几个常见的网络组件及其安装配置方法，包括Flannel、Open vSwitch、直接路由和Calico。

#### Flannel

Flannel之所以可以搭建Kubernetes依赖的底层网络，是因为它能实现以下两点。

（1）它能协助Kubernetes，给每一个Node上的Docker容器都分配互相不冲突的IP地址。

（2）它能在这些IP地址之间建立一个覆盖网络（Overlay Network），通过这个覆盖网络，将数据包原封不动地传递到目标容器内。

#### Open vSwitch

Open vSwitch是一个开源的虚拟交换机软件，有点儿像Linux中的bridge，但是功能要复杂得多。Open vSwitch的网桥可以直接建立多种通信通道（隧道），例如Open vSwitch withGRE/VxLAN。这些通道的建立可以很容易地通过OVS的配置命令实现。在Kubernetes、Docker场景下，我们主要是建立L3到L3的隧道。举个例子来看看Open vSwitch withGRE/VxLAN的网络架构.

![image-20200325163825529](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325163825529.png)

#### 直接路由

我们可以通过部署MultiLayer Switch（MLS）来实现这一点，在MLS中配置每个docker0子网地址到Node地址的路由项，通过MLS将docker0的IP寻址定向到对应的Node上。

另外，我们可以将这些docker0和Node的匹配关系配置在Linux操作系统的路由项中，这样通信发起的Node就能够根据这些路由信息直接找到目标Pod所在的Node，将数据传输过去。如图7.23所示。

![image-20200325163858831](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325163858831.png)

#### Calico容器

Calico是一个基于BGP的纯三层的网络方案，与OpenStack、Kubernetes、AWS、GCE等云平台都能够良好地集成。Calico在每个计算节点都利用Linux Kernel实现了一个高效的vRouter来负责数据转发。每个vRouter都通过BGP1协议把在本节点上运行的容器的路由信息向整个Calico网络广播，并自动设置到达其他节点的路由转发规则。Calico保证所有容器之间的数据流量都是通过IP路由的方式完成互联互通的。Calico节点组网时可以直接利用数据中心的网络结构（L2或者L3），不需要额外的NAT、隧道或者Overlay Network，没有额外的封包解包，能够节约CPU运算，提高网络效率，如图7.24所示

![image-20200325163935565](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325163935565.png)

## 共享存储原理

Kubernetes对于有状态的容器应用或者对数据需要持久化的应用，不仅需要将容器内的目录挂载到宿主机的目录或者emptyDir临时存储卷，而且需要更加可靠的存储来保存应用产生的重要数据，以便容器应用在重建之后仍然可以使用之前的数据。不过，存储资源和计算资源（CPU/内存）的管理方式完全不同。为了能够屏蔽底层存储实现的细节，让用户方便使用，同时让管理员方便管理，Kubernetes从1.0版本就引入PersistentVolume（PV）和PersistentVolumeClaim（PVC）两个资源对象来实现对存储的管理子系统。

PV是对底层网络共享存储的抽象，将共享存储定义为一种“资源”，比如Node也是一种容器应用可以“消费”的资源。PV由管理员创建和配置，它与共享存储的具体实现直接相关，例如GlusterFS、iSCSI、RBD或GCE或AWS公有云提供的共享存储，通过插件式的机制完成与共享存储的对接，以供应用访问和使用。

PVC则是用户对存储资源的一个“申请”。就像Pod“消费”Node的资源一样，PVC能够“消费”PV资源。PVC可以申请特定的存储空间和访问模式。

### PV

PV作为存储资源，主要包括存储能力、访问模式、存储类型、回收策略、后端存储类型等关键信息的设置。

Kubernetes支持的PV类型如下。

◎ AWSElasticBlockStore：AWS公有云提供的ElasticBlockStore。

◎ AzureFile：Azure公有云提供的File。

◎ AzureDisk：Azure公有云提供的Disk。

◎ CephFS：一种开源共享存储系统。

◎ FC（Fibre Channel）：光纤存储设备。

◎ FlexVolume：一种插件式的存储机制。

◎ Flocker：一种开源共享存储系统。

◎ GCEPersistentDisk：GCE公有云提供的PersistentDisk。

◎ Glusterfs：一种开源共享存储系统。

◎ HostPath：宿主机目录，仅用于单机测试。

◎ iSCSI：iSCSI存储设备。

◎ Local：本地存储设备，从Kubernetes 1.7版本引入，到1.14版本时更新为稳定版，目前可以通过指定块（Block）设备提供Local PV，或通过社区开发的sig-storage-local-static-provisioner插件（https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner）来管理Local PV的生命周期。

◎ NFS：网络文件系统。

◎ Portworx Volumes：Portworx提供的存储服务。

◎ Quobyte Volumes：Quobyte提供的存储服务。

◎ RBD（Ceph Block Device）：Ceph块存储。

◎ ScaleIO Volumes：DellEMC的存储设备。

◎ StorageOS：StorageOS提供的存储服务。

◎ VsphereVolume：VMWare提供的存储系统。

PV的关键配置参数.

1．存储能力（Capacity）

2．存储卷模式（Volume Mode）

3．访问模式（Access Modes）对PV进行访问模式的设置，用于描述用户的应用对存储资源的访问权限。访问模式如下。

◎ ReadWriteOnce（RWO）：读写权限，并且只能被单个Node挂载。

◎ ReadOnlyMany（ROX）：只读权限，允许被多个Node挂载。

◎ ReadWriteMany（RWX）：读写权限，允许被多个Node挂载。

4．存储类别（Class）

5．回收策略（Reclaim Policy）通过PV定义中的persistentVolumeReclaimPolicy字段进行设置，可选项如下。

◎ 保留：保留数据，需要手工处理。

◎ 回收空间：简单清除文件的操作（例如执行rm -rf /thevolume/*命令）。

◎ 删除：与PV相连的后端存储完成Volume的删除操作（如AWS EBS、GCE PD、AzureDisk、OpenStack Cinder等设备的内部Volume清理）。

6．挂载参数（Mount Options）

在将PV挂载到一个Node上时，根据后端存储的特点，可能需要设置额外的挂载参数，可以根据PV定义中的mountOptions字段进行设置。

7．节点亲和性（Node Affinity）

PV可以设置节点亲和性来限制只能通过某些Node访问Volume，可以在PV定义中的nodeAffinity字段进行设置。使用这些Volume的Pod将被调度到满足条件的Node上。

PV生命周期的各个阶段某个PV在生命周期中可能处于以下4个阶段（Phaes）之一。

◎ Available：可用状态，还未与某个PVC绑定。

◎ Bound：已与某个PVC绑定。

◎ Released：绑定的PVC已经删除，资源已释放，但没有被集群回收。

◎ Failed：自动资源回收失败。

### PVC

PVC作为用户对存储资源的需求申请，主要包括存储空间请求、访问模式、PV选择条件和存储类别等信息的设置。

PVC的关键配置参数说明如下。

◎ 资源请求（Resources）：描述对存储资源的请求，目前仅支持request.storage的设置，即存储空间大小。

◎ 访问模式（Access Modes）：PVC也可以设置访问模式，用于描述用户应用对存储资源的访问权限。其三种访问模式的设置与PV的设置相同。

◎ 存储卷模式（Volume Modes）：PVC也可以设置存储卷模式，用于描述希望使用的PV存储卷模式，包括文件系统和块设备。

◎ PV选择条件（Selector）：通过对Label Selector的设置，可使PVC对于系统中已存在的各种PV进行筛选。系统将根据标签选出合适的PV与该PVC进行绑定。选择条件可以使用matchLabels和matchExpressions进行设置，如果两个字段都设置了，则Selector的逻辑将是两组条件同时满足才能完成匹配。

◎ 存储类别（Class）： PVC在定义时可以设定需要的后端存储的类别（通过storageClassName字段指定），以减少对后端存储特性的详细信息的依赖。只有设置了该Class的PV才能被系统选出，并与该PVC进行绑定。

### PV和PVC的生命周期

我们可以将PV看作可用的存储资源，PVC则是对存储资源的需求，PV和PVC的相互关系遵循如图8.1所示的生命周期。

![image-20200325164324974](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325164324974.png)

#### 资源供应

Kubernetes支持两种资源的供应模式：静态模式（Static）和动态模式（Dynamic）。资源供应的结果就是创建好的PV。

◎ 静态模式：集群管理员手工创建许多PV，在定义PV时需要将后端存储的特性进行设置。

◎ 动态模式：集群管理员无须手工创建PV，而是通过StorageClass的设置对后端存储进行描述，标记为某种类型。此时要求PVC对存储的类型进行声明，系统将自动完成PV的创建及与PVC的绑定。PVC可以声明Class为""，说明该PVC禁止使用动态模式。

#### 资源绑定

在用户定义好PVC之后，系统将根据PVC对存储资源的请求（存储空间和访问模式）在已存在的PV中选择一个满足PVC要求的PV，一旦找到，就将该PV与用户定义的PVC进行绑定，用户的应用就可以使用这个PVC了。如果在系统中没有满足PVC要求的PV，PVC则会无限期处于Pending状态，直到等到系统管理员创建了一个符合其要求的PV。PV一旦绑定到某个PVC上，就会被这个PVC独占，不能再与其他PVC进行绑定了。在这种情况下，当PVC申请的存储空间比PV的少时，整个PV的空间就都能够为PVC所用，可能会造成资源的浪费。如果资源供应使用的是动态模式，则系统在为PVC找到合适的StorageClass后，将自动创建一个PV并完成与PVC的绑定。

#### 资源使用

Pod使用Volume的定义，将PVC挂载到容器内的某个路径进行使用。Volume的类型为persistentVolumeClaim，在后面的示例中再进行详细说明。在容器应用挂载了一个PVC后，就能被持续独占使用。不过，多个Pod可以挂载同一个PVC，应用程序需要考虑多个实例共同访问一块存储空间的问题。

#### 资源释放

当用户对存储资源使用完毕后，用户可以删除PVC，与该PVC绑定的PV将会被标记为“已释放”，但还不能立刻与其他PVC进行绑定。通过之前PVC写入的数据可能还被留在存储设备上，只有在清除之后该PV才能再次使用。

#### 资源回收

对于PV，管理员可以设定回收策略，用于设置与之绑定的PVC释放资源之后如何处理遗留数据的问题。只有PV的存储空间完成回收，才能供新的PVC绑定和使用。

### StorageClass

StorageClass作为对存储资源的抽象定义，对用户设置的PVC申请屏蔽后端存储的细节，一方面减少了用户对于存储资源细节的关注，另一方面减轻了管理员手工管理PV的工作，由系统自动完成PV的创建和绑定，实现了动态的资源供应。基于StorageClass的动态资源供应模式将逐步成为云平台的标准存储配置模式。

StorageClass的关键配置参数。

1．提供者（Provisioner）

2．参数（Parameters）

后端存储资源提供者的参数设置，不同的Provisioner包括不同的参数设置。某些参数可以不显示设定，Provisioner将使用其默认值。

### CSI存储机制

Kubernetes从1.9版本开始引入容器存储接口Container Storage Interface（CSI）机制，用于在Kubernetes和外部存储系统之间建立一套标准的存储管理接口，通过该接口为容器提供存储服务。CSI到Kubernetes 1.10版本升级为Beta版，到Kubernetes 1.13版本升级为GA版，已逐渐成熟。

Kubernetes通过PV、PVC、Storageclass已经提供了一种强大的基于插件的存储管理机制，但是各种存储插件提供的存储服务都是基于一种被称为“in-true”（树内）的方式提供的，这要求存储插件的代码必须被放进Kubernetes的主干代码库中才能被Kubernetes调用，属于紧耦合的开发模式。这种“in-tree”方式会带来一些问题：

◎ 存储插件的代码需要与Kubernetes的代码放在同一代码库中，并与Kubernetes的二进制文件共同发布；

◎ 存储插件代码的开发者必须遵循Kubernetes的代码开发规范；

◎ 存储插件代码的开发者必须遵循Kubernetes的发布流程，包括添加对Kubernetes存储系统的支持和错误修复；

◎ Kubernetes社区需要对存储插件的代码进行维护，包括审核、测试等工作；

◎ 存储插件代码中的问题可能会影响Kubernetes组件的运行，并且很难排查问题；

◎ 存储插件代码与Kubernetes的核心组件（kubelet和kube-controller-manager）享有相同的系统特权权限，可能存在可靠性和安全性问题。

基于以上这些问题和考虑，Kubernetes逐步推出与容器对接的存储接口标准，存储提供方只需要基于标准接口进行存储插件的实现，就能使用Kubernetes的原生存储机制为容器提供存储服务。这套标准被称为CSI（容器存储接口）。在CSI成为Kubernetes的存储供应标准之后，存储提供方的代码就能和Kubernetes代码彻底解耦，部署也与Kubernetes核心组件分离，显然，存储插件的开发由提供方自行维护，就能为Kubernetes用户提供更多的存储功能，也更加安全可靠。基于CSI的存储插件机制也被称为“out-of-tree”（树外）的服务提供方式，是未来Kubernetes第三方存储插件的标准方案。

图8.5描述了Kubernetes CSI存储插件的关键组件和推荐的容器化部署架构。

![image-20200325164611209](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325164611209.png)

其中主要包括两种组件：CSI Controller和CSI Node。

1．CSI ControllerCSI Controller的主要功能是提供存储服务视角对存储资源和存储卷进行管理和操作。在Kubernetes中建议将其部署为单实例Pod，可以使用StatefulSet或Deployment控制器进行部署，设置副本数量为1，保证为一种存储插件只运行一个控制器实例。

2．CSI NodeCSI Node的主要功能是对主机（Node）上的Volume进行管理和操作。在Kubernetes中建议将其部署为DaemonSet，在每个Node上都运行一个Pod。

## Kubernetes集群管理

###  Node的管理

在Kubernetes集群中，一个新Node的加入是非常简单的。在新的Node上安装Docker、kubelet和kube-proxy服务，然后配置kubelet和kube-proxy的启动参数，将Master URL指定为当前Kubernetes集群Master的地址，最后启动这些服务。通过kubelet默认的自动注册机制，新的Node将会自动加入现有的Kubernetes集群中，如图10.1所示。

![image-20200325164912822](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325164912822.png)

### 更新资源对象的Label

Label是用户可灵活定义的对象属性，对于正在运行的资源对象，我们随时可以通过kubectllabel命令进行增加、修改、删除等操作。

### Namespace：集群环境共享与隔离

在一个组织内部，不同的工作组可以在同一个Kubernetes集群中工作，Kubernetes通过命名空间和Context的设置对不同的工作组进行区分，使得它们既可以共享同一个Kubernetes集群的服务，也能够互不干扰，如图10.2所示。

![image-20200325164942014](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325164942014.png)

### Kubernetes资源管理

为了避免系统挂掉，该Node会选择“清理”某些Pod来释放资源，此时每个Pod都可能成为牺牲品。但有些Pod担负着更重要的职责，比其他Pod更重要，比如与数据存储相关的、与登录相关的、与查询余额相关的，即使系统资源严重不足，也需要保障这些Pod的存活，Kubernetes中该保障机制的核心如下。

◎ 通过资源限额来确保不同的Pod只能占用指定的资源。

◎ 允许集群的资源被超额分配，以提高集群的资源利用率。

◎ 为Pod划分等级，确保不同等级的Pod有不同的服务质量（QoS），资源不足时，低等级的Pod会被清理，以确保高等级的Pod稳定运行。

### 资源紧缺时的Pod驱逐机制

#### 驱逐策略

kubelet持续监控主机的资源使用情况，并尽量防止计算资源被耗尽。一旦出现资源紧缺的迹象，kubelet就会主动终止一个或多个Pod的运行，以回收紧缺的资源。当一个Pod被终止时，其中的容器会全部停止，Pod的状态会被设置为Failed。

#### 驱逐信号

在表10.6中提到了一些信号，kubelet能够利用这些信号作为决策依据来触发驱逐行为。其中，描述列中的内容来自kubelet Summary API；每个信号都支持整数值或者百分比的表示方法，百分比的分母部分就是各个信号相关资源的总量。

![image-20200325165153479](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325165153479.png)

### Pod Disruption Budget（主动驱逐保护）

在Kubernetes集群运行的过程中，许多管理操作都可能对Pod进行主动驱逐，“主动”一词意味着这一操作可以安全地延迟一段时间，目前主要针对以下两种场景。

◎ 节点的维护或升级时（kubectl drain）。

◎ 对应用的自动缩容操作（autoscaling down）。

###  Kubernetes集群的高可用部署方案

Kubernetes作为容器应用的管理平台，通过对Pod的运行状况进行监控，并且根据主机或容器失效的状态将新的Pod调度到其他Node上，实现了应用层的高可用性。针对Kubernetes集群，高可用性还应包含以下两个层面的考虑：etcd数据存储的高可用性和Kubernetes Master组件的高可用性。

#### 手工方式的高可用部署方案

1．etcd的高可用部署

2．Master的高可用部署

### 使用kubeadm的高可用部署方案

kubeadm提供了两种不同的高可用方案。

（1）堆叠方案：etcd服务和控制平面被部署在同样的节点中，对基础设施的要求较低，对故障的应对能力也较低，如图10.7所示。

![image-20200325165344115](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325165344115.png)

（2）外置etcd方案：etcd和控制平面被分离，需要更多的硬件，也有更好的保障能力，如图10.8所示。

![image-20200325165355340](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325165355340.png)

### Kubernetes集群监控

Kubernetes的早期版本依靠Heapster来实现完整的性能数据采集和监控功能，Kubernetes从1.8版本开始，性能数据开始以Metrics API的方式提供标准化接口，并且从1.10版本开始将Heapster替换为Metrics Server。在Kubernetes新的监控体系中，Metrics Server用于提供核心指标（Core Metrics），包括Node、Pod的CPU和内存使用指标。对其他自定义指标（Custom Metrics）的监控则由Prometheus等组件来完成。

通过Metrics Server监控

Prometheus+Grafana集群性能监控平台搭建

### 集群统一日志管理

在Kubernetes集群环境中，一个完整的应用或服务都会涉及为数众多的组件运行，各组件所在的Node及实例数量都是可变的。日志子系统如果不做集中化管理，则会给系统的运维支撑造成很大的困难，因此有必要在集群层面对日志进行统一收集和检索等工作。

在容器中输出到控制台的日志，都会以*-json.log的命名方式保存在/var/lib/docker/containers/目录下，这就为日志采集和后续处理奠定了基础。Kubernetes推荐采用Fluentd+Elasticsearch+Kibana完成对系统和容器日志的采集、查询和展现工作。

系统部署架构

![image-20200325165536632](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200325165536632.png)

### 使用Web UI（Dashboard）管理集群

Kubernetes的Web UI网页管理工具kubernetes-dashboard可提供部署应用、资源对象管理、容器日志查询、系统监控等常用的集群管理功能。为了在页面上显示系统资源的使用情况，要求部署Metrics Server，详见10.8节的说明。可通过https://rawgit.com/kubernetes/dashboard/master/src/deploy/kubernetes-dashboard.yaml页面下载部署kubernetes-dashboard的YAML配置文件。

### Helm：Kubernetes应用包管理工具

随着容器技术逐渐被企业接受，在Kubernetes上已经能便捷地部署简单的应用了。但对于复杂的应用中间件，在Kubernetes上进行容器化部署并非易事，通常需要先研究Docker镜像的运行需求、环境变量等内容，并能为这些容器定制存储、网络等设置，最后设计和编写Deployment、ConfigMap、Service及Ingress等相关YAML配置文件，再提交给Kubernetes部署。这些复杂的过程将逐步被Helm应用包管理工具实现。

Helm是一个由CNCF孵化和管理的项目，用于对需要在Kubernetes上部署的复杂应用进行定义、安装和更新。Helm以Chart的方式对应用软件进行描述，可以方便地创建、版本化、共享和发布复杂的应用软件。

Helm的主要概念如下。

◎ Chart：一个Helm包，其中包含运行一个应用所需要的工具和资源定义，还可能包含Kubernetes集群中的服务定义，类似于Homebrew中的formula、APT中的dpkg或者Yum中的RPM文件。

◎ Release：在Kubernetes集群上运行的一个Chart实例。在同一个集群上，一个Chart可以被安装多次。例如有一个MySQL Chart，如果想在服务器上运行两个MySQL数据库，就可以基于这个Chart安装两次。每次安装都会生成新的Release，会有独立的Release名称。

◎ Repository：用于存放和共享Chart仓库。简单来说，Helm整个系统的主要任务就是，在仓库中查找需要的Chart，然后将Chart以Release的形式安装到Kubernetes集群中。