## Netty

### Netty

Netty是由[JBOSS](https://baike.baidu.com/item/JBOSS)提供的一个[java开源](https://baike.baidu.com/item/java开源/10795649)框架，现为 [Github](https://baike.baidu.com/item/Github/10145341)上的独立项目。Netty提供异步的、[事件驱动](https://baike.baidu.com/item/事件驱动/9597519)的网络应用程序框架和工具，用以快速开发高性能、高可靠性的[网络服务器](https://baike.baidu.com/item/网络服务器/99096)和客户端程序。

也就是说，Netty 是一个基于NIO的客户、服务器端的编程框架，使用Netty 可以确保你快速和简单的开发出一个网络应用，例如实现了某种协议的客户、[服务端](https://baike.baidu.com/item/服务端/6492316)应用。Netty相当于简化和流线化了网络应用的编程开发过程，例如：基于TCP和UDP的socket服务开发。

“快速”和“简单”并不用产生维护性或性能上的问题。Netty 是一个吸收了多种协议（包括FTP、SMTP、HTTP等各种二进制文本协议）的实现经验，并经过相当精心设计的项目。最终，Netty 成功的找到了一种方式，在保证易于开发的同时还保证了其应用的性能，稳定性和伸缩性。

### 同步阻塞IO（Blocking IO）

阻塞IO，指的是需要内核IO操作彻底完成后，才返回到用户空间执行用户的操作。阻塞指的是用户空间程序的执行状态。传统的IO模型都是同步阻塞IO。在Java中，默认创建的socket都是阻塞的。

同步IO，是一种用户空间与内核空间的IO发起方式。同步IO是指用户空间的线程是主动发起IO请求的一方，内核空间是被动接受方。异步IO则反过来，是指系统内核是主动发起IO请求的一方，用户空间的线程是被动接受方。

举个例子，在Java中发起一个socket的read读操作的系统调用，流程大致如下：

（1）从Java启动IO读的read系统调用开始，用户线程就进入阻塞状态。

（2）当系统内核收到read系统调用，就开始准备数据。一开始，数据可能还没有到达内核缓冲区（例如，还没有收到一个完整的socket数据包），这个时候内核就要等待。

（3）内核一直等到完整的数据到达，就会将数据从内核缓冲区复制到用户缓冲区（用户空间的内存），然后内核返回结果（例如返回复制到用户缓冲区中的字节数）。

（4）直到内核返回后，用户线程才会解除阻塞的状态，重新运行起来。总之，阻塞IO的特点是：在内核进行IO执行的两个阶段，用户线程都被阻塞了。

![image-20200227154929394](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227154929394.png)

阻塞IO的优点是：应用的程序开发非常简单；在阻塞等待数据期间，用户线程挂起。在阻塞期间，用户线程基本不会占用CPU资源。

阻塞IO的缺点是：一般情况下，会为每个连接配备一个独立的线程；反过来说，就是一个线程维护一个连接的IO操作。在并发量小的情况下，这样做没有什么问题。但是，当在高并发的应用场景下，需要大量的线程来维护大量的网络连接，内存、线程切换开销会非常巨大。因此，基本上阻塞IO模型在高并发应用场景下是不可用的。

### 同步非阻塞IO（Non-blocking IO）

非阻塞IO，指的是用户空间的程序不需要等待内核IO操作彻底完成，可以立即返回用户空间执行用户的操作，即处于非阻塞的状态，与此同时内核会立即返回给用户一个状态值。

非阻塞IO要求socket被设置为NONBLOCK。

socket连接默认是阻塞模式，在Linux系统下，可以通过设置将socket变成为非阻塞的模式（Non-Blocking）。使用非阻塞模式的IO读写，叫作同步非阻塞IO（None Blocking IO），简称为NIO模式。

举个例子。发起一个非阻塞socket的read读操作的系统调用，流程如下：

（1）在内核数据没有准备好的阶段，用户线程发起IO请求时，立即返回。所以，为了读取到最终的数据，用户线程需要不断地发起IO系统调用。

（2）内核数据到达后，用户线程发起系统调用，用户线程阻塞。内核开始复制数据，它会将数据从内核缓冲区复制到用户缓冲区（用户空间的内存），然后内核返回结果（例如返回复制到的用户缓冲区的字节数）。

（3）用户线程读到数据后，才会解除阻塞状态，重新运行起来。也就是说，用户进程需要经过多次的尝试，才能保证最终真正读到数据，而后继续执行。

![image-20200227155028504](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227155028504.png)

同步非阻塞IO的优点：每次发起的IO系统调用，在内核等待数据过程中可以立即返回。用户线程不会阻塞，实时性较好。

同步非阻塞IO的缺点：不断地轮询内核，这将占用大量的CPU时间，效率低下。

### IO多路复用（IO Multiplexing）

即经典的Reactor反应器设计模式，有时也称为异步阻塞IO, Java中的Selector选择器和Linux中的epoll都是这种模型。

在IO多路复用模型中，引入了一种新的系统调用，查询IO的就绪状态。在Linux系统中，对应的系统调用为select/epoll系统调用。通过该系统调用，一个进程可以监视多个文件描述符，一旦某个描述符就绪（一般是内核缓冲区可读/可写），内核能够将就绪的状态返回给应用程序。随后，应用程序根据就绪的状态，进行相应的IO系统调用。

目前支持IO多路复用的系统调用，有select、epoll等等。select系统调用，几乎在所有的操作系统上都有支持，具有良好的跨平台特性。epoll是在Linux 2.6内核中提出的，是select系统调用的Linux增强版本。

在IO多路复用模型中通过select/epoll系统调用，单个应用程序的线程，可以不断地轮询成百上千的socket连接，当某个或者某些socket网络连接有IO就绪的状态，就返回对应的可以执行的读写操作。

举个例子来说明IO多路复用模型的流程。发起一个多路复用IO的read读操作的系统调用，流程如下：

（1）选择器注册。在这种模式中，首先，将需要read操作的目标socket网络连接，提前注册到select/epoll选择器中，Java中对应的选择器类是Selector类。然后，才可以开启整个IO多路复用模型的轮询流程。

（2）就绪状态的轮询。通过选择器的查询方法，查询注册过的所有socket连接的就绪状态。通过查询的系统调用，内核会返回一个就绪的socket列表。当任何一个注册过的socket中的数据准备好了，内核缓冲区有数据（就绪）了，内核就将该socket加入到就绪的列表中。当用户进程调用了select查询方法，那么整个线程会被阻塞掉。

（3）用户线程获得了就绪状态的列表后，根据其中的socket连接，发起read系统调用，用户线程阻塞。内核开始复制数据，将数据从内核缓冲区复制到用户缓冲区。

（4）复制完成后，内核返回结果，用户线程才会解除阻塞的状态，用户线程读取到了数据，继续执行。

![image-20200227155814246](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227155814246.png)

IO多路复用模型的优点：与一个线程维护一个连接的阻塞IO模式相比，使用select/epoll的最大优势在于，一个选择器查询线程可以同时处理成千上万个连接（Connection）。系统不必创建大量的线程，也不必维护这些线程，从而大大减小了系统的开销。

Java语言的NIO（New IO）技术，使用的就是IO多路复用模型。在Linux系统上，使用的是epoll系统调用。

IO多路复用模型的缺点：本质上，select/epoll系统调用是阻塞式的，属于同步IO。都需要在读写事件就绪后，由系统调用本身负责进行读写，也就是说这个读写过程是阻塞的。

如何彻底地解除线程的阻塞，就必须使用异步IO模型。

### 异步IO（Asynchronous IO）

异步IO模型（Asynchronous IO，简称为AIO）。AIO的基本流程是：用户线程通过系统调用，向内核注册某个IO操作。内核在整个IO操作（包括数据准备、数据复制）完成后，通知用户程序，用户执行后续的业务操作。

在异步IO模型中，在整个内核的数据处理过程中，包括内核将数据从网络物理设备（网卡）读取到内核缓冲区、将内核缓冲区的数据复制到用户缓冲区，用户程序都不需要阻塞。

![image-20200227155906313](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227155906313.png)

举个例子。发起一个异步IO的read读操作的系统调用，流程如下：

（1）当用户线程发起了read系统调用，立刻就可以开始去做其他的事，用户线程不阻塞。

（2）内核就开始了IO的第一个阶段：准备数据。等到数据准备好了，内核就会将数据从内核缓冲区复制到用户缓冲区（用户空间的内存）。

（3）内核会给用户线程发送一个信号（Signal），或者回调用户线程注册的回调接口，告诉用户线程read操作完成了。

（4）用户线程读取用户缓冲区的数据，完成后续的业务操作。

异步IO模型的特点：在内核等待数据和复制数据的两个阶段，用户线程都不是阻塞的。用户线程需要接收内核的IO操作完成的事件，或者用户线程需要注册一个IO操作完成的回调函数。正因为如此，异步IO有的时候也被称为信号驱动IO。

异步IO异步模型的缺点：应用程序仅需要进行事件的注册与接收，其余的工作都留给了操作系统，也就是说，需要底层内核提供支持。

### Reactor反应器

反应器模式由Reactor反应器线程、Handlers处理器两大角色组成：

（1）Reactor反应器线程的职责：负责响应IO事件，并且分发到Handlers处理器。

（2）Handlers处理器的职责：非阻塞的执行业务处理逻辑。

### 单线程Reactor反应器模式

![image-20200227160305214](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227160305214.png)

什么是单线程版本的Reactor反应器模式呢？简单地说，Reactor反应器和Handers处理器处于一个线程中执行。

在单线程反应器模式中，Reactor反应器和Handler处理器，都执行在同一条线程上。这样，带来了一个问题：当其中某个Handler阻塞时，会导致其他所有的Handler都得不到执行。在这种场景下，如果被阻塞的Handler不仅仅负责输入和输出处理的业务，还包括负责连接监听的AcceptorHandler处理器。这个是非常严重的问题。

### 多线程的Reactor反应器模式

（1）将负责输入输出处理的IOHandler处理器的执行，放入独立的线程池中。这样，业务处理线程与负责服务监听和IO事件查询的反应器线程相隔离，避免服务器的连接监听受到阻塞。

（2）如果服务器为多核的CPU，可以将反应器线程拆分为多个子反应器（SubReactor）线程；同时，引入多个选择器，每一个SubReactor子线程负责一个选择器。这样，充分释放了系统资源的能力；也提高了反应器管理大量连接，提升选择大量通道的能力。

### Netty的异步回调模式

Netty对JavaFuture异步任务的扩展如下：

（1）继承Java的Future接口，得到了一个新的属于Netty自己的Future异步任务接口；该接口对原有的接口进行了增强，使得Netty异步任务能够以非阻塞的方式处理回调的结果；注意，Netty没有修改Future的名称，只是调整了所在的包名，Netty的Future类的包名和Java的Future接口的包名不同。

（2）引入了一个新接口——GenericFutureListener，用于表示异步执行完成的监听器。这个接口和Guava的FutureCallbak回调接口不同。Netty使用了监听器的模式，异步任务的执行完成后的回调逻辑抽象成了Listener监听器接口。可以将Netty的GenericFutureListener监听器接口加入Netty异步任务Future中，实现对异步任务执行状态的事件监听。

### Netty中常见的通道

·NioSocketChannel：异步非阻塞TCP Socket传输通道。

· NioServerSocketChannel：异步非阻塞TCP Socket服务器端监听通道。

· NioDatagramChannel：异步非阻塞的UDP传输通道。

· NioSctpChannel：异步非阻塞Sctp传输通道。

· NioSctpServerChannel：异步非阻塞Sctp服务器端监听通道。

· OioSocketChannel：同步阻塞式TCP Socket传输通道。

· OioServerSocketChannel：同步阻塞式TCP Socket服务器端监听通道。

· OioDatagramChannel：同步阻塞式UDP传输通道。

· OioSctpChannel：同步阻塞式Sctp传输通道。

· OioSctpServerChannel：同步阻塞式Sctp服务器端监听通道。

· OioDatagramChannel：同步阻塞式UDP传输通道。

· OioSctpChannel：同步阻塞式Sctp传输通道。

· OioSctpServerChannel：同步阻塞式Sctp服务器端监听通道。

### Netty中的Reactor反应器

Netty中的反应器有多个实现类，与Channel通道类有关系。对应于NioSocketChannel通道，Netty的反应器类为：NioEventLoop。NioEventLoop类绑定了两个重要的Java成员属性：一个是Thread线程类的成员，一个是Java NIO选择器的成员属性。

### Netty中的Handler处理器

Netty的Handler处理器分为两大类：第一类是ChannelInboundHandler通道入站处理器；第二类是ChannelOutboundHandler通道出站处理器。二者都继承了ChannelHandler处理器接口。

### Netty的流水线（Pipeline）

Netty设计了一个特殊的组件，叫作ChannelPipeline（通道流水线），它像一条管道，将绑定到一个通道的多个Handler处理器实例，串在一起，形成一条流水线。ChannelPipeline（通道流水线）的默认实现，实际上被设计成一个双向链表。所有的Handler处理器实例被包装成了双向链表的节点，被加入到了ChannelPipeline（通道流水线）中。

### Bootstrap的启动流程

第1步：创建反应器线程组，并赋值给ServerBootstrap启动器实例

第2步：设置通道的IO类型

第3步：设置监听端口

第4步：设置传输通道的配置选项

第5步：装配子通道的Pipeline流水线

第6步：开始绑定服务器新连接的监听端口

第7步：自我阻塞，直到通道关闭

第8步：关闭EventLoopGroup

### ByteBuf缓冲区

与Java NIO的ByteBuffer相比，ByteBuf的优势如下：

· Pooling (池化，这点减少了内存复制和GC，提升了效率)

· 复合缓冲区类型，支持零复制

· 不需要调用flip()方法去切换读/写模式

· 扩展性好，例如StringBuffer

· 可以自定义缓冲区类型· 读取和写入索引分开

· 方法的链式调用

· 可以进行引用计数，方便重复使用

### ByteBuf的重要属性

· readerIndex（读指针）：指示读取的起始位置。每读取一个字节，readerIndex自动增加1。一旦readerIndex与writerIndex相等，则表示ByteBuf不可读了。

· writerIndex（写指针）：指示写入的起始位置。每写一个字节，writerIndex自动增加1。一旦增加到writerIndex与capacity()容量相等，则表示ByteBuf已经不可写了。capacity()是一个成员方法，不是一个成员属性，它表示ByteBuf中可以写入的容量。注意，它不是最大容量maxCapacity。

· maxCapacity（最大容量）：表示ByteBuf可以扩容的最大容量。当向ByteBuf写数据的时候，如果容量不足，可以进行扩容。扩容的最大限度由maxCapacity的值来设定，超过maxCapacity就会报错。

### Netty的解码器

首先，它是一个InBound入站处理器，解码器负责处理“入站数据”。

其次，它能将上一站Inbound入站处理器传过来的输入（Input）数据，进行数据的解码或者格式转换，然后输出（Output）到下一站Inbound入站处理器。

一个标准的解码器将输入类型为ByteBuf缓冲区的数据进行解码，输出一个一个的Java POJO对象。Netty内置了这个解码器，叫作ByteToMessageDecoder，位在Netty的io.netty.handler.codec包中。

强调一下，所有的Netty中的解码器，都是Inbound入站处理器类型，都直接或者间接地实现了ChannelInboundHandler接口。

### 粘包和半包

大家都知道，底层网络是以二进制字节报文的形式来传输数据的。读数据的过程大致为：当IO可读时，Netty会从底层网络将二进制数据读到ByteBuf缓冲区中，再交给Netty程序转成Java POJO对象。写数据的过程大致为：这中间编码器起作用，是将一个Java类型的数据转换成底层能够传输的二进制ByteBuf缓冲数据。解码器的作用与之相反，是将底层传递过来的二进制ByteBuf缓冲数据转换成Java能够处理的Java POJO对象。

在发送端Netty的应用层进程缓冲区，程序以ByteBuf为单位来发送数据，但是到了底层操作系统内核缓冲区，底层会按照协议的规范对数据包进行二次拼装，拼装成传输层TCP层的协议报文，再进行发送。在接收端收到传输层的二进制包后，首先保存在内核缓冲区，Netty读取ByteBuf时才复制到进程缓冲区。

在接收端，当Netty程序将数据从内核缓冲区复制到Netty进程缓冲区的ByteBuf时，问题就来了：

（1）首先，每次读取底层缓冲的数据容量是有限制的，当TCP底层缓冲的数据包比较大时，会将一个底层包分成多次ByteBuf进行复制，进而造成进程缓冲区读到的是半包。

（2）当TCP底层缓冲的数据包比较小时，一次复制的却不止一个内核缓冲区包，进而造成进程缓冲区读到的是粘包。

一般有三种常用的解决半包与粘包问题的方案：

● 比较常见的方案是应用层设计协议时，把协议包分为header和body（比如图8-4为Dubbo协议帧格式）, header里面记录body长度，当服务端从接收缓冲区读取数据后，如果发现数据大小小于包的长度则说明出现了半包，这时候就回退读取缓存的指针，等待下次读事件到来时再次测试。如果发现数据大小大于包长度则看大小是否是包长度的整数倍，如果是则循环读取多个包，否则就是出现了多个整包+半包（粘包）。

![image-20200311215401447](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311215401447.png)

● 第二种方案是在多个包之间添加分隔符，使用分隔符来判断一个包的结束。如图8-5所示，每个包中间使用“|”作为分隔符，此时每个包的大小可以不固定，当服务器端读取时，若遇到分隔符就知道当前包结束了，但是包的消息体内不能含有分隔符，Netty中提供了DelimiterBasedFrameDecoder用来实现该功能。

![image-20200311215417094](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311215417094.png)

● 还有一种方案是包定长，就是每个包大小固定长度，如图8-6所示。

![image-20200311215431710](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311215431710.png)



