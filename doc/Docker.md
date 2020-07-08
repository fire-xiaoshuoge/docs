# Docker

## Docker

### 云计算架构分层

一般来说，大家比较公认的云架构是划分为基础设施层、平台层和软件服务层三个层次的。对应名称为IaaS，PaaS和SaaS。IaaS, Infrastructure as a Service，中文名为基础设施即服务。

![img](https://bkimg.cdn.bcebos.com/pic/f9198618367adab47472e22c8fd4b31c8701e43b?x-bce-process=image/watermark,g_7,image_d2F0ZXIvYmFpa2UxMTY=,xp_5,yp_5)

IaaS主要包括计算机服务器、通信设备、存储设备等，能够按需向用户提供的计算能力、存储能力或网络能力等IT基础设施类服务，也就是能在基础设施层面提供的服务。IaaS能够得到成熟应用的核心在于虚拟化技术，通过虚拟化技术可以将形形色色计算设备统一虚拟化为虚拟资源池中的计算资源，将存储设备统一虚拟化为虚拟资源池中的存储资源，将网络设备统一虚拟化为虚拟资源池中的网络资源。当用户订购这些资源时，数据中心管理者直接将订购的份额打包提供给用户，从而实现了IaaS。

PaaS, Platform as a Service，中文名为平台即服务。如果以传统计算机架构中“硬件+操作系统/开发工具+应用软件”的观点来看待，那么云计算的平台层应该提供类似操作系统和开发工具的功能。实际上也的确如此，PaaS定位于通过互联网为用户提供一整套开发、运行和运营应用软件的支撑平台。就像在个人计算机软件开发模式下，程序员可能会在一台装有Windows或Linux操作系统的计算机上使用开发工具开发并部署应用软件一样。微软公司的Windows Azure和谷歌公司的GAE，可以算是PaaS平台中最为知名的两个产品了。

SaaS，软件即服务。简单地说，就是一种通过互联网提供软件服务的软件应用模式。在这种模式下，用户不需要再花费大量投资用于硬件、软件和开发团队的建设，只需要支付一定的租赁费用，就可以通过互联网享受到相应的服务，而且整个系统的维护也由厂商负责。

### Docker

Docker 是一个[开源](https://baike.baidu.com/item/开源/246339)的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 [Linux](https://baike.baidu.com/item/Linux)或Windows 机器上，也可以实现[虚拟化](https://baike.baidu.com/item/虚拟化/547949)。容器是完全使用[沙箱](https://baike.baidu.com/item/沙箱/393318)机制，相互之间不会有任何接口。

### Docker的架构

![img](https://www.runoob.com/wp-content/uploads/2016/04/576507-docker1.png)



![image-20200307183513816](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200307183513816.png)

### Docker优势

对开发和运维（DevOps）人员来说，最梦寐以求的效果可能就是一次创建或配置，之后可以在任意地方、任意时间让应用正常运行，而Docker恰恰是可以实现这一终极目标的“瑞士军刀”。具体说来，在开发和运维过程中，Docker具有如下几个方面的优势：

❑ 更快速的交付和部署。使用Docker，开发人员可以使用镜像来快速构建一套标准的开发环境；开发完成之后，测试和运维人员可以直接使用完全相同的环境来部署代码。只要是开发测试过的代码，就可以确保在生产环境无缝运行。Docker可以快速创建和删除容器，实现快速迭代，节约开发、测试、部署的大量时间。并且，整个过程全程可见，使团队更容易理解应用的创建和工作过程。

❑ 更高效的资源利用。运行Docker容器不需要额外的虚拟化管理程序（Virtual MachineManager, VMM，以及Hypervisor）的支持，Docker是内核级的虚拟化，可以实现更高的性能，同时对资源的额外需求很低。与传统虚拟机方式相比，Docker的性能要提高1～2个数量级。

❑ 更轻松的迁移和扩展。Docker容器几乎可以在任意的平台上运行，包括物理机、虚拟机、公有云、私有云、个人电脑、服务器等，同时支持主流的操作系统发行版本。这种兼容性让用户可以在不同平台之间轻松地迁移应用。

❑ 更简单的更新管理。使用Dockerfile，只需要小小的配置修改，就可以替代以往大量的更新工作。所有修改都以增量的方式被分发和更新，从而实现自动化并且高效的容器管理。

![image-20200326023417919](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326023417919.png)

![image-20200517084326485](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200517084326485.png)

## 核心概念

### Docker引擎

Docker仓库类似于代码仓库，是Docker集中存放镜像文件的场所。

根据所存储的镜像公开分享与否，Docker仓库可以分为公开仓库（Public）和私有仓库（Private）两种形式。

### Docker镜像

Docker镜像类似于虚拟机镜像，可以将它理解为一个只读的模板。

镜像是创建Docker容器的基础。通过版本管理和增量的文件系统，Docker提供了一套十分简单的机制来创建和更新现有的镜像，用户甚至可以从网上下载一个已经做好的应用镜像，并直接使用。

### Docker容器

Docker容器类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。

容器是从镜像创建的应用运行实例。它可以启动、开始、停止、删除，而这些容器都是彼此相互隔离、互不可见的。

## 使用Docker镜像

### 获取镜像

可以使用docker [image] pull命令直接从Docker Hub镜像源来下载镜像。该命令的格式为docker [image] pull NAME[:TAG]。

其中，NAME是镜像仓库名称（用来区分镜像）, TAG是镜像的标签（往往用来表示版本信息）。通常情况下，描述一个镜像需要包括“名称+标签”信息。

### 查看镜像信息

1．使用images命令列出镜像

使用docker images或docker image ls命令可以列出本地主机上已有镜像的基本信息。

images子命令主要支持如下选项，用户可以自行进行尝试：

❑ -a, --all=true|false：列出所有（包括临时文件）镜像文件，默认为否；

❑ --digests=true|false：列出镜像的数字摘要值，默认为否；

❑ -f, --filter=[]：过滤列出的镜像，如dangling=true只显示没有被使用的镜像；也可指定带有特定标注的镜像等；

❑ --format="TEMPLATE"：控制输出格式，如．ID代表ID信息，.Repository代表仓库信息等；

❑ --no-trunc=true|false：对输出结果中太长的部分是否进行截断，如镜像的ID信息，默认为是；

❑ -q, --quiet=true|false：仅输出ID信息，默认为否。

2．使用tag命令添加镜像标签

为了方便在后续工作中使用特定镜像，还可以使用docker tag命令来为本地镜像任意添加新的标签。

3．使用inspect命令查看详细信息

使用docker[image]inspect命令可以获取该镜像的详细信息，包括制作者、适应架构、各层的数字摘要等。

4．使用history命令查看镜像历史

既然镜像文件由多个层组成，那么怎么知道各个层的内容具体是什么呢？这时候可以使用history子命令，该命令将列出各层的创建信息。

### 搜寻镜像

本节主要介绍Docker镜像的search子命令。使用docker search命令可以搜索Docker Hub官方仓库中的镜像。语法为docker search [option] keyword。支持的命令选项主要包括：

❑ -f, --filter filter：过滤输出内容；

❑ --format string：格式化输出内容；

❑ --limit int：限制输出结果个数，默认为25个；

❑ --no-trunc：不截断输出结果。

### 删除和清理镜像

1．使用标签删除镜像

使用docker rmi或docker image rm命令可以删除镜像，命令格式为docker rmi IMAGE[IMAGE...]，其中IMAGE可以为标签或ID。

支持选项包括：❑ -f, -force：强制删除镜像，即使有容器依赖它；❑ -no-prune：不要清理未带标签的父镜像。

2．使用镜像ID来删除镜像

当使用docker rmi命令，并且后面跟上镜像的ID（也可以是能进行区分的部分ID串前缀）时，会先尝试删除所有指向该镜像的标签，然后删除该镜像文件本身。

3．清理镜像

使用Docker一段时间后，系统中可能会遗留一些临时的镜像文件，以及一些没有被使用的镜像，可以通过docker image prune命令来进行清理。

支持选项包括：❑ -a, -all：删除所有无用镜像，不光是临时镜像；❑ -filter filter：只清理符合给定过滤器的镜像；❑ -f, -force：强制删除镜像，而不进行提示确认。

### 创建镜像

1．基于已有容器创建

该方法主要是使用docker [container] commit命令。

命令格式为docker [container] commit [OPTIONS] CONTAINER [REPOSITORY [:TAG]]，主要选项包括：

❑ -a, --author=""：作者信息；

❑ -c, --change=[]：提交的时候执行Dockerfile指令，包括CMD|ENTRYPOINT|ENV|EXPOSE|LABEL|ONBUILD|USER|VOLUME|WORKDIR等；

❑ -m, --message=""：提交消息；

❑ -p, --pause=true：提交时暂停容器运行。

2．基于本地模板导入

用户也可以直接从一个操作系统模板文件导入一个镜像，主要使用docker [container] import命令。命令格式为docker [image] import [OPTIONS] file|URL|-[REPOSITORY [:TAG]]

3．基于Dockerfile创建

基于Dockerfile创建是最常见的方式。Dockerfile是一个文本文件，利用给定的指令描述基于某个父镜像创建新镜像的过程。

### 存出和载入镜像

1．存出镜像

如果要导出镜像到本地文件，可以使用docker [image] save命令。该命令支持-o、-outputstring参数，导出镜像到指定的文件中。

2．载入镜像

可以使用docker [image] load将导出的tar文件再导入到本地镜像库。支持-i、-input string选项，从指定文件中读入镜像内容。

### 上传镜像

本节主要介绍Docker镜像的push子命令。可以使用docker [image] push命令上传镜像到仓库，默认上传到Docker Hub官方仓库（需要登录）。命令格式为docker [image] pushNAME[:TAG] | [REGISTRY_HOST[:REGISTRY_PORT]/]NAME[:TAG]。

## 操作Docker容器

### 创建容器

1．新建容器

可以使用docker [container] create命令新建一个容器。

![image-20200326024855534](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326024855534.png)

![image-20200326024912984](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326024912984.png)

<img src="C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326024936567.png" alt="image-20200326024936567" style="zoom:150%;" />

2．启动容器

使用docker [container] start命令来启动一个已经创建的容器。

3．新建并启动容器

除了创建容器后通过start命令来启动，也可以直接新建并启动容器。

所需要的命令主要为docker [container] run，等价于先执行docker [container] create命令，再执行docker [container] start命令。

4．守护态运行

更多的时候，需要让D ocker容器在后台以守护态（Daemonized）形式运行。此时，可以通过添加-d参数来实现。

5．查看容器输出

要获取容器的输出信息，可以通过docker [container] logs命令。

该命令支持的选项包括：

❑ -details：打印详细信息；

❑ -f, -follow：持续保持输出；

❑ -since string：输出从某个时间开始的日志；

❑ -tail string：输出最近的若干日志；

❑ -t, -timestamps：显示时间戳信息；

❑ -until string：输出某个时间之前的日志。

### 停止容器

1．暂停容器

可以使用docker [container] pause CONTAINER [CONTAINER...]命令来暂停一个运行中的容器。

2．终止容器

可以使用docker [container] stop来终止一个运行中的容器。该命令的格式为docker[container] stop [-t|--time[=10]] [CONTAINER...]。

### 进入容器

1. attach命令

   attach是Docker自带的命令，命令格式为：

   ![image-20200326025150278](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025150278.png)

   这个命令支持三个主要选项：

   ❑ --detach-keys[=[]]：指定退出attach模式的快捷键序列，默认是CTRL-p CTRL-q；

   ❑ --no-stdin=true|false：是否关闭标准输入，默认是保持打开；

   ❑ --sig-proxy=true|false：是否代理收到的系统信号给应用进程，默认为true。

2. exec命令

该命令的基本格式为：

![image-20200326025221429](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025221429.png)

比较重要的参数有：

❑ -d, --detach：在容器中后台执行命令；

❑ --detach-keys=""：指定将容器切回后台的按键；

❑ -e, --env=[]：指定环境变量列表；

❑ -i, --interactive=true|false：打开标准输入接受用户输入命令，默认值为false；

❑ --privileged=true|false：是否给执行命令以高权限，默认值为false；

❑ -t, --tty=true|false：分配伪终端，默认值为false；

❑ -u, --user=""：执行命令的用户名或ID。

### 删除容器

可以使用docker [container] rm命令来删除处于终止或退出状态的容器，命令格式为docker[container] rm [-f|--force] [-l|--link] [-v|--volumes] CONTAINER [CONTAINER...]。

主要支持的选项包括：❑ -f, --force=false：是否强行终止并删除一个运行中的容器；❑ -l, --link=false：删除容器的连接，但保留容器；❑ -v, --volumes=false：删除容器挂载的数据卷。

### 导入和导出容器

1．导出容器

导出容器是指，导出一个已经创建的容器到一个文件，不管此时这个容器是否处于运行状态。可以使用docker [container] export命令，该命令格式为：

![image-20200326025311312](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025311312.png)

2．导入容器

导出的文件又可以使用docker [container] import命令导入变成镜像，该命令格式为：

![image-20200326025327047](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025327047.png)

### 查看容器

1．查看容器详情

查看容器详情可以使用docker container inspect [OPTIONS] CONTAINER [CONTAINER...]子命令。

2．查看容器内进程

查看容器内进程可以使用docker [container] top [OPTIONS] CONTAINER [CONTAINER...]子命令。

3．查看统计信息

查看统计信息可以使用docker [container] stats [OPTIONS] [CONTAINER...]子命令，会显示CPU、内存、存储、网络等使用情况的统计信息。

支持选项包括：❑ -a, -all：输出所有容器统计信息，默认仅在运行中；❑ -format string：格式化输出信息；❑ -no-stream：不持续输出，默认会自动更新持续实时结果；❑ -no-trunc：不截断输出信息。

### 其他容器命令

1．复制文件

container cp命令支持在容器和主机之间复制文件。命令格式为docker [container] cp[OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-。支持的选项包括：

❑ -a, -archive：打包模式，复制文件会带有原始的uid/gid信息；❑ -L, -follow-link：跟随软连接。当原路径为软连接时，默认只复制链接信息，使用该选项会复制链接的目标内容。

2．查看变更

container diff查看容器内文件系统的变更。命令格式为docker [container] diffCONTAINER。

3．查看端口映射

container port命令可以查看容器的端口映射情况。命令格式为docker container portCONTAINER [PRIVATE_PORT[/PROTO]]。

4．更新配置

container update命令可以更新容器的一些运行时配置，主要是一些资源限制份额。命令格式为docker [container] update [OPTIONS] CONTAINER [CONTAINER...]。

支持的选项包括：❑ -blkio-weight uint16：更新块IO限制，10～1000，默认值为0，代表着无限制；❑ -cpu-period int：限制CPU调度器CFS（Completely Fair Scheduler）使用时间，单位为微秒，最小1000；❑ -cpu-quota int：限制CPU调度器CFS配额，单位为微秒，最小1000；❑ -cpu-rt-period int：限制CPU调度器的实时周期，单位为微秒；❑ -cpu-rt-runtime int：限制CPU调度器的实时运行时，单位为微秒；❑ -c, -cpu-shares int：限制CPU使用份额；❑ -cpus decimal：限制CPU个数；❑ -cpuset-cpus string：允许使用的CPU核，如0-3,0,1；❑ -cpuset-mems string：允许使用的内存块，如0-3,0,1；❑ -kernel-memory bytes：限制使用的内核内存；❑ -m, -memory bytes：限制使用的内存；❑ -memory-reservation bytes：内存软限制；❑ -memory-swap bytes：内存加上缓存区的限制，-1表示为对缓冲区无限制；❑ -restart string：容器退出后的重启策略。

## 访问Docker仓库

仓库（Repository）是集中存放镜像的地方，分公共仓库和私有仓库。一个容易与之混淆的概念是注册服务器（Registry）。实际上注册服务器是存放仓库的具体服务器，一个注册服务器上可以有多个仓库，而每个仓库下面可以有多个镜像。从这方面来说，可将仓库看做一个具体的项目或目录。例如对于仓库地址private-docker.com/ubuntu来说，private-docker.com是注册服务器地址，ubuntu是仓库名。

### Docker Hub公共镜像市场

目前Docker官方维护了一个公共镜像仓库https://hub.docker.com，其中已经包括超过15000的镜像。大部分镜像需求，都可以通过在Docker Hub中直接下载镜像来实现。

1．登录

可以通过命令行执行docker login命令来输入用户名、密码和邮箱来完成注册和登录。注册成功后，本地用户目录的．dockercfg中将保存用户的认证信息。登录成功的用户可以上传个人制造的镜像。

2．基本操作

用户无需登录即可通过docker search命令来查找官方仓库中的镜像，并利用docker pull命令来将它下载到本地。

3．自动创建

自动创建（Automated Builds）功能对于需要经常升级镜像内程序来说，十分方便。有时候，用户创建了镜像，安装了某个软件，如果软件发布新版本则需要手动更新镜像。

要配置自动创建，包括如下的步骤：

1）创建并登录Docker Hub，以及目标网站；*在目标网站中连接帐户到Docker Hub；

2）在Docker Hub中配置一个“自动创建”；

3）选取一个目标网站中的项目（需要含Dockerfile）和分支；

4）指定Dockerfile的位置，并提交创建。

### 搭建本地私有仓库

1．使用registry镜像创建私有仓库

2．管理私有仓库

## Docker数据管理

### 数据卷

数据卷（Data Volumes）是一个可供容器使用的特殊目录，它将主机操作系统目录直接映射进容器，类似于Linux中的mount行为。

数据卷可以提供很多有用的特性：❑ 数据卷可以在容器之间共享和重用，容器间传递数据将变得高效与方便；❑ 对数据卷内数据的修改会立马生效，无论是容器内操作还是本地操作；❑ 对数据卷的更新不会影响镜像，解耦开应用和数据；❑ 卷会一直存在，直到没有容器使用，可以安全地卸载它。

1．创建数据卷

Docker提供了volume子命令来管理数据卷，如下命令可以快速在本地创建一个数据卷：

![image-20200326025600524](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025600524.png)

2．绑定数据卷

除了使用volume子命令来管理数据卷外，还可以在创建容器时将主机本地的任意路径挂载到容器内作为数据卷，这种形式创建的数据卷称为绑定数据卷。

在用docker [container] run命令的时候，可以使用-mount选项来使用数据卷。

-mount选项支持三种类型的数据卷，包括：❑ volume：普通数据卷，映射到主机/var/lib/docker/volumes路径下；❑ bind：绑定数据卷，映射到主机指定路径下；❑ tmpfs：临时数据卷，只存在于内存中。

### 数据卷容器

如果用户需要在多个容器之间共享一些持续更新的数据，最简单的方式是使用数据卷容器。数据卷容器也是一个容器，但是它的目的是专门提供数据卷给其他容器挂载。

### 利用数据卷容器来迁移数据

可以利用数据卷容器对其中的数据卷进行备份、恢复，以实现数据的迁移。

## 端口映射与容器互联

除了通过网络访问外，Docker还提供了两个很方便的功能来满足服务访问的基本需求：一个是允许映射容器内应用的服务端口到本地宿主主机；另一个是互联机制实现多个容器间通过容器名来快速访问。

### 端口映射实现访问容器

1．从外部访问容器应用

在启动容器的时候，如果不指定对应的参数，在容器外部是无法通过网络来访问容器内的网络应用和服务的。

当容器中运行一些网络应用，要让外部访问这些应用时，可以通过-P或-p参数来指定端口映射。当使用-P（大写的）标记时，Docker会随机映射一个49000～49900的端口到内部容器开放的网络端口：

![image-20200511065303399](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065303399.png)

此时，可以使用docker ps看到，本地主机的49155被映射到了容器的5000端口。访问宿主主机的49155端口即可访问容器内Web应用提供的界面。

同样，可以通过docker logs命令来查看应用的信息。

2．映射所有接口地址

![image-20200511065332362](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065332362.png)

3．映射到指定地址的指定端口

![image-20200511065347831](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065347831.png)

4．映射到指定地址的任意端口

![image-20200511065400589](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065400589.png)

5．查看映射端口配置

使用docker port命令来查看当前映射的端口配置，也可以查看到绑定的地址：

![image-20200511065415648](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065415648.png)

### 互联机制实现便捷互访

容器的互联（linking）是一种让多个容器中应用进行快速交互的方式。它会在源和接收容器之间创建连接关系，接收容器可以通过容器名快速访问到源容器，而不用指定具体的IP地址。

1．自定义容器命名

连接系统依据容器的名称来执行。因此，首先需要定义一个好记的容器名字。

虽然当创建容器的时候，系统默认会分配一个名字，但自定义容器名字有两个好处：

❑自定义的命名比较好记，比如一个Web应用容器，我们可以给它起名叫web，一目了然；

❑当要连接其他容器时，即便重启，也可以使用容器名而不用改变，比如连接web容器到db容器。

![image-20200511065444865](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065444865.png)

2．容器互联

![image-20200511065525609](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065525609.png)

![image-20200511065546381](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065546381.png)

Docker通过两种方式为容器公开连接信息：❑更新环境变量；❑更新/etc/hosts文件。

![image-20200511065607822](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065607822.png)

![image-20200511065618677](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065618677.png)

## Dockerfile创建镜像

### Dockerfile

Dockerfile由一行行命令语句组成，并且支持以#开头的注释行。

一般而言，Dockerfile主体内容分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令。

![image-20200511065657790](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511065657790.png)

### 指令说明

Dockerfile中指令的一般格式为INSTRUCTION arguments，包括“配置指令”（配置镜像信息）和“操作指令”（具体执行操作），参见表8-1。

![image-20200326025800631](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025800631.png)

### 创建镜像

编写完成Dockerfile之后，可以通过docker [image] build命令来创建镜像。

基本的格式为docker build [OPTIONS] PATH | URL | -。

该命令将读取指定路径下（包括子目录）的Dockerfile，并将该路径下所有数据作为上下文（Context）发送给Docker服务端。Docker服务端在校验Dockerfile格式通过后，逐条执行其中定义的指令，碰到ADD、COPY和RUN指令会生成一层新的镜像。最终如果创建镜像成功，会返回最终镜像的ID。

docker [image] build命令支持一系列的选项，可以调整创建镜像过程的行为，参见表8-2。

![image-20200326025856617](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326025856617.png)

## 为镜像添加SSH服务

###  commit命令创建

Docker提供了docker commit命令，支持用户提交自己对制定容器的修改，并生成新的镜像。命令格式为docker commit CONTAINER [REPOSITORY[:TAG]]。

### 使用Dockerfile创建

1、创建工作目录

\# mkdir sshd_ubuntu 

\# ls 

在其中，创建Dockerfile和run.sh文件

\# cd sshd_ubuntu/ 

\# touch Dockerfile run.sh 

\# ls 

2、 编写run.sh脚本和authorized_keys文件

\# vi run.sh 

写入内容：

```shell
#! /bin/bash

/usr/sbin/sshd –D
```

在宿主主机上生成SSH密钥对，并创建authorized_keys

\# ssh-keygen –t rsa 

\# cat ~/sshd_ubuntu/id_rsa.pub >authorized_keys 

3、编写Dockerfile

```SHELL
#设置继承镜像
FROM ubuntu
#提供一些作者的信息
MAINTAINER from www.dockerpool.com by Aiden
#下面开始运行命令，此处更改Ubuntu的源为国内163的源
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse" > /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN apt-get update
#安装ssh服务
RUN apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd
RUN mkdir -p /root/.ssh
#取消pam限制
RUN sed -ri 's/session required pam_loginuid.so/#session required pam_loginuid.so/g' /etc/pam.d/sshd
#复制配置文件到相应位置，并赋予脚本可执行权限
ADD authorized_keys /root/.ssh/authorized_keys
ADD run.sh /run.sh
RUN chmod 755 /run.sh
#开放端口
EXPOSE 22
#设置自启动命令
CMD ["/run.sh"]
```

4、创建镜像

在sshd_ubuntu目录下，使用docker build命令来创建镜像。这里用户需要注意在最后还有一个“.”，表示使用当前目录中的Dockerfile：

\# docker build –t ssh:dockerfile . 

\#docker images 

5．测试镜像，运行容器

下面使用刚才创建的sshd:dockerfile镜像来运行一个容器。直接启动镜像，映射容器的22端口到本地的10122端口：

\# docker run –d –p 10122:22 sshd:dockerfile 

\# docker ps 

在宿主主机新打开一个终端，连接到新建的容器

\# ssh 192.168.56.33 –p 10122 

镜像创建成功

## 常用工具

### BusyBox

BusyBox是一个集成了一百多个最常用Linux命令（如cat、echo、grep、mount、telnet等）的精简工具箱，它只有不到2 MB大小，被誉为“Linux系统的瑞士军刀”。BusyBox可运行于多款POSIX环境的操作系统中，如Linux（包括Android）、Hurd、FreeBSD等。

### Alpine

Alpine操作系统是一个面向安全的轻型Linux发行版，关注安全，性能和资源效能。不同于其他发行版，Alpine采用了musl libc和BusyBox以减小系统的体积和运行时资源消耗，比BusyBox功能上更完善。在保持瘦身的同时，Alpine还提供了包管理工具apk查询和安装软件包。

### Debian/Ubuntu

Debian和Ubuntu都是目前较为流行的Debian系的服务器操作系统，十分适合研发场景。Docker Hub上提供了它们的官方镜像，国内各大容器云服务都提供了完整的支持。

###  CentOS/Fedora

CentOS和Fedora都是基于Redhat的Linux发行版。CentOS是目前企业级服务器的常用操作系统；Fedora则主要面向个人桌面用户。

### Machine

Machine项目是Docker官方的开源项目，负责实现对Docker运行环境进行安装和管理，特别在管理多个Docker环境时，使用Machine要比手动管理高效得多。

### Compose

Compose项目是Docker官方的开源项目，负责实现对基于Docker容器的多应用服务的快速编排。从功能上看，跟OpenStack中的Heat十分类似。其代码目前在https://github.com/docker/compose上开源。

### Swarm

Docker Swarm是Docker公司推出的官方容器集群平台，基于Go语言实现，代码开源在https://github.com/docker/swarm。目前，包括Rackspace等平台都采用了Swarm，用户也很容易在AWS等公有云平台使用Swarm。

Swarm也采用了典型的“主从”结构（如图25-1所示），通过Raft协议来在多个管理节点（Manager）中实现共识。工作节点（Worker）上运行agent接受管理节点的统一管理和任务分配。用户提交服务请求只需要发给管理节点即可，管理节点会按照调度策略在集群中分配节点来运行服务相关的任务。

![image-20200326031421693](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326031421693.png)

### Mesos

Mesos项目是源自UC Berkeley的对集群资源进行抽象和管理的开源项目，类似于操作系统内核，使用它可以很容易地实现分布式应用的自动化调度。同时，Mesos自身也很好地结合和主持了Docker等相关容器技术，基于Mesos已有的大量应用框架，可以实现用户应用的快速上线。本章将介绍Mesos项目的安装、使用、配置、核心原理和实现。

## 核心实现技术

### 基本架构

Docker目前采用了标准的C/S架构，包括客户端、服务端两大核心组件，同时通过镜像仓库来存储镜像。客户端和服务端既可以运行在一个机器上，也可通过socket或者RESTful API来进行通信。

1．服务端

Docker服务端一般在宿主主机后台运行，dockerd作为服务端接受来自客户的请求，并通过containerd具体处理与容器相关的请求，包括创建、运行、删除容器等。服务端主要包括四个组件：

❑ dockerd：为客户端提供RESTful API，响应来自客户端的请求，采用模块化的架构，通过专门的Engine模块来分发管理各个来自客户端的任务。可以单独升级；

❑ docker-proxy：是dockerd的子进程，当需要进行容器端口映射时，docker-proxy完成网络映射配置；

❑ containerd：是dockerd的子进程，提供gRPC接口响应来自dockerd的请求，对下管理runC镜像和容器环境。可以单独升级；

❑ containerd-shim：是containerd的子进程，为runC容器提供支持，同时作为容器内进程的根进程。![image-20200326030729259](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326030729259.png)

2．客户端

Docker客户端为用户提供一系列可执行命令，使用这些命令可实现与Docker服务端交互。

用户使用的Docker可执行命令即为客户端程序。与Docker服务端保持运行方式不同，客户端发送命令后，等待服务端返回；一旦收到返回后，客户端立刻执行结束并退出。用户执行新的命令，需要再次调用客户端命令。

3．镜像仓库

镜像是使用容器的基础，Docker使用镜像仓库（Registry）在大规模场景下存储和分发Docker镜像。镜像仓库提供了对不同存储后端的支持，存放镜像文件，并且支持RESTful API，接收来自dockerd的命令，包括拉取、上传镜像等。

### 命名空间

命名空间（namespace）是Linux内核的一个强大特性，为容器虚拟化的实现带来极大便利。利用这一特性，每个容器都可以拥有自己单独的命名空间，运行在其中的应用都像是在独立的操作系统环境中一样。命名空间机制保证了容器之间彼此互不影响。

1．进程命名空间

Linux通过进程命名空间管理进程号，对于同一进程（同一个task_struct），在不同的命名空间中，看到的进程号不相同。每个进程命名空间有一套自己的进程号管理方法。进程命名空间是一个父子关系的结构，子空间中的进程对于父空间是可见的。新fork出的一个进程，在父命名空间和子命名空间将分别对应不同的进程号。

2. IPC命名空间

容器中的进程交互还是采用了Linux常见的进程间交互方法（Interprocess Communication,IPC），包括信号量、消息队列和共享内存等方式。PID命名空间和IPC命名空间可以组合起来一起使用，同一个IPC命名空间内的进程可以彼此可见，允许进行交互；不同空间的进程则无法交互。

3．网络命名空间

通过网络命名空间，可以实现网络隔离。一个网络命名空间为进程提供了一个完全独立的网络协议栈的视图。包括网络设备接口、IPv4和IPv6协议栈、IP路由表、防火墙规则、sockets等，这样每个容器的网络就能隔离开来。

Docker采用虚拟网络设备（Virtual Network Device, VND）的方式，将不同命名空间的网络设备连接到一起。默认情况下，Docker在宿主机上创建多个虚机网桥（如默认的网桥docker0），容器中的虚拟网卡通过网桥进行连接，如图17-3所示。

![image-20200326031005724](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326031005724.png)

4．挂载命名空间

类似于chroot，挂载（Mount, MNT）命名空间可以将一个进程的根文件系统限制到一个特定的目录下。

挂载命名空间允许不同命名空间的进程看到的本地文件位于宿主机中不同路径下，每个命名空间中的进程所看到的文件目录彼此是隔离的。例如，不同命名空间中的进程，都认为自己独占了一个完整的根文件系统（rootfs），但实际上，不同命名空间中的文件彼此隔离，不会造成相互影响，同时也无法影响宿主机文件系统中的其他路径。

5. UTS命名空间

UTS（UNIX Time-sharing System）命名空间允许每个容器拥有独立的主机名和域名，从而可以虚拟出一个有独立主机名和网络空间的环境，就跟网络上一台独立的主机一样。

6．用户命名空间

每个容器可以有不同的用户和组id，也就是说，可以在容器内使用特定的内部用户执行程序，而非本地系统上存在的用户。

每个容器内部都可以有最高权限的root帐号，但跟宿主主机不在一个命名空间。通过使用隔离的用户命名空间，可以提高安全性，避免容器内的进程获取到额外的权限；同时通过使用不同用户也可以进一步在容器内控制权限。

### 控制组

控制组（CGroups）是Linux内核的一个特性，主要用来对共享资源进行隔离、限制、审计等。只有将分配到容器的资源进行控制，才能避免多个容器同时运行时对宿主机系统的资源竞争。每个控制组是一组对资源的限制，支持层级化结构。

具体来看，控制组提供如下功能：

❑ 资源限制（resource limiting）：可将组设置一定的内存限制。比如：内存子系统可以为进程组设定一个内存使用上限，一旦进程组使用的内存达到限额再申请内存，就会出发Out of Memory警告。

❑ 优先级（prioritization）：通过优先级让一些组优先得到更多的CPU等资源。

❑ 资源审计（accounting）：用来统计系统实际上把多少资源用到适合的目的上，可以使用cpuacct子系统记录某个进程组使用的CPU时间。

❑ 隔离（isolation）：为组隔离命名空间，这样使得一个组不会看到另一个组的进程、网络连接和文件系统。

❑ 控制（control）：执行挂起、恢复和重启动等操作。

### 联合文件系统

联合文件系统（UnionFS）是一种轻量级的高性能分层文件系统，它支持将文件系统中的修改信息作为一次提交，并层层叠加，同时可以将不同目录挂载到同一个虚拟文件系统下，应用看到的是挂载的最终结果。联合文件系统是实现Docker镜像的技术基础。

Docker镜像可以通过分层来进行继承。例如，用户基于基础镜像（用来生成其他镜像的基础，往往没有父镜像）来制作各种不同的应用镜像。这些镜像共享同一个基础镜像层，提高了存储效率。此外，当用户改变了一个Docker镜像（比如升级程序到新的版本），则会创建一个新的层（layer）。因此，用户不用替换整个原镜像或者重新建立，只需要添加新层即可。用户分发镜像的时候，也只需要分发被改动的新层内容（增量部分）。这让Docker的镜像管理变得十分轻量和快速。

Docker目前支持的联合文件系统种类包括AUFS、btrfs、Device Mapper、overlay、over-lay2、vfs、zfs等。多种文件系统目前的支持情况总结如下：

❑ AUFS：最早支持的文件系统，对Debian/Ubuntu支持好，虽然没有合并到Linux内核中，但成熟度很高；

❑ btrfs：参考zfs等特性设计的文件系统，由Linux社区开发，试图未来取代DeviceMapper，成熟度有待提高；

❑ Device Mapper:RedHat公司和Docker团队一起开发用于支持RHEL的文件系统，内核支持，性能略慢，成熟度高；

❑ overlay：类似于AUFS的层次化文件系统，性能更好，从Linux 3.18开始已经合并到内核，但成熟度有待提高；

❑ overlay 2:Docker 1.12后推出，原生支持128层，效率比OverlayFS高，较新版本的Docker支持，要求内核大于4.0；

❑ vfs：基于普通文件系统（ext、nfs等）的中间层抽象，性能差，比较占用空间，成熟度也一般。

❑ zfs：最初设计为Solaris 10上的写时文件系统，拥有不少好的特性，但对Linux支持还不够成熟。

### Linux网络虚拟化

Docker的本地网络实现其实就是利用了Linux上的网络命名空间和虚拟网络设备（特别是vethpair）。熟悉这两部分的基本概念，有助于理解Docker网络的实现过程。

1．基本原理

直观上看，要实现网络通信，机器需要至少一个网络接口（物理接口或虚拟接口）与外界相通，并可以收发数据包；此外，如果不同子网之间要进行通信，需要额外的路由机制。

Docker中的网络接口默认都是虚拟的接口。虚拟接口的最大优势就是转发效率极高。这是因为Linux通过在内核中进行数据复制来实现虚拟接口之间的数据转发，即发送接口的发送缓存中的数据包将被直接复制到接收接口的接收缓存中，而无需通过外部物理网络设备进行交换。对于本地系统和容器内系统来看，虚拟接口跟一个正常的以太网卡相比并无区别，只是它速度要快得多。

Docker容器网络就很好地利用了Linux虚拟网络技术，在本地主机和容器内分别创建一个虚拟接口，并让它们彼此连通（这样的一对接口叫做veth pair），如图17-4所示。

![image-20200511070007099](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511070007099.png)

2．网络创建过程

一般情况下，Docker创建一个容器的时候，会具体执行如下操作：

1）创建一对虚拟接口，分别放到本地主机和新容器的命名空间中；

2）本地主机一端的虚拟接口连接到默认的docker0网桥或指定网桥上，并具有一个以veth开头的唯一名字，如veth1234；

3）容器一端的虚拟接口将放到新创建的容器中，并修改名字作为eth0。这个接口只在容器的命名空间可见；

4）从网桥可用地址段中获取一个空闲地址分配给容器的eth0（例如172.17.0.2/16），并配置默认路由网关为docker0网卡的内部接口docker0的IP地址（例如172.17.42.1/16）。

3．手动配置网络

用户使用--net=none后，Docker将不对容器网络进行配置。下面，将手动完成配置网络的整个过程。通过这个过程，可以了解到Docker配置网络的更多细节。

![image-20200511070059838](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511070059838.png)

![image-20200511070111856](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511070111856.png)

