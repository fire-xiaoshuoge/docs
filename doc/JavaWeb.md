#  Java Web

## 概念

### OSI参考模型

![image-20200226151827578](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200226151827578.png)

![image-20200417053533496](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417053533496.png)

◎ 物理层主要定义物理设备标准，它的主要作用是传输比特流，具体做法是在发送端将1、0转化为电流强弱来进行传输，在到达目的地后再将电流强弱转化为1、0，也就是我们常说的模数转换与数模转换，这一层的数据叫作比特。

◎ 数据链路层主要用于对数据包中的MAC地址进行解析和封装。这一层的数据叫作帧。在这一层工作的设备是网卡、网桥、交换机。

◎ 网络层主要用于对数据包中的IP地址进行封装和解析，这一层的数据叫作数据包。在这一层工作的设备有路由器、交换机、防火墙等。

◎ 传输层定义了传输数据的协议和端口号，主要用于数据的分段、传输和重组。在这一层工作的协议有TCP和UDP等。TCP是传输控制协议，传输效率低，可靠性强，用于传输对可靠性要求高、数据量大的数据，比如支付宝转账使用的就是TCP; UDP是用户数据报协议，与TCP的特性恰恰相反，用于传输可靠性要求不高、数据量小的数据，例如抖音等视频服务就使用了UDP。

◎ 会话层在传输层的基础上建立连接和管理会话，具体包括登录验证、断点续传、数据粘包与分包等。在设备之间需要互相识别的可以是IP，也可以是MAC或者主机名。

◎ 表示层主要对接收的数据进行解释、加密、解密、压缩、解压缩等，即把计算机能够识别的内容转换成人能够识别的内容（图片、声音、文字等）。

◎ 应用层基于网络构建具体应用，例如FTP文件上传下载服务、Telnet服务、HTTP服务、DNS服务、SNMP邮件服务等。

TCP/IP不是指TCP和IP这两个协议的合称，而是指因特网的整个TCP/IP协议簇。从协议分层模型方面来讲，TCP/IP由4个层次组成：网络接口层、网络层、传输层和应用层，如图6-2所示。

![image-20200417053806432](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417053806432.png)

TCP/IP中网络接口层、网络层、传输层和应用层的具体工作职责如下。

◎ 网络接口层（Network Access Layer）：定义了主机间网络连通的协议，具体包括Echernet、FDDI、ATM等通信协议。

◎ 网络层（Internet Layer）：主要用于数据的传输、路由及地址的解析，以保障主机可以把数据发送给任何网络上的目标。数据经过网络传输，发送的顺序和到达的顺序可能发生变化。在网络层使用IP（Internet Protocol）和地址解析协议（ARP）。

◎ 传输层（Transport Layer）：使源端和目的端机器上的对等实体可以基于会话相互通信。在这一层定义了两个端到端的协议TCP和UDP。TCP是面向连接的协议，提供可靠的报文传输和对上层应用的连接服务，除了基本的数据传输，它还有可靠性保证、流量控制、多路复用、优先权和安全性控制等功能。UDP是面向无连接的不可靠传输的协议，主要用于不需要TCP的排序和流量控制等功能的应用程序。

◎ 应用层（Application Layer）：负责具体应用层协议的定义，包括Telnet（TELecommunications NETwork，虚拟终端协议）、FTP（File TransferProtocol，文件传输协议）、SMTP（Simple Mail Transfer Protocol，电子邮件传输协议）、DNS（Domain Name Service，域名服务）、NNTP（Net News TransferProtocol，网上新闻传输协议）和HTTP（HyperText Transfer Protocol，超文本传输协议）等。

### C/S

有的程序需要统一管理软件中使用的数据，所以就将保存数据的数据库统一存放在一台主机中，所有的用户在需要数据时都要从主机获取，这时就分出了客户端和服务端，用户安装的软件叫客户端（Client），统一管理数据的主机中的软件就叫服务端（Server），这种结构就叫CS结构。

![image-20200330020543315](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330020543315.png)

### B/S

CS结构的程序已经可以完成网络通信了，不过使用起来还是有点麻烦，首先软件提供商需要同时开发客户端和服务端两套软件；其次每个用户在使用时都需要单独安装客户端软件，而且升级的时候也需要每个用户都进行升级。为了解决这个问题而设计了统一的客户端，而且默认安装在用户电脑里面，这就是我们电脑中的浏览器（Browser），而且一个浏览器可以访问所有同种类型的网站，当然它主要用作展示数据，具体业务处理是在不同的服务端进行的，这种结构就叫BS结构。BS结构除了提供了统一的客户端，还根据相应协议和标准提供通用的服务器程序，服务器程序统一处理数据连接、封装和解析等工作。

![image-20200330020621197](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330020621197.png)

### CDN

CDN其实是一种特殊的集群页面缓存服务器，它和普通集群的多台页面缓存服务器比主要是它存放的位置和分配请求的方式有点特殊。CDN的服务器是分布在全国各地的，当接收到用户的请求后会将请求分配到最合适的CDN服务器节点获取数据，比如，联通的用户会分配到联通的节点，电信的用户会分配到电信的节点；另外还会按照地理位置进行分配，北京的用户会分配到北京的节点，上海的用户会分配到上海的节点。CDN的每个节点其实就是一个页面缓存服务器，如果没有请求资源的缓存就会从主服务器获取，否则直接返回缓存的页面。CDN分配请求的方式比较特殊，它并不是使用普通的负载均衡服务器来分配的，而是用专门的CDN域名解析服务器在解析域名的时候就分配好的，一般的做法是在ISP那里使用CNAME将域名解析到一个特定的域名，然后再将解析到的那个域名用专门的CDN服务器解析到相应的CDN节点，结构图如图1-11所示。

![image-20200330021848355](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200330021848355.png)

CDN的关键技术包括内容发布、内容路由、内容交换和性能管理，具体如下。

◎ 内容发布：借助建立索引、缓存、流分裂、组播等技术，将内容发布到网络上距离用户最近的中心机房。

◎ 内容路由：通过内容路由器中的重定向（DNS）机制，在多个中心机房的服务器上负载均衡用户的请求，使用户从最近的中心机房获取数据。

◎ 内容交换：根据内容的可用性、服务器的可用性及用户的背景，在缓存服务器上利用应用层交换、流分裂、重定向等技术，智能地平衡负载流量。

◎ 性能管理：通过内部和外部监控系统，获取网络部件的信息，测量内容发布的端到端性能（包丢失、延时、平均带宽、启动时间、帧速率等），保证网络处于最佳运行状态。

**CDN的主要特点如下。**

◎ 本地缓存（Cache）加速：将用户经常访问的数据（尤其静态数据）缓存在本地，以提升系统的响应速度和稳定性。

◎ 镜像服务：消除不同运营商之间的网络差异，实现跨运营商的网络加速，保证不同运营商网络中的用户都能得到良好的网络体验。

◎ 远程加速：利用DNS负载均衡技术为用户选择服务质量最优的服务器，加快用户远程访问的速度。

◎ 带宽优化：自动生成服务器的远程镜像缓存服务器，远程用户在访问时从就近的缓存服务器上读取数据，减少远程访问的带宽，分担网络流量，并降低原站点的Web服务器负载等。

◎ 集群抗攻击：通过网络安全技术和CDN之间的智能冗余机制，可以有效减少网络攻击对网站的影响。

### DNS

域名系统（服务）协议（DNS）[是](https://baike.baidu.com/item/是)一种[分布式网络](https://baike.baidu.com/item/分布式网络/8951687)[目录服务](https://baike.baidu.com/item/目录服务/10413830)，主要用于域名与 IP 地址的相互转换，以及控制因特网的电子邮件的发送。

域名系统（Domain Name System缩写DNS，Domain Name被译为域名）是因特网的一项核心服务，它作为可以将域名和IP地址相互映射的一个分布式数据库，能够使人更方便的访问互联网，而不用去记住能够被机器直接读取的IP数串。

### HTTP

超文本传输协议，超文本就是HTML，传输表示由HTTP负责客户端和服务器的数据传输和解析。客户端发送一个HTTP请求至服务器，服务器响应该请求，将数据再发送给客户端。

### HTTP消息

HTTP 协议是一种通信协议。它允许将 HTML（超文本标记语言）从 Web 服务器传送到Web浏览器，因此需要Web服务器和Web浏览器都支持该协议。

![image-20200220163812049](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200220163812049.png)

### JSP

客户端通过表单将数据提交到action指定的目的地址。在这个目的地址指向的页面，需要将数据提取出来。这就需要一个动作脚本来完成动态网页技术中的数据交互。这种动作脚本与HTML语言相结合来获取和处理表单提交的数据。在Java Web中，这种用于服务器端处理数据的动作脚本就是JSP。

JSP是Java Server Pages的简称，它是在传统的HTML文件中插入Java程序段和JSP标记，形成的JSP（.jsp）文件。它是一种动态网页技术，遵从动态网页的技术标准。

服务器管理JSP页面分为两个阶段：转换阶段和执行阶段。

（1）当有一个JSP请求到来时，服务器会首先检验JSP页面的语法是否正确，将JSP转换成Servlet（Servlet就是用Java语言实现的CGI程序，后面章节将详细介绍）源文件，然后调用javac工具类编译Servlet源文件生成 .class文件，这就是转化阶段。

（2）Servlet容器加载转化后的Servlet类，实例化一个对象处理客户端的请求。在请求处理完成后，响应对象被服务器接受，服务器将 HTML 格式的响应信息发送给客户端，这一阶段便是执行阶段。

![image-20200514044032556](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514044032556.png)

### JSP内置对象

![image-20200514045056299](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514045056299.png)

### Servlet

Servlet（Server Applet）是Java Servlet的简称，称为小服务程序或服务连接器，是指用Java语言编写的服务器端程序，主要功能在于交互式地浏览和修改数据（即用于接收和响应客户端的HTTP请求），生成动态Web内容。

Servlet在扩展基于HTTP的Web服务器时执行以下主要任务。

（1）读取客户端（浏览器）发送的显式的数据。这包括网页上的HTML表单，或者也可以是来自Applet或自定义的HTTP客户端程序的表单。

（2）读取客户端（浏览器）发送的隐式的HTTP请求数据。这包括Cookies、媒体类型和浏览器能理解的压缩格式等。

（3）处理数据并生成结果。这个过程可能需要访问数据库，执行RMI或CORBA调用，调用Web服务，或者直接计算得出对应的响应。

（4）发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML或XML）、二进制文件（GIF图像）、Excel等。

（5）发送隐式的HTTP响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如HTML），设置Cookies和缓存参数，以及其他类似的任务。

### Cookie

Cookie，原意饼干，用来在浏览器端存储用户的状态信息，然后在访问后端的时候将这部分信息带回到后端。例如，在浏览某些网站时，这些网站会把一些数据通过Cookie存储在客户端，方便网站对用户操作进行跟踪用户，实现用户自定义的功能。Cookie的内容主要包括名字、值、过期时间、路径和域。

Cookie本质是浏览器保存信息（保存至服务器）的一种方式，可以理解为一个文件，服务器可以通过响应浏览器的set-cookie的标头，得到Cookie的信息。可以给这个文件设置一个期限，这个期限不会因为浏览器的关闭而消失。

其工作原理如下，工作原理图解如图7-3所示。

（1）发起请求时：浏览器检查所有存储的Cookie，如果某个Cookie所声明的作用范围（由路径和域决定）大于等于将要请求的资源所在的位置，则把该Cookie附在请求资源的HTTP请求头上发送给服务器。

（2）处理请求时：在服务器端，一般会对请求头中带的Cookie信息做检查（比如说登录检查），如果检查通过，才能进行实际的业务处理。

（3）如果校验不通过，例如没有找到Cookie或者Cookie信息不正确（可能是伪造），跳转让其登录，登录完成之后，在响应中返回Cookie信息，浏览器会根据返回的Cookie信息，保存在硬盘或者内存中供下次使用。

![image-20200514093057873](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514093057873.png)

### Session

由于HTTP是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识别具体的用户，这个机制就是Session。Session机制是一种服务器端的机制，服务器使用一种类似于散列表的结构（也可能就是使用散列表）来保存信息。简言之，Session是用来在服务器端保存用户的信息。

其工作原理如下。

（1）浏览器发起请求时，服务器首先会读取请求头中的Session信息。如果没有找到Session信息或者本地检索不到此Session ID，就新生成一个Session ID，存储到服务器硬盘或者内存中。

（2）浏览器接收到响应，会将这个返回的Session ID在本地内存也保存一份，供下一次请求使用。Session保存在本地的其中一种实现方案是保存信息在Cookie上，但是实际上Cookie并不是Session保存的唯一解决方案，使用URL重写的方式也可以（把Session id直接附加在URL路径的后面）。

有以下三种方式来维持Web客户端和Web服务器之间的Session会话。

1. Cookies
2. 隐藏的表单字段
3. URL重写

### Filter

Filter称为过滤器，是Servlet技术中最实用的技术，Web开发人员通过Filter技术，对Web服务器管理的所有Web资源，例如JSP、Servlet、静态图片文件或静态HTML文件进行拦截，从而实现一些特殊功能。例如，实现URL级别的权限控制、过滤敏感词汇、压缩响应信息等一些高级功能。

Filter的本质是实现了Filter接口的Java类，Servlet API提供了一个Filter接口，开发Web应用时，如果编写的Java类实现了这个接口，则把这个Java类称为Filter。通过Filter技术，开发人员可以实现用户在访问某个目标资源之前对访问的请求和响应进行拦截。Filter有以下4种拦截方式。

（1）REQUEST：直接访问目标资源时执行过滤器。包括在地址栏中直接访问、表单提交、超链接、重定向，只要在地址栏中可以看到目标资源的路径，就是REQUEST。

（2）FORWARD：转发访问执行过滤器。包括RequestDispatcher#forward()方法、<jsp:forward>标签都是转发访问。

（3）INCLUDE：包含访问执行过滤器。包括RequestDispatcher#include()方法、<jsp:include>标签都是包含访问。

（4）ERROR：当目标资源在web.xml中配置为<error-page>中时，并且真的出现了异常，转发到目标资源时，会执行过滤器。

![image-20200514093427064](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514093427064.png)

（1）当客户端发生请求后，在HTTP请求到达之前，过滤器拦截客户的HTTP请求。

（2）根据需要检查HTTP请求，也可以修改HTTP请求头和数据。

（3）在过滤器中调用doFilter方法，对请求放行。请求到达Servlet后，对请求进行处理并产生HTTP响应发送给客户端。

（4）在HTTP响应到达客户端之前，过滤器拦截HTTP响应。

（5）根据需要检查HTTP响应，可以修改HTTP响应头和数据。

（6）最后，HTTP响应到达客户端。

Filter接口中有以下三个重要的方法。

（1）init()方法：初始化参数，在创建Filter时自动调用。当需要设置初始化参数的时候，可以写到该方法中。

（2）doFilter()方法：拦截到要执行的请求时，doFilter就会执行。在这里面编写对请求和响应的预处理。

（3）destroy()方法：在销毁Filter时自动调用。

Filter和Sevlet类似，也有生命周期，其生命周期如下。

（1）服务器启动的时候，Web服务器创建Filter的实例对象，并调用其init方法，完成对象的初始化功能。Filter对象只会创建一次，init方法也只会执行一次。Filter的创建和销毁由Web服务器控制。

（2）拦截到请求时，执行doFilter方法。可以执行多次。

（3）服务器关闭时，Web服务器销毁Filter的实例对象。

![image-20200514094321334](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514094321334.png)

### Listener

Java Web中的监听器（Listener）与Filter类似，也是Servlet规范中定义的一种特殊类，也是由容器管理的，其主要是通过Listener接口监听在容器中的某个执行程序，并且根据其应用程序的需求做出相应的响应，于是，Listener监听器极大地增强了Web应用的事件处理能力，目前也已经成为Web应用开发的一个重要组成部分。

Listener监听器用于监听Java Web应用程序中的ServletContext、HttpSession和ServletRequest等域对象的创建与销毁事件，以及监听这些域对象的属性发生修改的事件，例如创建、修改、删除Session、Request、Context等，并触发响应的响应事件。Listener是通过观察者设计模式进行实现的。观察者模式又叫发布订阅模式或者监听器模式。在该模式中有两个角色：观察者和被观察者（通常也叫作主题）。观察者在主题里面注册自己感兴趣的事件，当这个事件发生时，主题会通过回调接口的方式通知观察者。

Servlet 3.0提供了8个监听器接口，按照其作用域来划分可以分为以下三类。

（1）Servlet上下文相关监听接口，包括：ServletContextListener和ServletContextAttributeListener。

（2）HTTP会话监听接口，包括：HttpSessionListener、HttpSessionActivationListener、HttpSessionAttribute Listener和HttpSessionBindingListener。

（3）Servlet请求监听接口，包括：ServletRequestListener和ServletRequestAttributeListener。

### JSP

JSP全名为Java Server Pages，即Java服务器页面，是一个简化的Servlet设计，它是由SunMicrosystems公司倡导、许多公司参与一起建立的一种动态网页技术标准。JSP技术有点儿类似ASP技术，它是在传统的网页HTML文件中插入Java程序段和JSP标记，从而形成JSP文件，后缀名为.jsp。用JSP开发的Web应用是跨平台的，既能在Linux下运行，也能在其他操作系统上运行。

JSP机制概述：可以把JSP页面的执行分成两个阶段，一个是转译阶段，一个是请求阶段。

转译阶段：JSP页面转换成Servlet类。

请求阶段：Servlet类执行，将响应结果发送至客户端，可分为以下6步完成。

（1）用户（客户机）访问响应的JSP页面，如http://localhost:8080/test/hello.jsp。

（2）服务器找到相应的JSP页面。

（3）服务器将JSP转译成Servlet的源代码。

（4）服务器将Servlet源代码编译为class文件。

（5）服务器将class文件加载到内存并执行。

（6）服务器将class文件执行后生成HTML代码发送给客户机，客户机浏览器根据相应的HTML代码进行显示。

JSP中一共预先定义了9个这样的内置对象，分别为：Request、Response、Session、Application、Out、PageContext、Config、Page、Exception。

### JavaBean

JavaBean在Java语言开发中是一个很重要的组件，在JSP中使用JavaBean组件可以减少很多的重复代码，从而使JSP代码更加简洁。JavaBean是特殊的Java类，使用Java语言书写，并且遵守JavaBean API规范。

接下来给出的是JavaBean与其他Java类相比而言独一无二的特征。

（1）提供一个默认的无参数构造函数。

（2）需要被序列化并且实现了Serializable接口。

（3）可能有一系列可读写属性。

（4）可能有一系列的getter或setter方法。

![image-20200514095224760](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514095224760.png)

### JSTL

JSTL（Java Server Pages Standard Tag Library，JSP标准标签库）是一个不断完善的开放源代码的JSP标签库，是由Apache的Jakarta小组来维护的，由4个定制标记库（core、format、xml和sql）和一对通用标记库验证器（ScriptFreeTLV和PermittedTaglibsTLV）组成，组成对应下面5个标签库。

### URL

统一资源标识符（Uniform ResourceLocator）。

### HTML

客户端（浏览器）通过HTTP接收的资源一般是一个HTML页面，用户并不理解HTML页面，必须由客户端（浏览器）将HTML页面转换为用户能理解的内容，HTML是超文本标记语言。

### TCP/IP概述

![image-20200220164030278](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200220164030278.png)

### 三次握手四次挥手

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=2590032753,2466318043&fm=173&app=49&f=JPEG?w=640&h=716&s=E7F239D247AFCCEA106594580300D072)

TCP在传输之前会进行三次沟通，一般称为“三次握手”，传完数据断开的时候要进行四次沟通，一般称为“四次挥手”。要理解这个过程首先需要理解TCP中的两个序号和三个标志位的含义：

□seq：sequence number的缩写，表示所传数据的序号。TCP传输时每一个字节都有一个序号，发送数据时会将数据的第一个序号发送给对方，接收方会按序号检查是否接收完整了，如果没接收完整就需要重新传送，这样就可以保证数据的完整性。

□ack：acknoledgement number的缩写，表示确认号。接收端用它来给发送端反馈已经成功接收到的数据信息的，它的值为希望接收的下一个数据包起始序号，也就是ack值所代表的序号前面数据已经成功接收到了。

□ACK：确认位，只有ACK=1的时候ack才起作用。正常通信时ACK为1，第一次发起请求时因为没有需要确认接收的数据所以ACK为0。

□SYN：同步位，用于在建立连接时同步序号。刚开始建立连接时并没有历史接收的数据，所以ack也就没办法设置，这时按照正常的机制就无法运行了，SYN的作用就是来解决这个问题的，当接收端接收到SYN=1的报文时就会直接将ack设置为接收到的seq+1的值，注意这里的值并不是校验后设置的，而是根据SYN直接设置的，这样正常的机制就可以运行了，所以SYN叫同步位。需要注意的是，SYN会在前两次握手时都为1，这是因为通信的双方的ack都需要设置一个初始值。

□FIN：终止位，用来在数据传输完毕后释放连接。

**三次握手建立连接：**

**第一次握手：**客户端发送syn包(seq=x)到服务器，并进入SYN_SEND状态，等待服务器确认;

**第二次握手：**服务器收到syn包，必须确认客户的SYN(ack=x+1)，同时自己也发送一个SYN包(seq=y)，即SYN+ACK包，此时服务器进入SYN_RECV状态;

**第三次握手：**客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。

**四次握手断开连接：**

**第一次挥手：**主动关闭方发送一个FIN，用来关闭主动方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方：我已经不会再给你发数据了(当 然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，主动关闭方依然会重发这些数据)，但此时主动关闭方还可以接受数据。

**第二次挥手：**被动关闭方收到FIN包后，发送一个ACK给对方，确认序号为收到序号+1(与SYN相同，一个FIN占用一个序号)。

**第三次挥手：**被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了，不会再给你发数据了。

**第四次挥手：**主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手。

### 子网掩码

子网掩码（Subnet Mask）用来将IP地址划分成网络地址和主机地址两部分，确定这个地址中哪一个部分是网络部分。对于同一个IP地址，如果其子网掩码不同，则代表不同的网络或主机。

### IP协议

Internet Protocol简称IP，又译为网际协议或互联网协议，是用在TCP/IP协议簇中的网络层协议。RFC 791“INTERNET PROTOCOL（Internet协议）”是IP协议的正式规范文件。随着Internet技术的发展，还有一些RFC文档对IP协议进行补充和扩展。

### ARP

地址解析协议，即ARP（Address Resolution Protocol），是根据[IP地址](https://baike.baidu.com/item/IP地址)获取[物理地址](https://baike.baidu.com/item/物理地址/2129)的一个[TCP/IP协议](https://baike.baidu.com/item/TCP%2FIP协议)。[主机](https://baike.baidu.com/item/主机/455151)发送信息时将包含目标IP地址的ARP请求广播到局域网络上的所有主机，并接收返回消息，以此确定目标的物理地址；收到返回消息后将该IP地址和物理地址存入本机ARP缓存中并保留一定时间，下次请求时直接查询ARP缓存以节约资源。地址解析协议是建立在网络中各个主机互相信任的基础上的，局域网络上的主机可以自主发送ARP应答消息，其他主机收到应答报文时不会检测该报文的真实性就会将其记入本机ARP缓存；由此攻击者就可以向某一主机发送伪ARP应答报文，使其发送的信息无法到达预期的主机或到达错误的主机，这就构成了一个[ARP欺骗](https://baike.baidu.com/item/ARP欺骗)。[ARP命令](https://baike.baidu.com/item/ARP命令)可用于查询本机ARP缓存中IP地址和[MAC地址](https://baike.baidu.com/item/MAC地址)的对应关系、添加或删除静态对应关系等。相关协议有[RARP](https://baike.baidu.com/item/RARP)、[代理ARP](https://baike.baidu.com/item/代理ARP)。[NDP](https://baike.baidu.com/item/NDP)用于在[IPv6](https://baike.baidu.com/item/IPv6)中代替地址解析协议。

### RARP

反向地址转换协议（RARP：Reverse Address Resolution Protocol） 反向地址转换协议（RARP）允许局域网的物理机器从[网关](https://baike.baidu.com/item/网关/98992)服务器的 ARP 表或者缓存上请求其 IP 地址。[网络管理员](https://baike.baidu.com/item/网络管理员/595848)在局域网[网关](https://baike.baidu.com/item/网关/98992)[路由器](https://baike.baidu.com/item/路由器/108294)里创建一个表以映射[物理地址](https://baike.baidu.com/item/物理地址/2129)（MAC）和与其对应的 IP 地址。当设置一台新的机器时，其 RARP 客户机程序需要向[路由器](https://baike.baidu.com/item/路由器/108294)上的 RARP 服务器请求相应的 IP 地址。假设在[路由表](https://baike.baidu.com/item/路由表/2707408)中已经设置了一个记录，RARP 服务器将会返回 IP 地址给机器，此机器就会存储起来以便日后使用。

### ICMP

ICMP（Internet Control Message Protocol）Internet控制[报文](https://baike.baidu.com/item/报文/3164352)协议。它是[TCP/IP协议簇](https://baike.baidu.com/item/TCP%2FIP协议簇)的一个子协议，用于在IP[主机](https://baike.baidu.com/item/主机/455151)、[路由](https://baike.baidu.com/item/路由)器之间传递控制消息。控制消息是指[网络通](https://baike.baidu.com/item/网络通)不通、[主机](https://baike.baidu.com/item/主机/455151)是否可达、[路由](https://baike.baidu.com/item/路由/363497)是否可用等网络本身的消息。这些控制消息虽然并不传输用户数据，但是对于用户数据的传递起着重要的作用。

### IP路由器

同一网络区段中的计算机可以直接通信，不同网络区段中的计算机要相互通信，则必须借助于路由器。路由器是在互联网络中实现路由功能的主要节点设备。典型的路由器通过局域网或广域网连接到两个或多个网络。路由器将网络划分为不同的子网（也有人将其称为网段），每个子网内部的数据包传送不会经过路由器，只有在子网之间传输数据包才经过路由器，这样提高了网络带宽的利用率。路由器还能用于连接不同拓扑结构的网络。

### RIP协议

RIP是一种较为简单的内部网关协议，主要用于规模较小的网络中，如校园网以及结构较为简单的园区网络。对于更为复杂的环境和大型网络，一般不使用RIP。RIP属于距离向量路由协议，只同相邻的路由器互相交换路由表，交换的路由信息也比较有限，仅包括目的网络地址、下一跳地址以及路由距离。由于RIP的实现较为简单，在配置和维护管理方面容易，因此在实际组网中仍有应用。

### OSPF协议

RIP受限于最大路径长度15，因此不能满足大型网络的要求，而开放最短路径优先（Open Shortest Path First, OSPF）协议是一个能够解决此问题的内部网关协议。OSPF设计用于在大型或特大型互联网络中交换路由信息，最多可支持几百台路由器。OSPF的最大优点是效率高，要求很小的网络开销，适应范围广，可以说是目前应用最广、性能最好的路由协议。

### TCP协议

TCP（Transmission Control Protocol，传输控制协议）是传输层最重要和最常用的协议。它提供一种面向连接的、可靠的数据传输服务，保证了端到端数据传输的可靠性。它同时也是一个比较复杂的协议，提供了传输层几乎所有的功能，支持多种网络应用。RFC 793“TRANSMISSIONCONTROL PROTOCOL DARPA INTERNET PROGRAM PROTOCOLSPECIFICATION”是TCP协议的正式规范文件。

### UDP协议

UDP（User Datagram Protocol，用户数据报协议）是不可靠的无连接的基于数据报的协议，支持无连接IP数据报的通信方式。相对TCP协议来说，UDP是一种非常简单的协议，在网络层的基础上实现了进程之间端到端的通信。RFC 768“User Datagram Protocol”是UDP协议的正式规范文件。

### DHCP协议

DHCP（Dynamic Host Configuration Protocol）可译为“动态主机配置协议”，是TCP/IP协议的应用层协议，提供一种向TCP/IP网络中的主机传递配置信息的架构，从而实现IP地址自动分配和TCP/IP参数自动配置。

### Telnet协议

Telnet是Telecommunication Network Protocol的缩写，作为一种著名的、历史较长的Internet协议，旨在让用户能够登录到一台远程主机并建立远程会话，执行各种操作，就像直接在远程计算机上工作一样。Telnet主要由RFC 854 “Telnet Protocol Specification”和RFC 855“TelnetOption Specifications”定义，还有一些其他RFC文档进行补充和扩展。

### FTP协议

FTP全称FILE TRANSFER PROTOCOL，可译为文件传输协议，主要用于不同类型的计算机之间传输文件。用户连接到FTP服务器，可以进行文件或目录的复制、移动、创建和删除等操作。FTP工作在TCP/IP模型的应用层，其下层传输协议是TCP，是面向连接的协议，为数据传输提供可靠的保证。FTP由RFC 959 “FILE TRANSFER PROTOCOL (FTP)”定义，还有一些其他RFC文档进行补充和扩展。

### 电子邮件协议

电子邮件用于网上信息传递和交流，是最重要的Internet服务之一。邮件系统中各种角色之间要实现通信，必须采用相应的协议，有3种邮件协议最重要，SMTP（Simple Mail Transfer Protocol）是用于传递邮件的标准协议，POP（Post Office Protocol）和IMAP（Internet MessageAccess Protocol）都是用于收取邮件的标准协议。通常一台提供收发邮件服务的邮件服务器至少需要两个邮件协议，一个是SMTP，用于发送邮件；另一个是POP或IMAP，用于接收邮件。另外，MIME（Multipurpose Internet Mail Extensions）是目前广泛应用的一种电子邮件技术规范。

###  SNMP协议

TCP/IP网络需要统一的网络管理体系结构和协议进行管理，目前SNMP（Simple Network Management Protocol，简单网络管理协议）应用最为广泛。SNMP是专门设计用于IP网络管理网络节点的一种标准协议，它是一种应用层协议，主要通过一组TCP/IP协议及其所依附的资源来提供网络管理服务。SNMP有3种版本：SNMPv1、SNMPv2和SNMPv3。

###  Java Socket

Java中的网络通信是通过Socket实现的，Socket分为ServerSocket和Socket两大类，ServerSocket用于服务端，可以通过accept方法监听请求，监听到请求后返回Socket，Socket用于具体完成数据传输，客户端直接使用Socket发起请求并传输数据。

ServerSocket的使用可以分为三步：

1）创建ServerSocket。ServerSocket的构造方法一共有5个，用起来最方便的是Server-Socket（int port），只需要一个port（端口号）就可以了。

2）调用创建出来的ServerSocket的accept方法进行监听。accept方法是阻塞方法，也就是说调用accept方法后程序会停下来等待连接请求，在接收到请求之前程序将不会往下走，当接收到请求后accept方法会返回一个Socket。

3）使用accept方法返回的Socket与客户端进行通信。

### 对称加密算法

一般是通过一个算法和一个密钥（secret key）对明文（plaintext）进行处理，得到的不规则字符就是密文（ciphertext）。

### 非对称加密算法

非对称加密算法需要两个密钥：公开密钥（publickey:简称公钥）和私有密钥（privatekey:简称私钥）。公钥与私钥是一对，如果用公钥对数据进行加密，只有用对应的私钥才能解密。因为加密和解密使用的是两个不同的密钥，所以这种算法叫作非对称加密算法。 非对称加密算法实现机密信息交换的基本过程是：甲方生成一对密钥并将公钥公开，需要向甲方发送信息的其他角色(乙方)使用该密钥(甲方的公钥)对机密信息进行加密后再发送给甲方；甲方再用自己私钥对加密后的信息进行解密。甲方想要回复乙方时正好相反，使用乙方的公钥对数据进行加密，同理，乙方使用自己的私钥来进行解密。

### SSL/TLS

SSL“安全套接层”协议，TLS“安全传输层”协议，都属于是加密协议，在其网络数据传输中起到保护隐私和数据的完整性。保证该网络传输的信息不会被未经授权的元素拦截或修改，从而确保只有合法的发送者和接收者才能完全访问并传输信息。

### https

HTTPS （全称：Hyper Text Transfer Protocol over SecureSocket Layer），是以安全为目标的 HTTP 通道，在HTTP的基础上通过传输加密和身份认证保证了传输过程的安全性 。HTTPS 在HTTP 的基础下加入SSL层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。 HTTPS 存在不同于 HTTP 的默认端口及一个加密/身份验证层（在 HTTP与 TCP之间）。这个系统提供了身份验证与加密通讯方法。

### JSON

JOSN(JavaScriptObject Notation, JS 对象简谱) 是一种轻量级的数据交换格式。它基于 ECMACScript (欧洲计算机协会制定的js规范)的一个子集，的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

### Ajax

Ajax 即“A*synchronous* J*avascript And* X*ML*”（异步 JavaScript 和 XML），是指一种创建交互式、快速动态网页应用的网页开发技术，无需重新加载整个网页的情况下，能够更新部分网页的技术。

通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

### RESTful

RESTful也称为REST（Representational State Transfer），可以将它理解为一种软件架构风格或设计风格。RESTful风格就是把请求参数变成请求路径的一种风格

### 请求转发与重定向

1)、请求转换是服务器内部跳转，所有地址栏上的路径不会改变.

 重定向是浏览器在次发送请求，地址栏上的路径会发生改变.

2)、请求转发只发送一次请求。

​    重定向会发送两次请求.

3)、请求转发只能在当前应用内部跳转. 

  重定向可以在内部跳转也可以跳出当前应用.

4)、请求转发时，因为是内部跳转。它的路径写法是  /资源路径。

  重定向，它的路径需要写  /工程名/资源路径. 

5)、请求转发，可以共享reqeust。

  重定向不可能，因为每一次都是一个新的request。

6)、请求转换是通过reqeust发起  request.getRequestDispatcher().forward();

   重定向  response发起  response.sendRedirect();

![image-20200514045808811](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514045808811.png)

### 会话劫持

攻击者通过一些手段来获取其他用户session id的攻击就叫session劫持。一个典型的场景是在未加密的Wi-Fi网络中，由于session id在用户的请求内而且是不加密的（未使用HTTPS），通过嗅探工具可以获取到用户的session id，然后可以冒充用户进行各种操作。除了嗅探外，还有一些其他的手段，如跨站脚本攻击、暴力破解、计算等。

如果使用了HTTPS加密传输，那么理论上可以防止嗅探，但实际上，HTTPS在世界范围内远未普及开来，许多网站登录的时候使用了HTTPS，登录成功后仍然返回了HTTP会话，一些网站虽然支持HTTPS，但并不作为默认选项，目前已知的网站中gmail是默认全部使用了HTTPS会话的，但常用的各种社交网站、电商网站大多只是部分采用HTTPS。因为HTTPS无法实现缓存、响应变得缓慢、运营成本高、虚拟主机无法在同一台物理服务器上为多个网站提供服务、和其他不支持HTTPS应用的交互，以上种种因素都制约着HTTPS的普及。

### 中间人攻击

中间人攻击是指攻击者在通信的两端分别创建独立的连接，并交换其所收到的数据，使通信的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制（例如，在一个未加密的Wi-Fi无线接入点的中间人攻击者，可以将自己作为一个中间人插入这个网络）。

中间人攻击能够成功的一个前提条件是攻击者能将自己伪装成每一个参与会话的终端，并且不被其他终端识破。大多数的加密协议都专门加入了一些特殊的认证方法以阻止中间人攻击。例如，SSL协议可以验证参与通信的一方或双方使用的证书是否由权威的受信任的数字证书认证机构颁发，并且能执行双向身份认证。

### 跨站脚本攻击

跨站脚本攻击（Cross Site Scripting）是指攻击者利用网站程序对用户输入过滤不足，输入可以显示在页面上对其他用户造成影响的HTML代码，从而盗取用户资料、利用用户身份进行某种动作，或者对访问者进行病毒侵害的一种攻击方式。针对这种攻击，主要应做好输入数据的验证，对输出数据进行适当的编码，以防止任何已成功注入的脚本在浏览器端运行。

### Https的SSL握手过程

Https协议由两部分组成:http+ssl,即在http协议的基础上增加了一层ssl的握手过程.

浏览器作为发起方,向网站发送一套浏览器自身支持的加密规则,比如客户端支持的加密算法,Hash算法,ssl版本,以及一个28字节的随机数client_random

.网站选出一套加密算法和hash算法，生成一个服务端随机数server_random并以证书的形式返回给客户端浏览器，这个证书还包含网站地址、公钥public_key、证书的颁发机构CA以及证书过期时间。

.浏览器解析证书是否有效，如果无效则浏览器弹出提示框告警。如果证书有效，则根据server_random生成一个preMaster_secret和Master_secret[会话密钥], master_secret 的生成需要 preMaster_key ，并需要 client_random 和 server_random 作为种子。浏览器向服务器发送经过public_key加密的preMaster_secret,以及对握手消息取hash值并使用master_secret进行加密发送给网站.[客户端握手结束通知，表示客户端的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供服务器校验]

.服务器使用private_key 解密后得到preMaster_secret,再根据client_random 和 server_random 作为种子得到master_secret.然后使用master_secret解密握手消息并计算hash值,跟浏览器发送的hash值对比是否一致.
然后把握手消息通过master_secret进行对称加密后返回给浏览器.以及把握手消息进行hash且master_secret加密后发给浏览器.[服务器握手结束通知，表示服务器的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供客户端校验。]

.客户端同样可以使用master_secret进行解密得到握手消息.校验握手消息的hash值是否跟服务器发送过来的hash值一致,一致则握手结束.通信开始

.以后的通信都是通过master_secret+对称加密算法的方式进行. 客户端与服务器进入加密通信，就完全是使用普通的HTTP协议，只不过用"会话密钥"加密内容。SSL握手过程中如果有任何错误，都会使加密连接断开，从而阻止了隐私信息的传输。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191015183437893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoZW5ncWlhbmZlbmc=,size_16,color_FFFFFF,t_70)



### 短连接

短连接是当服务端与客户端连接成功后开始传输数据，数据传输完毕后则连接立即关闭，如果还想再次传输数据，则需要再创建新的连接进行数据传输。

### 长连接

长连接可以实现当服务端与客户端连接成功后连续地传输数据，在这个过程中，连接保持开启的状态，数据传输完毕后连接不关闭。长连接是指建立Socket连接后，无论是否使用这个连接，该连接都保持连接的状态。

### 负载均衡

负载均衡建立在现有网络结构之上，提供了一种廉价、有效、透明的方法来扩展网络设备和服务器的带宽，增加了吞吐量，加强了网络数据处理能力，并提高了网络的灵活性和可用性。项目中常用的负载均衡有四层负载均衡和七层负载均衡。

###  四层负载均衡

四层负载均衡基于IP和端口的方式实现网络的负载均衡，具体实现为对外提供一个虚拟IP和端口接收所有用户的请求，然后根据负载均衡配置和负载均衡策略将请求发送给真实的服务器。

四层负载均衡主要通过修改报文中的目标地址和端口来实现报文的分发和负载均衡。以TCP为例，负载均衡设备在接收到第1个来自客户端的SYN请求后，会根据负载均衡配置和负载均衡策略选择一个最佳的服务器，并将报文中的目标IP地址修改为该服务器的IP直接转发给该服务器。TCP连接的建立（即三次握手过程）是在客户端和服务器端之间完成的，负载均衡设备只起到路由器的转发功能。

四层负载均衡常用的软硬件如下。

◎ F5：硬件负载均衡器，功能完备，价格昂贵。

◎ LVS：基于IP+端口实现的四层负载软件，常和Keepalive配合使用。

◎ Nginx：同时实现四层负载和七层负载均衡，带缓存功能，可基于正则表达式灵活转发。

### 七层负载均衡

七层负载均衡基于URL等资源来实现应用层基于内容的负载均衡，具体实现为通过虚拟的URL或主机名接收所有用户的请求，然后将请求发送给真实的服务器。

七层应用负载可以使整个网络更智能化，七层负载均衡根据不同的数据类型将数据存储在不同的服务器上来提高网络整体的负载能力。比如将客户端的基本信息存储在内存较大的缓存服务器上，将文件信息存储在磁盘空间较大的文件服务器上，将图片视频存储在网络I/O能力较强的流媒体服务器上。在接收到不同的客户端的请求时从不同的服务器上获取数据并将其返回给客户端，提高客户端的访问效率。

七层负载均衡常用的软件如下。

◎ HAProxy：支持七层代理、会话保持、标记、路径转移等。

◎ Nginx：同时实现四层负载和七层负载均衡，在HTTP和Mail协议上功能比较好，性能与HAProxy差不多。

◎ Apache：使用简单，性能较差。

四层负载均衡和七层负载均衡的最大差别是：四层负载均衡只能针对IP地址和端口上的数据做统一的分发，而七层负载均衡能根据消息的内容做更加详细的有针对性的负载均衡。我们通常使用LVS等技术实现基于Socket的四层负载均衡，使用Nginx等技术实现基于内容分发的七层负载均衡，比如将以“/user/***”*开头的URL请求负载到单点登录服务器，而将以“/business/**”*开头的URL请求负载到具体的业务服务器，如图6-7所示。

![image-20200417054130430](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054130430.png)

### 负载均衡算法

常用的负载均衡算法有：轮询均衡（Round Robin）、权重轮询均衡（Weighted RoundRobin）、随机均衡（Random）、权重随机均衡（Weighted Random）、响应速度均衡（Response Time）、最少连接数均衡（Least Connection）、处理能力均衡、DNS响应均衡（Flash DNS）、散列算法均衡、IP地址散列、URL散列。不同的负载均衡算法适用于不同的应用场景。

1．轮询均衡（Round Robin）

轮询均衡指将客户端请求轮流分配到1至N台服务器上，每台服务器均被均等地分配一定数量的客户端请求。轮询均衡算法适用于集群中所有服务器都有相同的软硬件配置和服务能力的情况下。

2．权重轮询均衡

（Weighted Round Robin）权重轮询均衡指根据每台服务器的不同配置及服务能力，为每台服务器都设置不同的权重值，然后按照设置的权重值轮询地将请求分配到不同的服务器上。例如，服务器A的权重值被设计成3，服务器B的权重值被设计成3，服务器C的权重值被设计成4，则服务器A、B、C将分别承担30%、30%、40%的客户端请求。权重轮询均衡算法主要用于服务器配置不均等的集群中。

3．随机均衡（Random）

随机均衡指将来自网络的请求随机分配给内部的多台服务器，不考虑服务器的配置和负载情况。

4．权重随机均衡（Weighted Random）

权重随机均衡算法类似于权重轮询算法，只是在分配请求时不再轮询发送，而是随机选择某个权重的服务器发送。

5．响应速度均衡（Response Time）

响应速度均衡指根据服务器设备响应速度的不同将客户端请求发送到响应速度最快的服务器上。对响应速度的获取是通过负载均衡设备定时为每台服务都发出一个探测请求（例如Ping）实现的。响应速度均衡能够为当前的每台服务器根据其不同的负载情况分配不同的客户端请求，这有效避免了某台服务器单点负载过高的情况。但需要注意的是，这里探测到的响应速度是负载均衡设备到各个服务器之间的响应速度，并不完全代表客户端到服务器的响应速度，因此存在一定偏差。

6．最少连接数均衡（Least Connection）

最少连接数均衡指在负载均衡器内部记录当前每台服务器正在处理的连接数量，在有新的请求时，将该请求分配给连接数最少的服务器。这种均衡算法适用于网络连接和带宽有限、CPU处理任务简单的请求服务，例如FTP。

7．处理能力均衡

处理能力均衡算法将服务请求分配给内部负荷最轻的服务器，负荷是根据服务器的CPU型号、CPU数量、内存大小及当前连接数等换算而成的。处理能力均衡算法由于考虑到了内部服务器的处理能力及当前网络的运行状况，所以相对来说更加精确，尤其适用于七层负载均衡的场景。

8．DNS响应均衡（Flash DNS）

DNS响应均衡算法指在分布在不同中心机房的负载均衡设备都收到同一个客户端的域名解析请求时，所有负载均衡设备均解析此域名并将解析后的服务器IP地址返回给客户端，客户端向收到第一个域名解析后的IP地址发起请求服务，而忽略其他负载均衡设备的响应。这种均衡算法适用于全局负载均衡的场景。

9．散列算法均衡

散列算法均衡指通过一致性散列算法和虚拟节点技术将相同参数的请求总是发送到同一台服务器，该服务器将长期、稳定地为某些客户端提供服务。在某个服务器被移除或异常宕机后，该服务器的请求基于虚拟节点技术平摊到其他服务器，而不会影响集群整体的稳定性。

10．IP地址散列

IP地址散列指在负载均衡器内部维护了不同链接上客户端和服务器的IP对应关系表，将来自同一客户端的请求统一转发给相同的服务器。该算法能够以会话为单位，保证同一客户端的请求能够一直在同一台服务器上处理，主要适用于客户端和服务器需要保持长连接的场景，比如基于TCP长连接的应用。

11．URL散列

URL散列指通过管理客户端请求URL信息的散列表，将相同URL的请求转发给同一台服务器。该算法主要适用于在七层负载中根据用户请求类型的不同将其转发给不同类型的应用服务器。

### LVS

LVS（Linux Virtual Server）是一个虚拟的服务器集群系统，采用IP负载均衡技术将请求均衡地转移到不同的服务器上执行，且通过调度器自动屏蔽故障服务器，从而将一组服务器构成一个高性能、高可用的虚拟服务器。整个服务器集群的结构对用户是透明的，无须修改客户端和服务器端的程序，便可实现客户端到服务器的负载均衡。

LVS由前端的负载均衡器（Load Balancer, LB）和后端的真实服务器（Real Server, RS）群组成，在真实服务器间可通过局域网或广域网连接。LVS的这种结构对用户是透明的，用户只需要关注作为LB的虚拟服务器（Virtual Server），而不需要关注提供服务的真实服务器群。在用户的请求被发送给虚拟服务器后，LB根据设定的包转发策略和负载均衡调度算法将用户的请求转发给真实服务器，真实服务器再将用户请求的结果返回给用户。

实现LVS的核心组件有负载均衡调度器、服务器池和共享存储。

◎ 负载均衡调度器（Load Balancer/Director）：是整个集群对外提供服务的入口，通过对外提供一个虚拟IP来接收客户端请求。在客户端将请求发送到该虚拟IP后，负载均衡调度器会负责将请求按照负载均衡策略发送到一组具体的服务器上。

◎ 服务器池（Server Pool）：服务器池是一组真正处理客户端请求的真实服务器，具体执行的服务有WEB、MAIL、FTP和DNS等。

◎ 共享存储（Shared Storage）：为服务器池提供一个共享的存储区，使得服务器池拥有相同的内容，提供相同的服务。

在接收LVS内部数据的转发流程前，这里先以表6-3介绍LVS技术中常用的一些名词，以让我们更好地理解LVS的工作原理。

![image-20200417054614433](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054614433.png)

#### LVS数据转发

LVS的数据转发流程是LVS设计的核心部分，如下所述，如图6-8所示。

![image-20200417054650217](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054650217.png)

（1）PREROUTING链接收用户请求：客户端向PREROUTING链发送请求。

（2）INPUT链转发：在PREROUTING链通过RouteTable列表发现请求数据包的目的地址是本机时，将数据包发送给INPUT链。

（3）IPVS检查：IPVS检查INPUT链上的数据包，如果数据包中的目的地址和端口不在规则列表中，则将该数据包发送到用户空间的ipvsadm。ipvsadm主要用于用户定义和管理集群。

（4）POSTROUTING链转发：如果数据包里面的目的地址和端口都在规则里面，那么将该数据包中的目的地址修改为事先定义好的真实服务器地址，通过FORWARD将数据发送到POSTROUTING链。

（5）真实服务器转发：POSTROUTING链根据数据包中的目的地址将数据包转发到真实服务器。

#### LVS NAT模式

LVS NAT（Network Address Translation）即网络地址转换模式，具体的实现流程如图6-9所示。

![image-20200417054726796](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054726796.png)

NAT模式通过对请求报文和响应报文的地址进行改写完成对数据的转发，具体流程如下。

（1）客户端将请求报文发送到LVS，请求报文的源地址是CIP（Client IP Address，客户端IP），目标地址是VIP（Virtual IP Address，虚拟IP）。

（2）LVS在收到报文后，发现请求的IP地址在LVS的规则列表中存在，则将客户端请求报文的目标地址VIP修改为RIP（Real-server IP Address，后端服务器的真实IP），并将报文发送到具体的真实服务器上。

（3）真实服务器在收到报文后，由于报文的目标地址是自己的IP，所以会响应该请求，并将响应报文返回给LVS。

（4）LVS在收到数据后将此报文的源地址修改为本机IP地址，即VIP，并将报文发送给客户端。

NAT模式的特点如下。

◎ 请求的报文和响应的报文都需要通过LVS进行地址改写，因此在并发访问量较大的时候LVS存在瓶颈问题，一般适用于节点不是很多的情况下。

◎ 只需要在LVS上配置一个公网IP即可。

◎ 每台内部的真实服务器的网关地址都必须是LVS的内网地址。

◎ NAT模式支持对IP地址和端口进行转换，即用户请求的端口和真实服务器的端口可以不同。

#### LVS DR模式

LVS DR（Direct Routing）模式用直接路由技术实现，通过改写请求报文的MAC地址将请求发送给真实服务器，具体的实现流程如图6-10所示。

![image-20200417054811645](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054811645.png)

LVD DR模式是局域网中经常被用到的一种模式，其报文转发流程如下。

（1）客户端将请求发送给LVS，请求报文的源地址是CIP，目标地址是VIP。

（2）LVS在收到报文后，发现请求在规则中存在，则将客户端请求报文的源MAC地址改为自己的DIP（Direct IP Address，内部转发IP）的MAC地址，将目标MAC改为RIP的MAC地址，并将此包发送给真实服务器。

（3）真实服务器在收到请求后发现请求报文中的目标MAC是自己，就会将此报文接收下来，在处理完请求报文后，将响应报文通过lo（回环路由）接口发送给eth0网卡，并最终发送给客户端。

#### LVS TUN模式

TUN（IP Tunneling）通过IP隧道技术实现，具体的实现流程如图6-11所示。

![image-20200417054845438](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054845438.png)

LVS TUN模式常用于跨网段或跨机房的负载均衡，具体的报文转发流程如下。

（1）客户端将请求发送给前端的LVS，请求报文的源地址是CIP，目标地址是VIP。

（2）LVS在收到报文后，发现请求在规则里中存在，则将在客户端请求报文的首部再封装一层IP报文，将源地址改为DIP，将目标地址改为RIP，并将此包发送给真实服务器。

（3）真实服务器在收到请求报文后会先拆开第1层封装，因为发现里面还有一层IP首部的目标地址是自己lo接口上的VIP，所以会处理该请求报文，并将响应报文通过lo接口发送给eth0网卡，并最终发送给客户端。

TUN模式的特点如下。

◎ UNNEL模式需要设置lo接口的VIP不能在公网上出现。

◎ TUNNEL模式必须在所有的真实服务器上绑定VIP的IP地址。

◎ TUNNEL模式中VIP→真实服务器的包通信通过TUNNEL隧道技术实现，不管是内网还是外网都能通信，所以不需要LVS和真实服务器在同一个网段内。

◎ 在TUNNEL模式中，真实服务器会把响应报文直接发送给客户端而不经过LVS，负载能力较强。

◎ TUNNEL模式采用的是隧道模式，使用方法相对复杂，一般用于跨机房LVS实现，并且需要所有服务器都支持IP Tunneling或IP Encapsulation协议。

#### LVS FULLNAT模式

无论是DR模式还是NAT模式，都要求LVS和真实服务器在同一个VLAN下，否则LVS无法作为真实服务器的网关，因此跨VLAN的真实服务器无法接入。同时，在流量增大、真实服务器水平扩容时，单点LVS会成为瓶颈。

FULLNAT能够很好地解决LVS和真实服务器跨VLAN的问题，在跨VLAN问题解决后，LVS和真实服务器不再存在VLAN上的从属关系，可以做到多个LVS对应多个真实服务器，解决水平扩容的问题。FULLNAT的原理是在NAT的基础上引入Local Address IP（内网IP地址），将CIP→VIP转换为LIP→RIP，而LIP和RIP均为IDC内网IP，可以通过交换机实现跨VLAN通信。FULLNAT的具体实现流程如图6-12所示。

![image-20200417054936683](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417054936683.png)

LVS FULLNAT具体的报文转发流程如下。

（1）客户端将请求发送给LVS的DNAT，请求报文的源地址是CIP，目标地址是VIP。

（2）LVS在收到数据后将源地址CIP修改成LIP（Local IP Address, LVS的内网IP），将目标地址VIP修改为RIP，并将数据发送到真实服务器。多个LIP在同一个IDC数据中心，可以通过交换机跨VLAN通信。

（3）真实服务器在收到数据包并处理完成后，将目标地址修改为LIP，将源地址修改为RIP，最终将这个数据包返回给LVS。

（4）LVS在收到数据包后，将数据包中的目标地址修改为CIP，将源地址修改为VIP，并将数据发送给客户端。

### DAO

DAO（Data Access Object，数据访问对象）的主要功能就是操作数据库，也就是数据的增删改查。

DAO的设计流程（包括6个部分）如下。

1. DatabaseConnection

设计一个专门负责打开连接数据库和关闭数据库操作的类。命名规则：×××.dbc.DatabaseConnection。

2. VO

设计VO（值对象），其主要由属性、setter和getter方法构成，与数据库中的字段项对应。命名规则：×××.vo.ttt；ttt要与数据库中的表的名字一致。

3. DAO

定义一系列的原子性操作，如增删改查等操作的接口。命名规则：×××.dao.I×××.DAO。

4. Impl

设计DAO接口的真正实现的类，完成具体的数据库操作。但是不再去负责数据库的打开和关闭。命名规则：×××.dao.impl.×××DAOImpl。

5. Proxy

Proxy代理类的实现：主要将以上4部分合起来，完成整个操作过程。命名规则：×××.dao.proxy.×××Proxy。

6. Factory

Factory类主要用来获得一个DAO类的实例对象。命名规则：×××.factory.DAOFactory。



## NIO

### NIO

java.nio全称java non-blocking IO（实际上是 new io），是指JDK 1.4 及以上版本里提供的新api（New IO） ，为所有的原始类型（boolean类型除外）提供[缓存](https://baike.baidu.com/item/缓存/100710)支持的数据容器，使用它可以提供非阻塞式的高伸缩性网络。

#### NIO的核心实现

在标准IO API中，你可以操作字节流和字符流，但在新IO中，你可以操作通道和缓冲，数据总是从通道被读取到缓冲中或者从缓冲写入到通道中。

NIO核心API Channel, Buffer, Selector

#### 通道Channel

NIO的通道类似于流，但有些区别如下：

\1. 通道可以同时进行读写，而流只能读或者只能写

\2. 通道可以实现异步读写数据

\3. 通道可以从缓冲读数据，也可以写数据到缓冲: 

![img](https://upload-images.jianshu.io/upload_images/13957164-c970757320fbbc30.png?imageMogr2/auto-orient/strip|imageView2/2/w/338/format/webp)

#### 缓存Buffer

缓冲区本质上是一个可以写入数据的内存块，然后可以再次读取，该对象提供了一组方法，可以更轻松地使用内存块，使用缓冲区读取和写入数据通常遵循以下四个步骤：

\1. 写数据到缓冲区；

\2. 调用buffer.flip()方法；

\3. 从缓冲区中读取数据；

\4. 调用buffer.clear()或buffer.compat()方法；

当向buffer写入数据时，buffer会记录下写了多少数据，一旦要读取数据，需要通过flip()方法将Buffer从写模式切换到读模式，在读模式下可以读取之前写入到buffer的所有数据，一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。

**Buffer在与Channel交互时，需要一些标志:**

buffer的大小/容量 - **Capacity**

作为一个内存块，Buffer有一个固定的大小值，用参数capacity表示。

当前读/写的位置 - **Position**

当写数据到缓冲时，position表示当前待写入的位置，position最大可为capacity – 1；当从缓冲读取数据时，position表示从当前位置读取。

信息末尾的位置 - **limit**

在写模式下，缓冲区的limit表示你最多能往Buffer里写多少数据； 写模式下，limit等于Buffer的capacity，意味着你还能从缓冲区获取多少数据。

下图展示了buffer中三个关键属性capacity，position以及limit在读写模式中的说明：

![image-20200411113951096](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411113951096.png)

缓冲区常用的操作

**向缓冲区写数据：**

  \1. 从Channel写到Buffer；

  \2. 通过Buffer的put方法写到Buffer中；

**从缓冲区读取数据：**

  \1. 从Buffer中读取数据到Channel；

  \2. 通过Buffer的get方法从Buffer中读取数据；

**flip方法：**

   将Buffer从写模式切换到读模式，将position值重置为0，limit的值设置为之前position的值；

**clear方法 vs compact方法：**

​    clear方法清空缓冲区；compact方法只会清空已读取的数据，而还未读取的数据继续保存在Buffer中；

#### Selector

一个组件，可以检测多个NIO channel，看看读或者写事件是否就绪。

多个Channel以事件的方式可以注册到同一个Selector，从而达到用一个线程处理多个请求成为可能。

![img](https://upload-images.jianshu.io/upload_images/13957164-539fcf908e51b229.png?imageMogr2/auto-orient/strip|imageView2/2/w/783/format/webp)

![img](https://upload-images.jianshu.io/upload_images/13957164-3d8041ee5b735b73.png?imageMogr2/auto-orient/strip|imageView2/2/w/462/format/webp)

可供选择器监控的通道IO事件类型，包括以下四种：（1）可读：SelectionKey.OP_READ（2）可写：SelectionKey.OP_WRITE（3）连接：SelectionKey.OP_CONNECT（4）接收：SelectionKey.OP_ACCEPT

### 缓冲区

#### Socket编程

Socket技术基于TCP/IP，提前了解一些协议的知识更有利于学习Socket。但是TCP/IP规范非常复杂，我们不可能把该协议的所有细节都掌握，只需要掌握TCP与UDP里常规的内容即可，因为这是Socket技术实现网络通信主要使用的协议。是否有书籍把TCP/UDP的理论知识和Socket编程结合起来？真的有这样的书，推荐《UNIX网络编程（卷1）：套接字联网API》和《UNIX网络编程（卷2）：进程间通信》。这两本书就将TCP/UDP/Socket进行整合并介绍，对TCP和UDP的细节进行文字讲述，并且使用Socket API进行代码的演示，但是演示的代码使用的是C语言实现的，并不是Java，但Java程序员可以以这两本书作为TCP/UDP理论知识的参考。如果你想更加深入、细致地研究TCP/IP编程，这两本书会提供很大帮助。

Socket编程其实就是实现服务端与客户端的数据通信，不管使用任何的编程语言，在实现上基本上都是4个步骤：①建立连接；②请求连接；③回应数据；④结束连接，这4个步骤的流程图如图1-3所示。

![image-20200326013102838](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200326013102838.png)

#### NIO概述

常规的I/O（如InputStream和OutputStream）存在很大的缺点，就是它们是阻塞的，而NIO解决的就是常规I/O执行效率低的问题。即采用非阻塞高性能运行的方式来避免出现以前“笨拙”的同步I/O带来的低效率问题。NIO在大文件操作上相比常规I/O更加优秀，对常规I/O使用的byte[]或char[]进行封装，采用ByteBuffer类来操作数据，再结合针对File或Socket技术的Channel，采用同步非阻塞技术实现高性能处理。

#### 缓冲区介绍

在使用传统的I/O流API时，如InputStream和OutputStream，以及Reader和Writer联合使用时，常常把字节流中的数据放入byte[]字节数组中，或把字符流中的数据放入char[]字符数组中，也可以从byte[]或char[]数组中获取数据来实现功能上的需求，但由于在Java语言中对array数组自身进行操作的API非常少，常用的操作仅仅是length属性和下标[x]了，在JDK中也没有提供更加方便操作数组中数据的API，如果对数组中的数据进行高级处理，需要程序员自己写代码进行实现，处理的方式是比较原始的，这个问题可以使用NIO技术中的缓冲区Buffer类来解决，它提供了很多工具方法，大大提高了程序开发的效率。

从Buffer类的Java文档中可以发现，Buffer类是一个抽象类，它具有7个直接子类，分别是ByteBuffer、CharBuffer、DoubleBuffer、FloatBuffer、IntBuffer、LongBuffer、ShortBuffer，也就是缓冲区中存储的数据类型并不像普通I/O流只能存储byte或char数据类型，Buffer类能存储的数据类型是多样的。

####  Buffer类的使用

在NIO技术的缓冲区中，存在4个核心技术点，分别是：

❑capacity（容量）

❑limit（限制）

❑position（位置）

❑mark（标记）

这4个技术点之间值的大小关系如下：0≤mark≤position≤limit≤capacity

capacity，它代表包含元素的数量。缓冲区的capacity不能为负数，并且capacity也不能更改。

什么是限制呢？缓冲区中的限制代表第一个不应该读取或写入元素的index（索引）。缓冲区的限制（limit）不能为负，并且limit不能大于其capacity。如果position大于新的limit，则将position设置为新的limit。如果mark已定义且大于新的limit，则丢弃该mark。

什么是位置呢？它代表“下一个”要读取或写入元素的index（索引），缓冲区的position（位置）不能为负，并且position不能大于其limit。如果mark已定义且大于新的position，则丢弃该mark。

标记有什么作用呢？缓冲区的标记是一个索引，在调用reset()方法时，会将缓冲区的position位置重置为该索引。标记（mark）并不是必需的。定义mark时，不能将其定义为负数，并且不能让它大于position。如果定义了mark，则在将position或limit调整为小于该mark的值时，该mark被丢弃，丢弃后mark的值是-1。如果未定义mark，那么调用reset()方法将导致抛出InvalidMarkException异常。

1）缓冲区的capacity不能为负数，缓冲区的limit不能为负数，缓冲区的position不能为负数。

2）position不能大于其limit。

3）limit不能大于其capacity。

4）如果定义了mark，则在将position或limit调整为小于该mark的值时，该mark被丢弃。

5）如果未定义mark，那么调用reset()方法将导致抛出InvalidMarkException异常。

6）如果position大于新的limit，则position的值就是新limit的值。

7）当limit和position值一样时，在指定的position写入数据时会出现异常，因为此位置是被限制的。

| limit(), limit(10)等                    | 其中读取和设置这4个属性的方法的命名和jQuery中的val(),val(10)类似，一个负责get，一个负责set |
| --------------------------------------- | ------------------------------------------------------------ |
| reset()                                 | 把position设置成mark的值，相当于之前做过一个标记，现在要退回到之前标记的地方 |
| clear()                                 | position = 0;limit = capacity;mark = -1; 有点初始化的味道，但是并不影响底层byte数组的内容 |
| flip()                                  | limit = position;position = 0;mark = -1; 翻转，也就是让flip之后的position到limit这块区域变成之前的0到position这块，翻转就是将一个处于存数据状态的缓冲区变为一个处于准备取数据的状态 |
| rewind()                                | 把position设为0，mark设为-1，不改变limit的值                 |
| remaining()                             | return limit - position;返回limit和position之间相对位置差    |
| hasRemaining()                          | return position < limit返回是否还有未读内容                  |
| compact()                               | 把从position到limit中的内容移到0到limit-position的区域内，position和limit的取值也分别变成limit-position、capacity。如果先将positon设置到limit，再compact，那么相当于clear() |
| get()                                   | 相对读，从position位置读取一个byte，并将position+1，为下次读写作准备 |
| get(int index)                          | 绝对读，读取byteBuffer底层的bytes中下标为index的byte，不改变position |
| get(byte[] dst, int offset, int length) | 从position位置开始相对读，读length个byte，并写入dst下标从offset到offset+length的区域 |
| put(byte b)                             | 相对写，向position的位置写入一个byte，并将postion+1，为下次读写作准备 |
| put(int index, byte b)                  | 绝对写，向byteBuffer底层的bytes中下标为index的位置插入byte b，不改变position |
| put(ByteBuffer src)                     | 用相对写，把src中可读的部分（也就是position到limit）写入此byteBuffer |
| put(byte[] src, int offset, int length) | 从src数组中的offset到offset+length区域读取数据并使用相对写写入此byteBuffer |

#### ByteBuffer类的使用

ByteBuffer类是Buffer类的子类，可以在缓冲区中以字节为单位对数据进行存取，而且它也是比较常用和重要的缓冲区类。在使用NIO技术时，有很大的概率使用ByteBuffer类来进行数据的处理。

ByteBuffer类提供了6类操作。

1）以绝对位置和相对位置读写单个字节的get()和put()方法。

2）使用相对批量get(byte[] dst)方法可以将缓冲区中的连续字节传输到byte[] dst目标数组中。

3）使用相对批量put(byte[] src)方法可以将byte[]数组或其他字节缓冲区中的连续字节存储到此缓冲区中。

4）使用绝对和相对getType和putType方法可以按照字节顺序在字节序列中读写其他基本数据类型的值，方法getType和putType可以进行数据类型的自动转换。

5）提供了创建视图缓冲区的方法，这些方法允许将字节缓冲区视为包含其他基本类型值的缓冲区，这些方法有asCharBuffer()、asDoubleBuffer()、asFloatBuffer()、asIntBuffer()、asLongBuffer()和asShortBuffer()。

6）提供了对字节缓冲区进行压缩（compacting）、复制（duplicating）和截取（slicing）的方法。

### 通道和FileChannel

#### 通道（Channel）

通道主要就是用来传输数据的通路。

在JDK 1.8版本中，Channel接口具有11个子接口，它们列表如下：

1）AsynchronousChannel

2）AsynchronousByteChannel

3）ReadableByteChannel

4）ScatteringByteChannel

5）WritableByteChannel

6）GatheringByteChannel

7）ByteChannel

8）SeekableByteChannel

9）NetworkChannel

10）MulticastChannel

11）InterruptibleChannel

####  FileChannel

FileChannel类的主要作用是读取、写入、映射和操作文件的通道。该通道永远是阻塞的操作。

FileChannel类在内部维护当前文件的position，可对其进行查询和修改。该文件本身包含一个可读写、长度可变的字节序列，并且可以查询该文件的当前大小。当写入的字节超出文件的当前大小时，则增加文件的大小；截取该文件时，则减小文件的大小。文件可能还有某个相关联的元数据，如访问权限、内容类型和最后的修改时间，但此类未定义访问元数据的方法。

### 选择器的使用

#### 选择器

Selector选择器是NIO技术中的核心组件，可以将通道注册进选择器中，其主要作用就是使用1个线程来对多个通道中的已就绪通道进行选择，然后就可以对选择的通道进行数据处理，属于一对多的关系，也就是使用1个线程来操作多个通道，这种机制在NIO技术中称为“I/O多路复用”。它的优势是可以节省CPU资源，因为只有1个线程，CPU不需要在不同的线程间进行上下文切换。线程的上下文切换是一个非常耗时的动作，减少切换对设计高性能服务器具有很重要的意义。

#### Selector类

Selector类是抽象类，它是SelectableChannel对象的多路复用器。这句话的含义是只有SelectableChannel通道对象才能被Selector.java选择器所复用，那么为什么必须是SelectableChannel通道对象才能被Selector.java选择器所复用呢？因为只有SelectableChannel类才具有register(Selector sel, int ops)方法，该方法的作用是将当前的SelectableChannel通道注册到指定的选择器中，参数sel也说明了这个问题。

SelectableChannel类可以通过选择器实现多路复用。

SelectableChannel在多线程并发环境下是安全的。

#### Selector类的使用

通过SelectionKey对象来表示SelectableChannel（可选择通道）到选择器的注册。选择器维护了3种SelectionKey-Set（选择键集）。

1）键集：包含的键表示当前通道到此选择器的注册，也就是通过某个通道的register()方法注册该通道时，所带来的影响是向选择器的键集中添加了一个键。此集合由keys()方法返回。键集本身是不可直接修改的。

2）已选择键集：在首先调用select()方法选择操作期间，检测每个键的通道是否已经至少为该键的相关操作集所标识的一个操作准备就绪，然后调用selectedKeys()方法返回已就绪键的集合。已选择键集始终是键集的一个子集。

3）已取消键集：表示已被取消但其通道尚未注销的键的集合。不可直接访问此集合。已取消键集始终是键集的一个子集。在select()方法选择操作期间，从键集中移除已取消的键。

#### SelectionKey类的使用

SelectionKey类表示SelectableChannel在选择器中的注册的标记。

在每次向选择器注册通道时，就会创建一个选择键（SelectionKey）。通过调用某个键的cancel()方法、关闭其通道，或者通过关闭其选择器取消该键之前，通道一直保持有效。取消某个键不会立即从其选择器中移除它，而是将该键添加到选择器的已取消键集，以便在下一次进行select()方法操作时移除它。可通过调用某个键的isValid()方法来测试其有效性。

选择键包含两个集，是表示为整数值的操作集，其中每一位都表示该键通道所支持的一类可选择操作。

1）interest集，确定了下一次调用某个选择器的select()方法时，将测试哪类操作的准备就绪信息。创建该键时使用给定的值初始化interest集合，之后可通过interestOps(int)方法对其进行更改。

2）ready集，标识了这样一类操作，即某个键的选择器检测到该键的通道已为此类操作准备就绪。在创建该键时，ready集初始化为零，可以在之后的select()方法操作中通过选择器对其进行更新，但不能直接更新它。

### 磁盘I/O优化

1.性能检测

我们可以压力测试应用程序看系统的I/O wait指标是否正常，例如，测试机器有4个CPU，那么理想的I/O wait参数不应该超过25%，如果超过25%，I/O很可能成为应用程序的性能瓶颈。Linux操作系统下可以通过iostat命令查看。

通常我们在判断I/O性能时还会看另外一个参数，就是IOPS，应用程序需要的最低的IOPS是多少，磁盘的IOPS能不能达到要求。每个磁盘的IOPS通常在一个范围内，这和存储在磁盘上的数据块的大小和访问方式也有关，但主要是由磁盘的转速决定的，磁盘的转速越高磁盘的IOPS也越高。

现在为了提高磁盘I/O的性能，通常采用一种叫RAID的技术，就是将不同的磁盘组合起来以提高I/O性能，目前有多种RAID技术，每种RAID技术对I/O性能的提升会有不同，可以用一个RAID因子来代表，磁盘的读写吞吐量可以通过iostat命令来获取，于是可以计算出一个理论的IOPS值，计算公式如下：( 磁盘数 × 每块磁盘的IOPS ) / ( 磁盘读的吞吐量 + RAID因子 × 磁盘写的吞吐量 ) = IOPS

2.提升I/O性能

通常提升磁盘I/O性能的方法有：

◎ 增加缓存，减少磁盘访问次数。

◎ 优化磁盘的管理系统，设计最优的磁盘方式策略，以及磁盘的寻址策略，这是在底层操作系统层面考虑的。

◎ 设计合理的磁盘存储数据块，以及访问这些数据块的策略，这是在应用层面考虑的。例如，我们可以给存放的数据设计索引，通过寻址索引来加快和减少磁盘的访问量，还可以采用异步和非阻塞的方式加快磁盘的访问速度。

◎ 应用合理的RAID策略提升磁盘IO，RAID策略及说明如表2-3所示。

![image-20200411114213904](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411114213904.png)

### TCP网络参数调优

要能够建立一个TCP连接，必须知道对方的IP和一个未被使用的端口号，由于32位操作系统的端口号通常由两个字节表示，也就是只有216=65535个，所以一台主机能够同时建立的连接数是有限的，当然操作系统还有一些端口0~1024是受保护的，如80端口、22端口，这些端口都不能被随意占用。

![image-20200411114310225](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411114310225.png)

### 网络I/O优化

网络I/O优化通常有如下一些基本处理原则。

**减少网络交互的次数。**要减少网络交互的次数通常需要在网络交互的两端设置缓存，如oracle的jdbc驱动程序就提供了对查询的SQL结果的缓存，在客户端和数据库端都有，可以有效地减少对数据库的访问。除了设置缓存还有一个办法，即合并访问请求，如在查询数据库时，我们要查10个id，我可以每次查一个id，也可以一次查10个id。再比如，在访问一个页面时通常会有多个JS或CSS的文件，我们可以将多个JS文件合并在一个HTTP链接中，每个文件用逗号隔开，然后发送到后端Web服务器，根据这个URL链接再拆分出各个文件，最后打包再一并发回给前端浏览器。这些都是常用的减少网络I/O的办法。

**减少网络传输数据量的大小。**减少网络数据量的办法通常是将数据压缩后再传输，如在HTTP请求中，通常Web服务器将请求的Web页面gzip压缩后再传输给浏览器。还有就是通过设计简单的协议，尽量通过读取协议头来获取有用的价值信息，如在设计代理程序时，4层代理和7层代理都是在尽量避免要读取整个通信数据来取得需要的信息。

**尽量减少编码。**通常在网络I/O中数据传输都是以字节形式进行的，也就是说通常要序列化。但是我们发送的要传输的数据都是字符形式的，从字符到字节必须编码。但是这个编码过程是比较耗时的，所以在要经过网络I/O传输时，尽量直接以字节形式发送，也就是尽量提前将字符转化为字节，或者减少字符到字节的转化过程。

根据应用场景设计合适的交互方式。所谓的交互场景主要包括同步与异步、阻塞与非阻塞方式，下面详细介绍。

![image-20200411114422340](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411114422340.png)

## Web请求过程

### B/S网络架构概述

B/S网络架构从前端到后端都得到了简化，都基于统一的应用层协议HTTP来交互数据，与大多数传统C/S互联网应用程序采用的长连接的交互模式不同，HTTP协议采用无状态的短连接的通信方式，通常情况下，一次请求就完成了一次数据交互，通常也对应一个业务逻辑，然后这次通信连接就断开了。采用这种方式是为了能够同时服务更多的用户，因为当前互联网应用每天都会处理上亿的用户请求，不可能每个用户访问一次后就一直保持住这个连接。

基于HTTP协议本身的特点，目前的B/S网络架构大都采用类似于图1-1所示的架构设计，既要满足海量用户的访问请求，又要保持用户请求的快速响应，所以现在的网络架构也越来越复杂。

![image-20200411110000788](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411110000788.png)

### HTTP协议解析

![image-20200411110141918](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411110141918.png)

![image-20200411110154759](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411110154759.png)

![image-20200411110208525](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411110208525.png)

### DNS域名解析

![image-20200411110252933](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411110252933.png)

当用户在浏览器中输入域名并按下回车键后，第1步，浏览器会检查缓存中有没有这个域名对应的解析过的IP地址，如果缓存中有，这个解析过程就将结束。浏览器缓存域名也是有限制的，不仅浏览器缓存大小有限制，而且缓存的时间也有限制，通常情况下为几分钟到几小时不等，域名被缓存的时间限制可以通过TTL属性来设置。这个缓存时间太长和太短都不好，如果缓存时间太长，一旦域名被解析到的IP有变化，会导致被客户端缓存的域名无法解析到变化后的IP地址，以致该域名不能正常解析，这段时间内有可能会有一部分用户无法访问网站。如果时间设置太短，会导致用户每次访问网站都要重新解析一次域名。

2步，如果用户的浏览器缓存中没有，浏览器会查找操作系统缓存中是否有这个域名对应的DNS解析结果。其实操作系统也会有一个域名解析的过程，在Windows中可以通过C:\Windows\System32\drivers\etc\hosts文件来设置，你可以将任何域名解析到任何能够访问的IP地址。如果你在这里指定了一个域名对应的IP地址，那么浏览器会首先使用这个IP地址。例如，我们在测试时可以将一个域名解析到一台测试服务器上，这样不用修改任何代码就能测试到单独服务器上的代码的业务逻辑是否正确。正是因为有这种本地DNS解析的规程，所以黑客就有可能通过修改你的域名解析来把特定的域名解析到它指定的IP地址上，导致这些域名被劫持。

第3步，如何、怎么知道域名服务器呢？在我们的网络配置中都会有“DNS服务器地址”这一项，这个地址就用于解决前面所说的如果两个过程无法解析时要怎么办，操作系统会把这个域名发送给这里设置的LDNS，也就是本地区的域名服务器。这个DNS通常都提供给你本地互联网接入的一个DNS解析服务，例如你是在学校接入互联网，那么你的DNS服务器肯定在你的学校，如果你是在一个小区接入互联网的，那这个DNS就是提供给你接入互联网的应用提供商，即电信或者联通，也就是通常所说的SPA，那么这个DNS通常也会在你所在城市的某个角落，通常不会很远。

第4步，如果LDNS仍然没有命中，就直接到Root Server域名服务器请求解析。

第5步，根域名服务器返回给本地域名服务器一个所查询域的主域名服务器（gTLD Server）地址。gTLD是国际顶级域名服务器，如.com、.cn、.org等，全球只有13台左右。

第6步，本地域名服务器（Local DNS Server）再向上一步返回的gTLD服务器发送请求。

第7步，接受请求的gTLD服务器查找并返回此域名对应的Name Server域名服务器的地址，这个Name Server通常就是你注册的域名服务器，例如你在某个域名服务提供商申请的域名，那么这个域名解析任务就由这个域名提供商的服务器来完成。第8步，Name Server域名服务器会查询存储的域名和IP的映射关系表，正常情况下都根据域名得到目标IP记录，连同一个TTL值返回给DNS Server域名服务器。

第8步，Name Server域名服务器会查询存储的域名和IP的映射关系表，正常情况下都根据域名得到目标IP记录，连同一个TTL值返回给DNS Server域名服务器。

第9步，返回该域名对应的IP和TTL值，Local DNS Server会缓存这个域名和IP的对应关系，缓存的时间由TTL值控制。

第10步，把解析的结果返回给用户，用户根据TTL值缓存在本地系统缓存中，域名解析过程结束。

域名解析记录主要分为A记录、MX记录、CNAME记录、NS记录和TXT记录。

◎ A记录，A代表的是Address，用来指定域名对应的IP地址，如将item.taobao.com指定到115.238.23.241，将switch.taobao.com指定到121.14.24.241。A记录可以将多个域名解析到一个IP地址，但是不能将一个域名解析到多个IP地址。

◎ MX记录，表示的是Mail Exchange，就是可以将某个域名下的邮件服务器指向自己的，如taobao.com域名的A记录IP地址是115.238.25.245，如果MX记录设置为115.238.25.246，是xxx@taobao.com的邮件路由，DNS会将邮件发送到115.238.25.246所在的服务器，而正常通过Web请求的话仍然解析到A记录的IP地址。

◎ CNAME记录，全称是Canonical Name（别名解析）。所谓的别名解析就是可以为一个域名设置一个或者多个别名。如将taobao.com解析到xulingbo.net，将srcfan.com也解析到xulingbo.net。其中xulingbo.net分别是taobao.com和srcfan.com的别名。前面的跟踪域名解析中的“www.taobao.com. 1542 IN CNAME www.gslb.taobao.com”就是CNAME解析。◎ NS记录，为某个域名指定DNS解析服务器，也就是这个域名有指定的IP地址的DNS服务器去解析，前面的“gslb.taobao.com. 86400 IN NS gslbns2.taobao. com.”就是NS解析。

◎ NS记录，为某个域名指定DNS解析服务器，也就是这个域名有指定的IP地址的DNS服务器去解析，前面的“gslb.taobao.com. 86400 IN NS gslbns2.taobao. com.”就是NS解析。

◎ TXT记录，为某个主机名或域名设置说明，如可以为xulingbo.net设置TXT记录为“君山的博客|许令波”这样的说明。

### CDN工作机制

CDN也就是内容分布网络（Content Delivery Network），它是构筑在现有Internet上的一种先进的流量分配网络。其目的是通过在现有的Internet中增加一层新的网络架构，将网站的内容发布到最接近用户的网络“边缘”，使用户可以就近取得所需的内容，提高用户访问网站的响应速度。有别于镜像，它比镜像更智能，可以做这样一个比喻：CDN =镜像（Mirror）+ 缓存（Cache）+ 整体负载均衡（GSLB）。因而，CDN可以明显提高Internet中信息流动的效率。

通常来说CDN要达到以下几个目标。

◎ 可扩展（Scalability）。性能可扩展性：应对新增的大量数据、用户和事务的扩展能力；成本可扩展性：用低廉的运营成本提供动态的服务能力和高质量的内容分发。

◎ 安全性（Security）。强调提供物理设备、网络、软件、数据和服务过程的安全性，（趋势）减少因为DDoS攻击或者其他恶意行为造成商业网站的业务中断。

◎ 可靠性、响应和执行（Reliability、Responsiveness和Performance）。服务可用性，能够处理可能的故障和用户体验的下降，通过负载均衡及时提供网络的容错机制。

![image-20200411111654128](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200411111654128.png)

