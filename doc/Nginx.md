## Nginx

### Nginx

Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。 Nginx 主要提供反向代理、负载均衡、动静分离(静态资源服务)等服务。

### 正向代理

正向代理，意思是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端才能使用正向代理。

### 反向代理

反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为Web加速，即使用反向代理作为Web服务器的前置机来降低网络和服务器的负载，提高访问效率。

### 负载均衡算法

源地址哈希法：根据获取客户端的IP地址，通过哈希函数计算得到一个数值，用该数值对服务器列表的大小进行取模运算，得到的结果便是客服端要访问服务器的序号。采用源地址哈希法进行负载均衡，同一IP地址的客户端，当后端服务器列表不变时，它每次都会映射到同一台后端服务器进行访问。

轮询法：将请求按顺序轮流地分配到后端服务器上，它均衡地对待后端的每一台服务器，而不关心服务器实际的连接数和当前的系统负载。

随机法：通过系统的随机算法，根据后端服务器的列表大小值来随机选取其中的一台服务器进行访问。

加权轮询法：不同的后端服务器可能机器的配置和当前系统的负载并不相同，因此它们的抗压能力也不相同。给配置高、负载低的机器配置更高的权重，让其处理更多的请；而配置低、负载高的机器，给其分配较低的权重，降低其系统负载，加权轮询能很好地处理这一问题，并将请求顺序且按照权重分配到后端。

加权随机法：与加权轮询法一样，加权随机法也根据后端机器的配置，系统的负载分配不同的权重。不同的是，它是按照权重随机请求后端服务器，而非顺序。

最小连接数法：由于后端服务器的配置不尽相同，对于请求的处理有快有慢，最小连接数法根据后端服务器当前的连接情况，动态地选取其中当前积压连接数最少的一台服务器来处理当前的请求，尽可能地提高后端服务的利用效率，将负责合理地分流到每一台服务器。

### **如何实现高并发**

一个主进程，多个工作进程，每个工作进程可以处理多个请求
每进来一个request，会有一个worker进程去处理。但不是全程的处理，处理到可能发生阻塞的地方，比如向上游（后端）服务器转发request，并等待请求返回。那么，这个处理的worker继续处理其他请求，而一旦上游服务器返回了，就会触发这个事件，worker才会来接手，这个request才会接着往下走。
由于web server的工作性质决定了每个request的大部份生命都是在网络传输中，实际上花费在server机器上的时间片不多。这是几个进程就解决高并发的秘密所在。即@skoo所说的webserver刚好属于网络io密集型应用，不算是计算密集型。

### Nginx模块

Nginx有五大优点：**模块化、事件驱动、异步、非阻塞、多进程单线程**。由内核和模块组成的，其中内核完成的工作比较简单，仅仅**通过查找配置文件将客户端请求映射到一个location block，然后又将这个location block中所配置的每个指令将会启动不同的模块去完成相应的工作。**

### 模块划分

**Nginx的模块从结构上分为核心模块、基础模块和第三方模块：**

> **核心模块：**HTTP模块、EVENT模块和MAIL模块
>
> **基础模块：**HTTP Access模块、HTTP FastCGI模块、HTTP Proxy模块和HTTP Rewrite模块，
>
> **第三方模块：**HTTP Upstream Request Hash模块、Notice模块和HTTP Access Key模块。

**Nginx的模块从功能上分为如下四类：**

> **Core(核心模块)：**构建nginx基础服务、管理其他模块。
>
> **Handlers（处理器模块）：**此类模块直接处理请求，并进行输出内容和修改headers信息等操作。
>
> **Filters （过滤器模块）：**此类模块主要对其他处理器模块输出的内容进行修改操作，最后由Nginx输出。
>
> **Proxies （代理类模块）：**此类模块是Nginx的HTTP Upstream之类的模块，这些模块主要与后端一些服务比如FastCGI等进行交互，实现服务代理和负载均衡等功能。

**Nginx的核心模块主要负责建立nginx服务模型、管理网络层和应用层协议、以及启动针对特定应用的一系列候选模块。其他模块负责分配给web服务器的实际工作：**

> (1) 当Nginx发送文件或者转发请求到其他服务器，由Handlers(处理模块)或Proxies（代理类模块）提供服务；
>
> (2) 当需要Nginx把输出压缩或者在服务端加一些东西，由Filters(过滤模块)提供服务。

### **Nginx模块处理流程**



![img](https://upload-images.jianshu.io/upload_images/6807865-c9dfe26ee351020e.png?imageMogr2/auto-orient/strip|imageView2/2/w/890/format/webp)

### **Nginx架构**

![img](https://upload-images.jianshu.io/upload_images/6807865-214f029b0fb1d28c.png?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)



### **Nginx 工作原理**

Nginx本身做的工作实际很少，当它接到一个HTTP请求时，它仅仅是通过查找配置文件将此次请求映射到一个location block，而此location中所配置的各个指令则会启动不同的模块去完成工作，因此模块可以看做Nginx真正的劳动工作者。通常一个location中的指令会涉及一个handler模块和多个filter模块（当然，多个location可以复用同一个模块）。handler模块负责处理请求，完成响应内容的生成，而filter模块对响应内容进行处理。

### **Nginx进程模型**

Nginx默认采用多进程工作方式，Nginx启动后，会运行一个master进程和多个worker进程。其中master充当整个进程组与用户的交互接口，同时对进程进行监护，管理worker进程来实现重启服务、平滑升级、更换日志文件、配置文件实时生效等功能。worker用来处理基本的网络事件，worker之间是平等的，他们共同竞争来处理来自客户端的请求。

![img](https://images2017.cnblogs.com/blog/1183448/201802/1183448-20180210145226654-1347579045.png)

### **处理HTTP请求流程**

http请求是典型的请求-响应类型的的网络协议。http是文件协议，所以我们在分析请求行与请求头，以及输出响应行与响应头，往往是一行一行的进行处理。通常在一个连接建立好后，读取一行数据，分析出请求行中包含的method、uri、http_version信息。然后再一行一行处理请求头，并根据请求method与请求头的信息来决定是否有请求体以及请求体的长度，然后再去读取请求体。得到请求后，我们处理请求产生需要输出的数据，然后再生成响应行，响应头以及响应体。在将响应发送给客户端之后，一个完整的请求就处理完了。

![img](https://images2017.cnblogs.com/blog/1183448/201802/1183448-20180210145440388-1841568939.png)

### CGI

CGI全称"通用网关接口"（Common Gateway Interface），用于HTTP服务器与其它机器上的程序服务通信交流的一种工具，CGI程序须运行在网络服务器上。

传统CGI接口方式的主要缺点是性能较差，因为每次HTTP服务器遇到动态程序时都需要重启解析器来执行解析，然后结果被返回给HTTP服务器。这在处理高并发访问几乎是不可用的，因此就诞生了FastCGI。另外传统的CGI接口方式安全性也很差。

### FastCGI

FastCGI是一个可伸缩地、高速地在HTTP服务器和动态脚本语言间通信的接口（FastCGI接口在Linux下是socket（可以是文件socket，也可以是ip socket）），主要优点是把动态语言和HTTP服务器分离开来。多数流行的HTTP服务器都支持FastCGI，包括Apache、Nginx和lightpd。

同时，FastCGI也被许多脚本语言所支持，比较流行的脚本语言之一为PHP。FastCGI接口方式采用C/S架构，可以将HTTP服务器和脚本解析服务器分开，同时在脚本解析服务器上启动一个或多个脚本解析守护进程。当HTTP服务器每次遇到动态程序时，可以将其直接交付给FastCGI进程执行，然后将得到的结构返回给浏览器。这种方式可以让HTTP服务器专一地处理静态请求或者将动态脚本服务器的结果返回给客户端，这在很大程度上提高了整个应用系统的性能。

FastCGI的重要特点：

- 1、FastCGI是HTTP服务器和动态脚本语言间通信的接口或者工具。
- 2、FastCGI优点是把动态语言解析和HTTP服务器分离开来。
- 3、Nginx、Apache、Lighttpd以及多数动态语言都支持FastCGI。
- 4、FastCGI接口方式采用C/S架构，分为客户端（HTTP服务器）和服务端（动态语言解析服务器）。
- 5、PHP动态语言服务端可以启动多个FastCGI的守护进程。
- 6、HTTP服务器通过FastCGI客户端和动态语言FastCGI服务端通信。

### Nginx FastCGI的运行原理

Nginx不支持对外部动态程序的直接调用或者解析，所有的外部程序（包括PHP）必须通过FastCGI接口来调用。FastCGI接口在Linux下是socket（可以是文件socket，也可以是ip socket）。为了调用CGI程序，还需要一个FastCGI的wrapper，这个wrapper绑定在某个固定socket上，如端口或者文件socket。当Nginx将CGI请求发送给这个socket的时候，通过FastCGI接口，wrapper接收到请求，然后派生出一个新的线程，这个线程调用解释器或者外部程序处理脚本并读取返回数据；接着，wrapper再将返回的数据通过FastCGI接口，沿着固定的socket传递给Nginx；最后，Nginx将返回的数据发送给客户端，这就是Nginx+FastCGI的整个运作过程。

![img](https://upload-images.jianshu.io/upload_images/5350978-fcd13cc99e17f59a.png?imageMogr2/auto-orient/strip|imageView2/2/w/665/format/webp)

### IPv4的内核7个参数

1. net.core.netdev_max_backlog参数

参数net.core.netdev_max_backlog，表示当每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许发送到队列的数据包的最大数目。一般默认值为128（可能不同的Linux系统该数值也不同）。Nginx服务器中定义的NGX_LISTEN_BACKLOG默认为511。

2. net.core.somaxconn参数

该参数用于调节系统同时发起的TCP连接数，一般默认值为128。在客户端存在高并发请求的情况下，该默认值较小，可能导致链接超时或者重传问题，我们可以根据实际需要结合并发请求数来调节此值。

3. net.ipv4.tcp_max_orphans参数

参数用于设定系统中最多允许存在多少TCP套接字不被关联到任何一个用户文件句柄上。如果超过这个数字，没有与用户文件句柄关联的TCP套件字将立即被复位，同时给出警告信息。这个限制只是为了防止简单的DoS（Denial of Service，拒绝服务）攻击。

4. net.ipv4.tcp_max_syn_backlog参数

该参数用于记录尚未收到客户端确认信息的连接请求的最大值。对于拥有128 MB内存的系统而言，此参数的默认值是1024，对小内存的系统则是128。

5. net.ipv4.tcp_timestamps参数

该参数用于设置时间戳，这可以避免序列号的卷绕。在一个1Gb/s的链路上，遇到以前用过的序列号的概率很大。当此值赋值为0时，禁用对于TCP时间戳的支持。在默认情况下，TCP协议会让内核接受这种“异常”的数据包。

6. net.ipv4.tcp_synack_retries参数

该参数用于设置内核放弃TCP连接之前向客户端发送SYN+ACK包的数量。为了建立对端的连接服务，服务器和客户端需要进行三次握手，第二次握手期间，内核需要发送SYN并附带一个回应前一个SYN的ACK，这个参数主要影响这个过程，一般赋值为1

7. net.ipv4.tcp_syn_retries参数

该参数的作用与上一个参数类似，设置内核放弃建立连接之前发送SYN包的数量

### CPU的Nginx配置优化

1.worker_processes指令

worker_processes指令用来设置Nginx服务的进程数。官方文档建议此指令一般设置为1即可，赋值太多会影响系统的IO效率，降低Nginx服务器的性能。根据笔者的经验，为了让多核CPU能够很好地并行处理任务，我们可以将worker_processes指令的赋值适当地增大一些，最好是赋值为机器CPU的倍数。当然，这个值并不是越大越好，Nginx进程太多可能增加主进程调度负担，也可能影响系统的IO效率。针对双核CPU，建议设置为2或4。

2. worker_cpu_affinity指令

worker_cpu_affinity指令用来为每个进程分配CPU的工作内核。

### 网络连接相关的配置

1. keepalive_timeout指令

该指令用于设置Nginx服务器与客户端保持连接的超时时间。

2.send_timeout指令

该指令用于设置Nginx服务器响应客户端的超时时间，这个超时时间仅针对两个客户端和服务器之间建立连接后，某次活动之间的时间。如果这个时间后客户端没有任何活动，Nginx服务器将会关闭连接

3. client_header_buffer_size指令

指令用于设置Nginx服务器允许的客户端请求头部的缓冲区大小，默认为1KB。

4. multi_accept指令

该指令用于配置Nginx服务器是否尽可能多地接收客户端的网络连接请求，默认值为off。

### 事件驱动模型相关的配置

1. use指令

   use指令用于指定Nginx服务器使用的事件驱动模型。

2. worker_connections指令

该指令用于设置Nginx服务器的每个工作进程允许同时连接客户端的最大数

3. worker_rlimit_sigpending指令

该指令用于设置Linux 2.6.6-mm2版本之后Linux平台的事件信号队列长度上限。

4. devpoll_changes和devpoll_events指令

这两个指令用于设置在/dev/poll事件驱动模式下Nginx服务器可以与内核之间传递事件的数量，前者设置传递给内核的事件数量，后者设置从内核获取的事件数量

5. kqueue_changes和kqueue_events指令

这两个指令用于设置在kqueue事件驱动模式下Nginx服务器可以与内核之间传递事件的数量，前者设置传递给内核的事件数量，后者设置从内核获取的事件数量

6. epoll_events指令

该指令用于设置在epoll事件驱动模式下Nginx服务器可以与内核之间传递事件的数量

7. rtsig_signo指令

该指令用于设置rtsig模式使用的两个信号中的第一个，第二个信号是在第一个信号的编号上加1

8. rtsig_overflow_＊ 指令

该指令代表三个具体的指令，分别为rtsig_overflow_events指令、rtsig_overflow_test指令和rtsig_overflow_threshold指令。

### Nginx服务器正向代理服务

1. resolver指令

该指令用于指定DNS服务器的IP地址。DNS服务器的主要工作是进行域名解析，将域名映射为对应的IP地址。

2. resolver_timeout指令

该指令用于设置DNS服务器域名解析超时时间

3. proxy_pass指令

指令用于设置代理服务器的协议和地址，它不仅仅用于Nginx服务器的代理服务，更主要的是应用于反向代理服务

### Nginx服务器的反向代理服务

1、proxy_pass指令

该指令用来设置被代理服务器的地址，可以是主机名称、IP地址加端口号等形式。其语法结构为：

> proxy_pass URL;

其中，URL为要设置的被代理服务器的地址，包含传输协议、主机名称或IP地址加端口号、URI等要素。传输协议通常是“http”或者“https”。指令同时还接受以“unix”开始的UNIX-domain套接字路径。

2、proxy_hide_header指令

该指令用于设置Nginx服务器在发送HTTP响应时，隐藏一些头域信息。其语法结构为：

> proxy_hide_header field;

其中，field为需要隐藏的头域。该指令可以在http块、server块或者location块中进行配置。

3、proxy_pass_header指令

默认情况下，Nginx服务器在发送响应报文时，报文头中不包含“Date”、“Server”、“X-Accel”等来自被代理服务器的头域信息。该指令可以设置这些头域信息以被发送，其语法结构为：

> proxy_pass_header field;

其中，field为需要发送的头域。该指令可以在http块、server块或者location块中进行配置。

4、proxy_pass_request_body指令

该指令用于配置是否将客户端请求的请求体发送给代理服务器，其语法结构为：

> proxy_pass_request_body on | off;

默认设置为开启（on），开关可以在http块、server块或者location块中进行配置。

5、proxy_pass_request_headers指令

该指令用于配置是否将客户端请求的请求头发送给代理服务器，其语法结构为：

> proxy_pass_request_headers on | off;

默认设置为开启（on），开关可以在http块、server块或者location块中进行配置。

6、proxy_set_header指令

该指令可以更改Nginx服务器接收到客户端请求的请求头信息，然后将新的请求头发送给被代理的服务器，其语法结构为：

> proxy_set_header field value;

field：要更改的信息所在的头域。

value：更改的值，支持使用文本、变量或者变量的组合。

7、proxy_set_body指令

该指令可以更改Nginx服务器接收到的客户端请求的请求体信息，然后将新的请求体发送给被代理的服务器，其语法结构为：

> proxy_set_body value;

其中，value为更改的信息，支持使用文本、变量或者变量的组合。

8、proxy_bind指令

该指令可以强制将与代理主机的连接绑定到指定的IP地址。通俗来讲就是，在配置了多个基于名称或者基于IP的主机的情况下，如果我们希望代理连接由指定的主机处理，就可以使用该指令进行配置，其语法结构为：

> proxy_bind address;

其中，address为指定主机的IP地址

9、proxy_connect_timeout指令

该指令配置Nginx服务器与后端被代理服务器尝试建立连接的超时时间，其语法结构为：

> proxy_connect_timeout time;

其中，time为设置的超时时间，默认为60s。

10、proxy_read_timeout指令

该指令配置Nginx服务器向后端被代理服务器发出read请求后，等待响应的超时时间，其语法结构为：

> proxy_read_timeout time;

其中，time为设置的超时时间，默认为60s。

11、proxy_send_timeout指令

该指令配置Nginx服务器向后端被代理服务器发出write请求后，等待响应的超时时间，其语法结构为：

> proxy_send_timeout time;

其中，time为设置的超时时间，默认为60s。

12、proxy_http_version指令

该指令用于设置用于Nginx服务器提供代理服务的HTTP协议版本，其语法结构为：

> proxy_http_version 1.0 | 1.1;

默认设置为1.0版本。1.1版本支持upsteam服务器组设置中的keepalive指令。

13、proxy_method指令

该指令用于设置Nginx服务器请求被代理服务器时使用的请求方法，一般为POST或者GET。设置了该指令，客户端的请求方式将被忽略，其语法结构为：

> proxy_method method;

其中，method的值可以设置为POST或者GET，注意不加引号。

14、proxy_ignore_client_abort指令

该指令用于设置在客户端中断网络请求时，Nginx服务器是否中断对被代理服务器的请求，其语法结构为：

> proxy_ignore_client_abort on | off;

默认设置为off，当客户端中断网络请求时，Nginx服务器中断对被代理服务器的请求。

15、proxy_ignore_headers指令

该指令用于设置一些HTTP响应头中的头域，Nginx服务器接收到被代理服务器的响应数据后，不会处理被设置的头域，其语法结构为：

> proxy_ignore_headers field ...;

其中，field为要设置的HTTP响应头的头域，例如：“X-Accel-Redirect”、“X-Accel-Expires”、“Expires”、“Cache-Control”或“Set-Cookie”等。

16、proxy_redirect指令

该指令用于修改被代理服务器返回的响应头中的Location头域和Refresh头域，与proxy_pass指令配合使用。比如，Nginx服务器通过proxy_pass指令将客户端的请求地址重写为被代理服务器的地址，那么Nginx服务器返回给客户端的响应头中Location头域显示的地址就应该和客户端发起请求的地址相对应，而不是代理服务器直接返回的地址信息，否则就会出问题。该指令解决了这个问题，可以把代理服务器返回的地址信息更改为需要的地址信息，其语法结构为：

> 结构1：proxy_redirect redirect replacement;
>
> 结构2：proxy_redirect default;
>
> 结构3：proxy_redirect off;

redirect：匹配Location头域值的字符串，支持变量的使用和正则表达式。

replacement：用于替换redirect变量内容的字符串，支持变量的使用。

结构2使用default，代表使用location块的URI变量作为replacement，并使用proxy_pass变量作为redirect。

使用结构3可以将当前作用域下所有的proxy_redirect指令配置全部设置为无效。

17、proxy_intercept_errors指令

该指令用于配置一个状态是开启还是关闭。在开启该状态时，如果被代理的服务器返回的HTTP状态代码为400或者大于400，则Nginx服务器使用自己定义的错误页了；如果是关闭该状态，Nginx服务器直接将被代理服务器返回的HTTP状态返回给客户端，其语法结构为：

> proxy_intercept_errors on | off;

18、proxy_headers_hash_max_size指令

该指令用于配置存放HTTP报文头的哈希表的容量，其语法结构为：

> proxy_headers_hash_max_size size;

其中，size为HTTP报文头哈希表的容量上限，默认为512个字符。

19、proxy_headers_hash_bucket_size指令

该指令用于设置Nginx服务器申请存放HTTP报文头的哈希表容量的单位大小，其语法结构为：

> proxy_headers_hash_bucket_size size;

其中，size为设置的容量，默认为64个字符。

20、proxy_next_upstream指令

在配置Nginx服务器反向代理功能时，如果使用upstream指令配置了一组服务器作为被代理服务器，服务器组中各服务器的访问规则遵循upstream指令配置的轮询规则，同时可以使用该指令配置在发生哪些异常情况时，将请求顺次交由下一个组内服务器处理，其语法结构为：

> proxy_next_upstream status ...;

其中，status为设置的服务器返回状态，可以是一个或者多个。这些状态包括：

error：在建立连接、向被代理的服务器发送请求或者读取响应头时服务器发生连接错误。

timeout：在建立连接、向被代理的服务器发送请求或者读取响应头时服务器发生连接超时。

invalid_header：被代理的服务器返回的响应头为空或者无效。

http_500 | http_502 | http_503 | http_504 | http_404：被代理的服务器返回500、502、503、504或者404状态代码。

off：无法将请求发送给被代理的服务器。

21、proxy_ssl_session_reuse指令

该指令用于配置是否使用基于SSL安全协议的会话连接被代理的服务器，其语法结构为：

> proxy_ssl_session_reuse on | off;

默认设置为开启（on）状态，如果我们在错误日志中发现“SSL3_GET_FINISHED:digest check failed”的情况，可以将该指令配置为关闭（off）状态。

### Proxy Buffer的配置

Proxy Buffer相关指令的配置都是针对每一个请求起作用的，而不是全局概念，即每个请求都会按照这些指令的配置类配置各自的Buffer，Nginx服务器不会生成一个公共的Proxy Buffer供代理请求使用。

Proxy Buffer启用以后，Nginx服务器会异步地将被代理服务器的响应数据传递给客户端。

Nginx服务器首先尽可能地从被代理服务器那里接收响应数据，放置在Proxy Buffer中，Buffer的大小由proxy_buffer_size指令和proxy_buffers指令决定。如果在接收过程中，发现Buffer没有足够大小来接收一次响应的数据，Nginx服务器会将部分接收到的数据临时存放在磁盘的临时文件夹中，磁盘上的临时文件路径可以通过proxy_temp_path指令进行设置，临时文件的大小由proxy_max_temp_file_size指令和proxy_temp_file_write_size指令决定。一次响应数据被接收完成或者Buffer已经装满后，Nginx服务器开始向客户端传输数据。

每个Proxy Buffer装满数据后，在从开始向客户端发送一直到Proxy Buffer中的数据全部传输给客户端的整个过程中，它都处于busy状态，期间对它进行的其他操作都会失败。同时处于busy状态的Proxy Buffer总大小由proxy_busy_buffers_size指令限制，不会超过该指令设置的大小。

当Proxy Buffer关闭时，Nginx服务器只要接收到响应数据就会同步地传递给客户端，它本身不会读取完整的响应数据。

1、proxy_buffering指令

该指令用于配置是否启用或者关闭Proxy Buffer，其语法结构为：

> proxy_buffering on | off;

默认设置为开启（on）状态，开启和关闭Proxy Buffer还可以通过在HTTP响应头部的“X-accel-Buffering”头域设置“yes”或者“no”来实现，但Nginx配置中proxy_ignore_headers指令的设置可能导致该头域设置失效。

2、proxy_buffers指令

该指令用于配置接收一次被代理服务器响应数据的Proxy Buffer个数和每个Buffer的大小，其语法结构为：

> proxy_buffers number size;

number：Proxy Buffer的个数。

size：每个Buffer的大小，一般设置为内存页的大小。根据平台的不同，可能为4KB或者8KB。

3、proxy_buffer_size指令

该指令用于配置从被代理服务器获取的第一部分响应数据的大小，该数据中一般包含了HTTP响应头，Nginx服务器通过它来获取响应数据和被代理服务器的一些必要信息。该指令的语法结构为：

> proxy_buffer_size size；

其中，size为设置的缓存大小，默认设置为4KB或者8KB，保持与proxy_buffers指令中的size变量相同，当然也可以设置得更小，注意该指令不要和proxy_buffers指令混淆。

4、proxy_busy_buffers_size指令

该指令用于限制同时处于busy状态的Proxy Buffer的总大小。该指令的语法结构如下：

> proxy_busy_buffers_size size;

其中，size为设置的处于busy状态的缓冲区总大小。默认设置为8KB或者16KB。

5、proxy_temp_path指令

该指令用于配置磁盘上的一个文件路径，该文件用于临时存放代理服务器的大体积响应数据。如果Proxy Buffer被装满后，响应数据仍然没有被Nginx服务器完全接收，响应数据就会被临时存放在该文件中。该指令的语法结构为：

> proxy_temp_path path [level1 [level2 [level3]]];

path：设置磁盘上存放临时文件的路径。

levelN：设置在path变量设置的路径下第几级hash目录中存放临时文件。

6、proxy_max_temp_file_size指令

该指令用于配置所有临时文件的总体积大小，存放在磁盘上的临时文件大小不能超过该配置值，这避免了响应数据过大造成磁盘空间不足的问题，其语法结构为：

> proxy_max_temp_file_size size;

其中，size为设置的临时文件总体积上限值，默认设置为1024MB。

7、proxy_temp_file_write_size指令

该指令用于配置同时写入临时文件的数据量的总大小，合理的设置可以避免磁盘IO负载过重导致系统性能下降的问题，其语法结构为：

> proxy_temp_file_write_size size;

其中，size为设置的数据量总大小上限值，默认设置根据平台的不同，可以为8KB或者16KB，一般与平台的内存页大小相同。

### Proxy Cache的配置

proxy Buffer开启后才能使用proxy_cachhe。将已有的数据在内存中建立缓存数据，缓存过期不销毁硬盘的数据，nginx接受到被代理服务器的数据后，通过proxy buffer机制将数据传递给客户端，通过proxy cache将数据缓存到本地硬盘

**1. proxy_cache** zone | off;

配置一块公用的内存区域名称，该区域可以存放缓存的索引数据

**2. proxy_cache_bypass** string ...;

配置nginx服务器向客户端发送数据时，数据不从缓存中获取的条件

proxy_cache_bypass $cookie_nocache $arg_nocache $arg_comment $http_pragma

**3. proxy_cache_key** string;

设置nginx服务器在内存中为缓存数据建立索引时使用的关键字

如：proxy_cache_key $scheme$proxy_host$uri$is_args$args;//数据包包含主机关键字

**4. proxy_cache_lock** on | off;

是否开启缓存的锁功能，开启后，nginx同时只能有一个请求填充缓存中的某一数据项，不允许其他请求操作，其他请求要操作由proxy_cache_lock_timeout配置等待时间。

**5. proxy_cache_lock_timeout** time;

锁后等待时间，默认5s。

**6. proxy_cache_min_uses** number;

配置客户端请求发送的次数，当相同请求达到设置次数后才会缓存该请求的响应数据，默认为1。

**7. proxy_cache_path;**

proxy_cache_path path [levels=levels] [use_temp_path=on|off] keys_zone=name:size [inactive=time] [max_size=size] [manager_files=number] [manager_sleep=time] [manager_threshold=time] [loader_files=number] [loader_sleep=time] [loader_threshold=time] [purger=on|off] [purger_files=number] [purger_sleep=time] [purger_threshold=time];

path：缓存数据存放根目录

levels：指定该缓存空间有两层hash目录，第一层目录为1个字母，第二层为2个字母

keys_zone=name:size 设置缓存名字和共享内存大小

inactive在指定时间内没人访问则被删除在这里是1天 （天：d、秒：s、分：m）

max_size最大缓存空间

**8. proxy_cache_use_stale;**

proxy_cache_use_stale error | timeout | invalid_header | updating | http_500 | http_502 | http_503 | http_504 | http_403 | http_404 | http_429 | off ...;

当被代理服务器无法访问或出错时，nginx用历史缓存响应客户端请求，默认off

**9. proxy_cache_valid** [code...]time;

对不同的http响应状态设置不同的缓存时间

proxy_cache_valid 200 3m;

proxy_cache_valid 404 1m;

proxy_cache_valid any 5m;//any表示该指令所有未设置的其他状态

**10. proxy_no_cache** string...;

string可以使多个变量，为空为0则启用cache功能

**11. proxy_store** on | off | string;

配置是否在本地磁盘缓存来自被代理服务器的响应数据，对静态数据的效果比较好

on会存放在alias指令或者root指令设置的本地路径下

string自定义缓存文件存放路径

**12. proxy_store_access** users:permissions ...;

配置用户或者用户组对proxy store缓存的数据访问权限

user：user,group,all

permissions:权限

如：proxy_store_access user:rw group:rw all:r

