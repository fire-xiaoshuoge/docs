# JUC

## JMM

### 上下文切换

即使是单核处理器也支持多线程执行代码，CPU通过给每个线程分配CPU时间片来实现这个机制。时间片是CPU分配给各个线程的时间，因为时间片非常短，所以CPU通过不停地切换线程执行，让我们感觉多个线程是同时执行的，时间片一般是几十毫秒（ms）。

CPU通过时间片分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再加载这个任务的状态。所以任务从保存到再加载的过程就是一次上下文切换。

### 减少上下文切换

减少上下文切换的方法有无锁并发编程、CAS算法、使用最少线程和使用协程。

❑ 无锁并发编程。多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以用一些办法来避免使用锁，如将数据的ID按照Hash算法取模分段，不同的线程处理不同段的数据。

❑ CAS算法。Java的Atomic包使用CAS算法来更新数据，而不需要加锁。

❑ 使用最少线程。避免创建不需要的线程，比如任务很少，但是创建了很多线程来处理，这样会造成大量线程都处于等待状态。

❑ 协程：在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

### JMM

JMM即为JAVA 内存模型（java memory model）。因为在不同的硬件生产商和不同的操作系统下，内存的访问逻辑有一定的差异，结果就是当你的代码在某个系统环境下运行良好，并且线程安全，但是换了个系统就出现各种问题。Java内存模型，就是为了屏蔽系统和硬件的差异，让一套代码在不同平台下能到达相同的访问结果。JMM从java 5开始的JSR-133发布后，已经成熟和完善起来。

JMM定义了Java 虚拟机(JVM)在计算机内存(RAM)中的工作方式。JVM是整个计算机虚拟模型，所以JMM是隶属于JVM的。从抽象的角度来看，JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（Main Memory）中，每个线程都有一个私有的本地内存（Local Memory），本地内存中存储了该线程以读/写共享变量的副本。本地内存是JMM的一个抽象概念，并不真实存在。它涵盖了缓存、写缓冲区、寄存器以及其他的硬件和编译器优化。

主内存：java虚拟机规定所有的变量(不是程序中的变量)都必须在主内存中产生，为了方便理解，可以认为是堆区。可以与前面说的物理机的主内存相比，只不过物理机的主内存是整个机器的内存，而虚拟机的主内存是虚拟机内存中的一部分。

工作内存：java虚拟机中每个线程都有自己的工作内存，该内存是线程私有的为了方便理解，可以认为是虚拟机栈。可以与前面说的高速缓存相比。线程的工作内存保存了线程需要的变量在主内存中的副本。虚拟机规定，线程对主内存变量的修改必须在线程的工作内存中进行，不能直接读写主内存中的变量。不同的线程之间也不能相互访问对方的工作内存。如果线程之间需要传递变量的值，必须通过主内存来作为中介进行传递。

![img](https://img-blog.csdn.net/20170608221857890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamF2YXplamlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 工作内存与主内存交互

java虚拟机中主内存和工作内存交互，就是一个变量如何从主内存传输到工作内存中，如何把修改后的变量从工作内存同步回主内存。

- **lock(锁定)**:作用于主内存的变量，一个变量在同一时间只能一个线程锁定，该操作表示这条线成独占这个变量
- **unlock(解锁)**:作用于主内存的变量，表示这个变量的状态由处于锁定状态被释放，这样其他线程才能对该变量进行锁定
- **read(读取)**:作用于主内存变量，表示把一个主内存变量的值传输到线程的工作内存，以便随后的load操作使用
- **write(写入)**:作用于主内存的变量，把store操作从工作内存中得到的变量的值放入主内存的变量中
- **load(载入)**:作用于线程的工作内存的变量，表示把read操作从主内存中读取的变量的值放到工作内存的变量副本中(副本是相对于主内存的变量而言的)
- **use(使用)**:作用于线程的工作内存中的变量，表示把工作内存中的一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用变量的值的字节码指令时就会执行该操作
- **assign(赋值)**:作用于线程的工作内存的变量，表示把执行引擎返回的结果赋值给工作内存中的变量，每当虚拟机遇到一个给变量赋值的字节码指令时就会执行该操作
- **store(存储)**:作用于线程的工作内存中的变量，把工作内存中的一个变量的值传递给主内存，以便随后的write操作使用

如果要把一个变量从主内存传输到工作内存，那就要顺序的执行read和load操作，如果要把一个变量从工作内存回写到主内存，就要顺序的执行store和write操作。对于普通变量，虚拟机只是要求顺序的执行，并没有要求连续的执行，所以如下也是正确的。对于两个线程，分别从主内存中读取变量a和b的值，并不一样要read a; load a; read b; load b; 也会出现如下执行顺序：read a; read b; load b; load a; (对于volatile修饰的变量会有一些其他规则,后边会详细列出)，对于这8中操作，虚拟机也规定了一系列规则，在执行这8中操作的时候必须遵循如下的规则：

**不允许read和load、store和write操作之一单独出现**，也就是不允许从主内存读取了变量的值但是工作内存不接收的情况，或者不允许从工作内存将变量的值回写到主内存但是主内存不接收的情况

**不允许一个线程丢弃最近的assign操作**，也就是不允许线程在自己的工作线程中修改了变量的值却不同步/回写到主内存

**不允许一个线程回写没有修改的变量到主内存**，也就是如果线程工作内存中变量没有发生过任何assign操作，是不允许将该变量的值回写到主内存

**变量只能在主内存中产生**，不允许在工作内存中直接使用一个未被初始化的变量，也就是没有执行load或者assign操作。也就是说在执行use、store之前必须对相同的变量执行了load、assign操作

**一个变量在同一时刻只能被一个线程对其进行lock操作**，也就是说一个线程一旦对一个变量加锁后，在该线程没有释放掉锁之前，其他线程是不能对其加锁的，但是同一个线程对一个变量加锁后，可以继续加锁，同时在释放锁的时候释放锁次数必须和加锁次数相同。

**对变量执行lock操作，就会清空工作空间该变量的值**，执行引擎使用这个变量之前，需要重新load或者assign操作初始化变量的值

**不允许对没有lock的变量执行unlock操作**，如果一个变量没有被lock操作，那也不能对其执行unlock操作，当然一个线程也不能对被其他线程lock的变量执行unlock操作

**对一个变量执行unlock之前，必须先把变量同步回主内存中**，也就是执行store和write操作。

### **进程间通信的方式**

进程间通信（IPC，InterProcess  Communication）是指在不同进程之间传播或交换信息。

**无名管道通信**
          管道是一种半双工（即数据只能在一个方向上流动）的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常指父子进程关系。

**高级管道通信**
          将另一个程序当做一个新的进程在当前程序进程中启动，则它算是当前程序的子进程，这种方式我们称为高级管道方式。

**有名管道通信**
          有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

**消息队列通信**
          是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

**信号量通信**
          信号量（Semaphore）是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及统一进程内不用线程之间的同步手段。

信号
          信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

**共享内存通信**
          共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是对快的IPC方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量配合使用，来实现进程间的同步和通信。

**套接字通信**
          套接字（socket）：套接口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。

### 共享内存

Java的并发采用的是共享内存模型，Java线程之间的通信总是隐式进行，整个通信过程对程序员完全透明。

共享内存这种方式比较常见，我们经常会设置一个共享变量。然后多个线程去操作同一个共享变量。从而达到线程通讯的目的。例如，我们使用多个线程去执行页面抓取任务，我们可以使用一个共享变量count来记录任务完成的数量。每当一个线程完成抓取任务，会在原来的count上执行加1操作。这样每个线程都可以通过获取这个count变量来获得当前任务的完成情况。当然必须要考虑的是共享变量的同步问题，这也共享内存容易出错的原因所在。

这种通讯模型中，不同的线程之间是没有直接联系的。都是通过共享变量这个“中间人”来进行交互。而这个“中间人”必要情况下还需被保护在临界区内（加锁或同步）。由此可见，一旦共享变量变得多起来，并且涉及到多种不同线程对象的交互，这种管理会变得非常复杂，极容易出现死锁等问题。

### 消息传递

消息传递方式采取的是线程之间的直接通信，不同的线程之间通过显式的发送消息来达到交互目的。消息传递最有名的方式应该是actor模型了。在这种模型下，一切都是actor，所有的actor之间的通信都必须通过传递消息才能达到。每个actor都有一个收件箱（消息队列）用来保存收到其他actor传递来的消息。actor自己也可以给自己发送消息。

![img](https://images0.cnblogs.com/i/277239/201403/091328107843237.png)

### 并发内存模型的实质

#### 原子性(Automicity)

由Java内存模型来直接保证原子性的变量操作包括read、load、use、assign、store、write这6个动作，虽然存在long和double的特例，但基本可以忽律不计，目前虚拟机基本都对其实现了原子性。如果需要更大范围的控制，lock和unlock也可以满足需求。lock和unlock虽然没有被虚拟机直接开给用户使用，但是提供了字节码层次的指令monitorenter和monitorexit对应这两个操作，对应到java代码就是synchronized关键字，因此在synchronized块之间的代码都具有原子性。

#### 可见性

可见性是指一个线程修改了一个变量的值后，其他线程立即可以感知到这个值的修改。正如前面所说，volatile类型的变量在修改后会立即同步给主内存，在使用的时候会从主内存重新读取，是依赖主内存为中介来保证多线程下变量对其他线程的可见性的。
 除了volatile，synchronized和final也可以实现可见性。synchronized关键字是通过unlock之前必须把变量同步回主内存来实现的，final则是在初始化后就不会更改，所以只要在初始化过程中没有把this指针传递出去也能保证对其他线程的可见性。

#### 有序性

有序性从不同的角度来看是不同的。单纯单线程来看都是有序的，但到了多线程就会跟我们预想的不一样。可以这么说：如果在本线程内部观察，所有操作都是有序的；如果在一个线程中观察另一个线程，所有的操作都是无序的。前半句说的就是“线程内表现为串行的语义”，后半句值得是“指令重排序”现象和主内存与工作内存之间同步存在延迟的现象。
 保证有序性的关键字有volatile和synchronized，volatile禁止了指令重排序，而synchronized则由“一个变量在同一时刻只能被一个线程对其进行lock操作”来保证。

### Java内存模型带来的问题

#### 可见性问题

CPU中运行的线程从主存中拷贝共享对象obj到它的CPU缓存，把对象obj的count变量改为2。但这个变更对运行在右边CPU中的线程不可见，因为这个更改还没有flush到主存中：要解决共享对象可见性这个问题，我们可以使用java volatile关键字或者是加锁。

![img](https://upload-images.jianshu.io/upload_images/4222138-58dbd966b4f80fab.png?imageMogr2/auto-orient/strip|imageView2/2/w/520/format/webp)

#### 竞争现象

线程A和线程B共享一个对象obj。假设线程A从主存读取Obj.count变量到自己的CPU缓存，同时，线程B也读取了Obj.count变量到它的CPU缓存，并且这两个线程都对Obj.count做了加1操作。此时，Obj.count加1操作被执行了两次，不过都在不同的CPU缓存中。如果这两个加1操作是串行执行的，那么Obj.count变量便会在原始值上加2，最终主存中的Obj.count的值会是3。然而下图中两个加1操作是并行的，不管是线程A还是线程B先flush计算结果到主存，最终主存中的Obj.count只会增加1次变成2，尽管一共有两次加1操作。 要解决上面的问题我们可以使用java synchronized代码块。

![img](https://upload-images.jianshu.io/upload_images/4222138-0ad9904ab8a34470.png?imageMogr2/auto-orient/strip|imageView2/2/w/542/format/webp)

### Java内存模型中的重排序

在执行程序时，为了提高性能，编译器和处理器常常会对指令做重排序。

![img](https://upload-images.jianshu.io/upload_images/4222138-0531c2c33ca2f3d2.png?imageMogr2/auto-orient/strip|imageView2/2/w/1025/format/webp)

1）编译器优化的重排序。编译器在不改变单线程程序语义的前提下，可以重新安排语句的执行顺序。

2）指令级并行的重排序。现代处理器采用了指令级并行技术（Instruction-LevelParallelism，ILP）来将多条指令重叠执行。如果不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序。

3）内存系统的重排序。由于处理器使用缓存和读/写缓冲区，这使得加载和存储操作看上去可能是在乱序执行。

数据依赖性
如果两个操作访问同一个变量，且这两个操作中有一个为写操作，此时这两个操作之间就存在数据依赖性。数据依赖分为下列3种类型，这3种情况，只要重排序两个操作的执行顺序，程序的执行结果就会被改变。

![img](https://upload-images.jianshu.io/upload_images/4222138-2c95d88191f5637b.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

控制依赖性
flag变量是个标记，用来标识变量a是否已被写入，在use方法中比变量i依赖if (flag)的判断，这里就叫控制依赖，如果发生了重排序，结果就不对了。

![img](https://upload-images.jianshu.io/upload_images/4222138-459ed3ae17c6d8c2.png?imageMogr2/auto-orient/strip|imageView2/2/w/262/format/webp)

as-if-serial
 不管如何重排序，都必须保证代码在单线程下的运行正确，连单线程下都无法正确，更不用讨论多线程并发的情况，所以就提出了一个as-if-serial的概念。
 as-if-serial语义的意思是：不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。编译器、runtime和处理器都必须遵守as-if-serial语义。为了遵守as-if-serial语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。（强调一下，这里所说的数据依赖性仅针对单个处理器中执行的指令序列和单个线程中执行的操作，不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。）但是，如果操作之间不存在数据依赖关系，这些操作依然可能被编译器和处理器重排序。

![img](https://upload-images.jianshu.io/upload_images/4222138-6683667d7b51efe5.png?imageMogr2/auto-orient/strip|imageView2/2/w/224/format/webp)

1和3之间存在数据依赖关系，同时2和3之间也存在数据依赖关系。因此在最终执行的指令序列中，3不能被重排序到1和2的前面（3排到1和2的前面，程序的结果将会被改变）。但1和2之间没有数据依赖关系，编译器和处理器可以重排序1和2之间的执行顺序。
 asif-serial语义使单线程下无需担心重排序的干扰，也无需担心内存可见性问题。

### happen-before

先行发生原则是Java内存模型中定义的两个操作之间的偏序关系。比如说操作A先行发生于操作B，那么在B操作发生之前，A操作产生的“影响”都会被操作B感知到。这里的影响是指修改了内存中的共享变量、发送了消息、调用了方法等。个人觉得更直白一些就是有可能对操作B的结果有影响的都会被B感知到，对B操作的结果没有影响的是否感知到没有太大关系。

再来重复下八大原则：

- 单线程happen-before原则：在同一个线程中，书写在前面的操作happen-before后面的操作。
- 锁的happen-before原则：同一个锁的unlock操作happen-before此锁的lock操作。
- volatile的happen-before原则：对一个volatile变量的写操作happen-before对此变量的任意操作(当然也包括写操作了)。
- happen-before的传递性原则：如果A操作 happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作。
- 线程启动的happen-before原则：同一个线程的start方法happen-before此线程的其它方法。
- 线程中断的happen-before原则：对线程interrupt方法的调用happen-before被中断线程的检测到中断发送的代码。
- 线程终结的happen-before原则：线程中的所有操作都happen-before线程的终止检测。
- 对象创建的happen-before原则：一个对象的初始化完成先于他的finalize方法调用。

### Synchronized

synchronized关键字是java并发编程中必不可少的工具。它一次只允许一个线程进入特定代码段，从而避免多线程同时修改同一数据。

synchronized是java中一个用来实现锁机制的关键字，可以用来修饰方法（包括静态方法和非静态方法）和代码块。

修饰静态方法时，锁住的是当前class对象；

修饰非静态方法时，锁住的是当前实例对象；

修饰代码块时，锁住的是（）中的对象。

![image-20200229204524030](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200229204524030.png)

#### **实现原理**

synchronized可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性。

#### **三种应用方式**

Java中每一个对象都可以作为锁，这是synchronized实现同步的基础：

普通同步方法（实例方法），锁是当前实例对象 ，进入同步代码前要获得当前实例的锁
静态同步方法，锁是当前类的class对象 ，进入同步代码前要获得当前类对象的锁
同步方法块，锁是括号里面的对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

#### 底层语义原理

Java 虚拟机中的同步(Synchronization)基于进入和退出管程(Monitor)对象实现， 无论是显式同步(有明确的 monitorenter 和 monitorexit 指令,即同步代码块)还是隐式同步都是如此。在 Java 语言中，同步用的最多的地方可能是被 synchronized 修饰的同步方法。同步方法 并不是由 monitorenter 和 monitorexit 指令来实现同步的，而是由方法调用指令读取运行时常量池中方法的 ACC_SYNCHRONIZED 标志来隐式实现的，关于这点，稍后详细分析。下面先来了解一个概念Java对象头，这对深入理解synchronized实现原理非常关键。

#### Java对象头与Monitor

在JVM中，对象在内存中的布局分为三块区域：对象头、实例数据和对齐填充。如下：

![img](https://img-blog.csdn.net/20170603163237166?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamF2YXplamlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实例变量：存放类的属性数据信息，包括父类的属性信息，如果是数组的实例部分还包括数组的长度，这部分内存按4字节对齐。

填充数据：由于虚拟机要求对象起始地址必须是8字节的整数倍。填充数据不是必须存在的，仅仅是为了字节对齐，这点了解即可。

而对于顶部，则是Java头对象，它实现synchronized的锁对象的基础，这点我们重点分析它，一般而言，synchronized使用的锁对象是存储在Java对象头里的，jvm中采用2个字来存储对象头(如果对象是数组则会分配3个字，多出来的1个字记录的是数组长度)，其主要结构是由Mark Word 和 Class Metadata Address 组成，其结构说明如下表：
![image-20200317235603273](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200317235603273.png)

其中Mark Word在默认情况下存储着对象的HashCode、分代年龄、锁标记位等以下是32位JVM的Mark Word默认存储结构

| 锁状态   | 25bit        | 4bit         | 1bit是否是偏向锁 | 2bit 锁标志位 |
| -------- | ------------ | ------------ | ---------------- | ------------- |
| 无锁状态 | 对象HashCode | 对象分代年龄 | 0                | 01            |

由于对象头的信息是与对象自身定义的数据没有关系的额外存储成本，因此考虑到JVM的空间效率，Mark Word 被设计成为一个非固定的数据结构，以便存储更多有效的数据，它会根据对象本身的状态复用自己的存储空间，如32位JVM下，除了上述列出的Mark Word默认存储结构外，还有如下可能变化的结构：

![img](https://img-blog.csdn.net/20170603172215966?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamF2YXplamlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

synchronized的对象锁，锁标识位为10，其中指针指向的是monitor对象（也称为管程或监视器锁）的起始地址。每个对象都存在着一个 monitor 与之关联，对象与其 monitor 之间的关系有存在多种实现方式，如monitor可以与对象一起创建销毁或当线程试图获取对象锁时自动生成，但当一个 monitor 被某个线程持有后，它便处于锁定状态。在Java虚拟机(HotSpot)中，monitor是由ObjectMonitor实现的。

#### Java虚拟机对synchronized的优化

锁的状态总共有四种，无锁状态、偏向锁、轻量级锁和重量级锁。随着锁的竞争，锁可以从偏向锁升级到轻量级锁，再升级的重量级锁，但是锁的升级是单向的，也就是说只能从低到高升级，不会出现锁的降级，关于重量级锁，前面我们已详细分析过，下面我们将介绍偏向锁和轻量级锁以及JVM的其他优化手段，这里并不打算深入到每个锁的实现和转换过程更多地是阐述Java虚拟机所提供的每个锁的核心优化思想，毕竟涉及到具体过程比较繁琐，如需了解详细过程可以查阅《深入理解Java虚拟机原理》。

偏向锁
偏向锁是Java 6之后加入的新锁，它是一种针对加锁操作的优化手段，经过研究发现，在大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，因此为了减少同一线程获取锁(会涉及到一些CAS操作,耗时)的代价而引入偏向锁。**偏向锁的核心思想是，如果一个线程获得了锁，那么锁就进入偏向模式，此时Mark Word 的结构也变为偏向锁结构，当这个线程再次请求锁时，无需再做任何同步操作，即获取锁的过程，这样就省去了大量有关锁申请的操作，从而也就提供程序的性能。**所以，对于没有锁竞争的场合，偏向锁有很好的优化效果，毕竟极有可能连续多次是同一个线程申请相同的锁。但是对于锁竞争比较激烈的场合，偏向锁就失效了，因为这样场合极有可能每次申请锁的线程都是不相同的，因此这种场合下不应该使用偏向锁，否则会得不偿失，需要注意的是，偏向锁失败后，并不会立即膨胀为重量级锁，而是先升级为轻量级锁。下面我们接着了解轻量级锁。

轻量级锁
倘若偏向锁失败，虚拟机并不会立即升级为重量级锁，它还会尝试使用一种称为轻量级锁的优化手段(1.6之后加入的)，此时Mark Word 的结构也变为轻量级锁的结构。轻量级锁能够提升程序性能的依据是“对绝大部分的锁，在整个同步周期内都不存在竞争”，注意这是经验数据。需要了解的是，轻量级锁所适应的场景是线程交替执行同步块的场合，如果存在同一时间访问同一锁的场合，就会导致轻量级锁膨胀为重量级锁。

自旋锁
轻量级锁失败后，虚拟机为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。这是基于在大多数情况下，线程持有锁的时间都不会太长，如果直接挂起操作系统层面的线程可能会得不偿失，毕竟操作系统实现线程之间的切换时需要从用户态转换到核心态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高，因此自旋锁会假设在不久将来，当前的线程可以获得锁，因此虚拟机会让当前想要获取锁的线程做几个空循环(这也是称为自旋的原因)，一般不会太久，可能是50个循环或100循环，在经过若干次循环后，如果得到锁，就顺利进入临界区。如果还不能获得锁，那就会将线程在操作系统层面挂起，这就是自旋锁的优化方式，这种方式确实也是可以提升效率的。最后没办法也就只能升级为重量级锁了。

锁消除
消除锁是虚拟机另外一种锁的优化，这种优化更彻底，Java虚拟机在JIT编译时(可以简单理解为当某段代码即将第一次被执行时进行编译，又称即时编译)，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过这种方式消除没有必要的锁，可以节省毫无意义的请求锁时间，如下StringBuffer的append是一个同步方法，但是在add方法中的StringBuffer属于一个局部变量，并且不会被其他线程所使用，因此StringBuffer不可能存在共享资源竞争的情景，JVM会自动将其锁消除。

### volatile

#### volatile

volatile在并发编程中很常见，但也容易被滥用，现在我们就进一步分析volatile关键字的语义。volatile是Java虚拟机提供的轻量级的同步机制。volatile关键字有如下两个作用

保证被volatile修饰的共享变量对所有线程总数可见的，也就是当一个线程修改了一个被volatile修饰共享变量的值，新值总数可以被其他线程立即得知。

禁止指令重排序优化。

#### volatile的可见性

关于volatile的可见性作用，我们必须意识到被volatile修饰的变量对所有线程总数立即可见的，对volatile变量的所有写操作总是能立刻反应到其他线程中，但是对于volatile变量运算操作在多线程环境并不保证安全性，如下

```java
public class VolatileVisibility {
    public static volatile int i =0;

    public static void increase(){
        i++;
    }
}
```

正如上述代码所示，i变量的任何改变都会立马反应到其他线程中，但是如此存在多条线程同时调用increase()方法的话，就会出现线程安全问题，毕竟i++;操作并不具备原子性，该操作是先读取值，然后写回一个新值，相当于原来的值加上1，分两步完成，如果第二个线程在第一个线程读取旧值和写回新值期间读取i的域值，那么第二个线程就会与第一个线程一起看到同一个值，并执行相同值的加1操作，这也就造成了线程安全失败，因此对于increase方法必须使用synchronized修饰，以便保证线程安全，需要注意的是一旦使用synchronized修饰方法后，由于synchronized本身也具备与volatile相同的特性，即可见性，因此在这样种情况下就完全可以省去volatile修饰变量。

```java
public class VolatileVisibility {
    public static int i =0;

    public synchronized static void increase(){
        i++;
    }
}
```

现在来看另外一种场景，可以使用volatile修饰变量达到线程安全的目的，如下

```java
public class VolatileSafe {

    volatile boolean close;

    public void close(){
        close=true;
    }

    public void doWork(){
        while (!close){
            System.out.println("safe....");
        }
    }
}
```

由于对于boolean变量close值的修改属于原子性操作，因此可以通过使用volatile修饰变量close，使用该变量对其他线程立即可见，从而达到线程安全的目的。那么JMM是如何实现让volatile变量对其他线程立即可见的呢？实际上，当写一个volatile变量时，JMM会把该线程对应的工作内存中的共享变量值刷新到主内存中，当读取一个volatile变量时，JMM会把该线程对应的工作内存置为无效，那么该线程将只能从主内存中重新读取共享变量。volatile变量正是通过这种写-读方式实现对其他线程可见。

#### volatile禁止重排优化

volatile关键字另一个作用就是禁止指令重排优化，从而避免多线程环境下程序出现乱序执行的现象，关于指令重排优化前面已详细分析过，这里主要简单说明一下volatile是如何实现禁止指令重排优化的。先了解一个概念，内存屏障(Memory Barrier）。
内存屏障，又称内存栅栏，是一个CPU指令，它的作用有两个，一是保证特定操作的执行顺序，二是保证某些变量的内存可见性（利用该特性实现volatile的内存可见性）。由于编译器和处理器都能执行指令重排优化。如果在指令间插入一条Memory Barrier则会告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重排序，也就是说通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化。Memory Barrier的另外一个作用是强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本。总之，volatile变量正是通过内存屏障实现其在内存中的语义，即可见性和禁止重排优化。

### DCL

#### DCL

**双重检查加锁** 被熟知为“懒汉式”单例模式的实现，下文将统一称之为 DCL。

早期 JVM 中因为同步的开销巨大，为了降低实现单例模式中同步带来的开销，人们想出了很多技巧，DCL 便是其中一种。一段常规的 DCL 实现的单例模式如下：

```java
public class DoubleCheckedLocking {
    
    private static SingleInstance singleInstance;

    public static SingleInstance getInstance() {
        if (singleInstance == null) {
            synchronized (DoubleCheckedLocking.class) {
                if (singleInstance == null) {
                    singleInstance == new SingleInstance();
                }
            }
        }
    }
}
```

#### 描述

DCL 主要是为了使用延迟初始化来降低“饿汉式”单例模式对 JVM 启动时的性能影响，为了方便解释其中存在的问题，这里将上面代码块中的两个 `if` 语句分别成为 `if.1` 和 `if.2`。

#### 情景

假设此时有两个线程 A 和 B 都需要获取 SingleInstance，并且 A 线程进入到 `if.2` 内，B 线程刚开始 `if.1` 的判断。也就是说，在 A 线程在执行初始化的过程中，B 线程读取了线程间共享变量（singleInstance）。这种情况下，B 线程很有可能读取到一个初始化并未完成的非空的引用（singleInstance 被部分构造，产生原因后面会有讨论）。

比如 SingleInstance 对象中有一个字段 `id:String`，并且在构造函数中为该字段进行赋值。在上文描述的情况下就是：singleInstance 实例已经是一个非空的引用，new SingleInstance("110") 操作已经生效，但是并没有全部完成。singleInstance.getId() 返回的仍然是 `null`，而不是构造函数中传入的 ”110“。
 这种情况下，B 线程不会再创建重复的实例，但是会拿着一个无效的对象进行后续操作，可能会在程序中造成更严重的错误。

#### 为什么会出现部分构造的对象

简单来说是因为无序写入（out-of-order writes）。
 如果构造函数写入非 final 字段，则不必立即将它们提交到内存，甚至可以在单例变量之后提交。构造函数其实已经完成，但这并不意味着所有写入对其它线程可见。
 部分构造就是这种情况的一个糟糕体现，singleInstance 引用已对其它线程可见，但对象的内容singleInstance.getId() 对其它线程并不可见。就是因为对象构造过程中一系列指令写入内存的乱序，导致了失效对象的产生。

#### 解决方法

在 Java 5.0 之后，使用 volatile 来修饰 singleInstance 实例，就不会产生指令重排序的情况，这样 DCL 也就可以正常工作了。
 但因为有了更加方便与安全的替代方式，DCL 也没有什么特别的优势，便被废弃了。

![image-20200511044027649](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511044027649.png)

## AQS/CAS

### AQS

#### AQS

aqs全称为AbstractQueuedSynchronizer，它提供了一个FIFO队列，可以看成是一个用来实现同步锁以及其他涉及到同步功能的核心组件，常见的有:ReentrantLock、CountDownLatch等。
AQS是一个抽象类，主要是通过继承的方式来使用，它本身没有实现任何的同步接口，仅仅是定义了同步状态的获取以及释放的方法来提供自定义的同步组件。

#### AQS两种功能

从使用层面来说，AQS的功能分为两种：独占和共享

- 独占锁，每次只能有一个线程持有锁，比如前面给大家演示的ReentrantLock就是以独占方式实现的互斥锁
- 共享锁，允许多个线程同时获取锁，并发访问共享资源，比如ReentrantReadWriteLock

#### AQS的内部实现

AQS的实现依赖内部的同步队列,也就是FIFO的双向队列，如果当前线程竞争锁失败，那么AQS会把当前线程以及等待状态信息构造成一个Node加入到同步队列中，同时再阻塞该线程。当获取锁的线程释放锁以后，会从队列中唤醒一个阻塞的节点(线程)。

![AQS同步队列](https://segmentfault.com/img/remote/1460000017372071?w=439&h=204)

AQS队列内部维护的是一个FIFO的双向链表，这种结构的特点是每个数据结构都有两个指针，分别指向直接的后继节点和直接前驱节点。所以双向链表可以从任意一个节点开始很方便的访问前驱和后继。每个Node其实是由线程封装，当线程争抢锁失败后会封装成Node加入到ASQ队列中去。

#### Node类

```java
static final class Node {
        static final Node SHARED = new Node();
        static final Node EXCLUSIVE = null;
        static final int CANCELLED =  1;
        static final int SIGNAL    = -1;
        static final int CONDITION = -2;
        static final int PROPAGATE = -3;
        volatile int waitStatus;
        volatile Node prev; //前驱节点
        volatile Node next; //后继节点
        volatile Thread thread;//当前线程
        Node nextWaiter; //存储在condition队列中的后继节点
        //是否为共享锁
        final boolean isShared() { 
            return nextWaiter == SHARED;
        }

        final Node predecessor() throws NullPointerException {
            Node p = prev;
            if (p == null)
                throw new NullPointerException();
            else
                return p;
        }

        Node() {    // Used to establish initial head or SHARED marker
        }
        //将线程构造成一个Node，添加到等待队列
        Node(Thread thread, Node mode) {     // Used by addWaiter
            this.nextWaiter = mode;
            this.thread = thread;
        }
        //这个方法会在Condition队列使用，后续单独写一篇文章分析condition
        Node(Thread thread, int waitStatus) { // Used by Condition
            this.waitStatus = waitStatus;
            this.thread = thread;
        }
    }
```

#### 添加节点

当出现锁竞争以及释放锁的时候，AQS同步队列中的节点会发生变化，首先看一下添加节点的场景。

![节点添加到同步队列](https://segmentfault.com/img/remote/1460000017372072?w=648&h=228)

这里会涉及到两个变化

- 新的线程封装成Node节点追加到同步队列中，设置prev节点以及修改当前节点的前置节点的next节点指向自己
- 通过CAS讲tail重新指向新的尾部节点

#### 释放锁移除节点

head节点表示获取锁成功的节点，当头结点在释放同步状态时，会唤醒后继节点，如果后继节点获得锁成功，会把自己设置为头结点，节点的变化过程如下

![移除节点的变化](https://segmentfault.com/img/remote/1460000017372073?w=624&h=208)

这个过程也是涉及到两个变化

- 修改head节点指向下一个获得锁的节点
- 新的获得锁的节点，将prev的指针指向null

这里有一个小的变化，就是设置head节点不需要用CAS，原因是设置head节点是由获得锁的线程来完成的，而同步锁只能由一个线程获得，所以不需要CAS保证，只需要把head节点设置为原首节点的后继节点，并且断开原head节点的next引用即可。

#### AQS方法

![img](https://img-blog.csdn.net/20180509090336580?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmdoYW4xMjIy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

AQS提供的模板方法基本分为3类：独占式获取与释放同步状态，共享式获取与释放同步状态和查询同步队列中的等待线程情况。

#### 独占式同步状态获取与释放

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

这个方法主要完成了同步状态获取、节点的构造、加入同步队列以及在同步队列中自旋的相关操作。    

1、tryAcquire：是自定义AQS实现的。该方法保证线程安全的获取同步状态，获取成功返回true，获取失败则返回false

2、addWaiter：如果tryAcquire返回false，也就是获取同步状态失败，这时候则构造同步节点（独占式）EXCLUSIVE，调用该方法将节点加入到同步队列尾部

3、acquireQueued：该方法是的同步队列中的节点以自旋方式获取同步状态。如果获取不到则阻塞节点中的线程，而被阻塞的线程的唤醒主要依靠前驱节点的出队或阻塞线程被中断来实现。

#### 独占式响应获取同步状态

上面说的acquire方法虽然以独占方式获取同步状态，但是该方法对线程的中断并不响应，当该线程被中断时，该线程仍然在同步队列中等待着获取同步状态，为了响应中断，AQS还提供了acquireInterruptibly方法，当该线程在等待获取同步状态时被中断，会立刻响应中断并抛出中断异常。

```java

public final void acquireInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}
```

可以看到，程序首先判断线程是否中断，如果中断，则抛出中断异常，否则，尝试获取同步状态，如果获取同步状态成功，则调用doAcquireInterruptibly方法。

```java

private void doAcquireInterruptibly(int arg)
throws InterruptedException {
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

从上述代码可以看出，doAcquireInterruptibly方法和acquire方法仅有两点不同，一个是doAcquireInterruptibly方法声明抛出中断异常，另一个就是在中断方法处，不是设置线程的中断标志，而是抛出中断异常。

#### 独占式超时获取同步状态

除了上面两个方法外，AQS还提供了一个超时获取同步状态方法tryAcquireNanos，该方法不但可以响应中断，还可以在指定时间内获取同步状态，获取成功返回true，否则返回false。

```java
public final boolean tryAcquireNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquire(arg) ||
        doAcquireNanos(arg, nanosTimeout);
}
```

可以看出，tryAcquireNanos方法主要是调用了doAcquireNanos方法。

```java
private boolean doAcquireNanos(int arg, long nanosTimeout)
    throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    //获取超时时间点
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return true;
            }
            //获取同步状态失败，计算剩余时间
            nanosTimeout = deadline - System.nanoTime();
            //如果超时，则返回false
            if (nanosTimeout <= 0L)
                return false;
            if (shouldParkAfterFailedAcquire(p, node) &&
                    nanosTimeout > spinForTimeoutThreshold)
                LockSupport.parkNanos(this, nanosTimeout);
            //响应中断
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

该线程在自旋过程中，当节点的前驱节点为头结点时尝试获取同步状态，如果获取成功，则返回true，但在获取同步状态失败时的处理方式则不同。最开始先计算出线程超时的时间点 deadline = System.nanoTime()+timeout，如果获取同步状态失败，计算剩余时间，接下来判断是否超时（timeout<=0），超时则返回false，否则重新计算超时间隔，然后使当前线程等待timeout纳秒。

如果timeout小于等于spinForTimeoutThreshold（1000纳秒），将不会使该线程进行超时等待，而是进入快速自旋的过程。原因就是在于，非常短的超时等待无法做到十分精确，如果这是在进行超时等待，相反的会让timeout的超时从整体上表现得反而不精确。因此在超时非常短的情景下，AQS会进入无条件的快速自旋。

![img](https://img-blog.csdn.net/20180509112524636?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmdoYW4xMjIy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 独占式同步状态释放

 在线程获取同步状态之后，执行完相应的逻辑代码，就会释放同步状态，其他等待线程就可以竞争获取同步状态。通过调用AQS的release方法可以释放同步状态，该方法释放了同步状态后，会唤醒其后继节点。

```java

public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```

 该方法执行时，会唤醒头结点的后继节点，unparkSuccessor方法使用LockSupport（在下一篇博客会说到）来唤醒处于等待状态的线程。

#### 共享式同步状态获取

共享式获取与独占式获取最主要的区别在于同一时刻能否有多个线程同时获取到同步状态。

```java
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
```

 在acquireShared方法中，AQS调用tryAcquireShared方法尝试获取同步状态。tryAcquireShared方法返回值为int类型，当返回值大于等于0时，表示能够获取到同步状态。如果返回值小于0，则说明获取共享同步状态失败，此时调用doAcquireShared方法自旋获取同步状态。

```java
private void doAcquireShared(int arg) {
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            //获取前驱节点
            final Node p = node.predecessor();
            //当前驱节点是头结点时才尝试获取同步状态
            if (p == head) {
                //尝试获取共享式同步状态
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    //获取成功，从自旋过程退出
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
```

 可以看出，在doAcquireShared方法中，如果前驱节点是头结点才尝试获取同步状态，获取成功则退出自旋。并且此方法是不响应中断的，当然AQS也提供了响应中断acquireSharedInterruptibly和超时等待的方法tryAcquireSharedNanos。

#### 共享式同步状态释放

```java

public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}

```

该方法在释放同步状态后，将会唤醒后续处于等待状态的节点。对于能够支持多个线程同时访问的并发组件（如Semphore），他和独占式主要区别在于tryReleaseShared方法必须确保同步状态线程安全释放，一般通过循环和CAS来保证的，因为释放同步状态的操作会同时来自多个线程。

#### 线程的阻塞

1、如果当前线程节点的前驱节点为SINGAL状态，则表明当前线程处于等待状态，返回true，当前线程阻塞。

2、如果当前线程节点的前驱节点状态为CANCELLED（值为1），则表明前驱节点线程已经等待超时或者被中断，此时需要将该节点从同步队列中移除掉。最后返回false。

 3、如果当前节点节点前驱节点非SINGAL，CANCELLED状态，则通过CAS将其前驱节点的等待状态设置为SINGAL，返回false。

#### 线程的唤醒

 当线程释放同步状态后，将唤醒其后继节点：

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            //唤醒后继节点
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```

唤醒线程使用unparkSuccessor方法。

```java
private void unparkSuccessor(Node node) {
    //头结点的等待状态
    int ws = node.waitStatus;
    //如果头结点的状态小于0（初始状态）
    if (ws < 0)
        //使用CAS操作将头结点的状态设置为0
        compareAndSetWaitStatus(node, ws, 0);
    //头结点的后继节点
    Node s = node.next;
    //当后继节点为空或者等待超时或被中断
    if (s == null || s.waitStatus > 0) {
        s = null;
        //从同步队列尾部开始，找到一个可以唤醒的节点
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    //唤醒后继节点
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

在唤醒后继节点时，后继节点可能为空，等待超时，被中断，这个时候将从同步队列尾部开始寻找，寻找一个可唤醒的节点，然后调用unpark来唤醒该节点的线程。

#### LockSupport

从上面内容我们注意到，线程的阻塞和唤醒都使用到了LockSupport的方法，LockSupport是用来创建锁和其他同步类的基本线程阻塞原语。

LockSupport定义了一组以park开头的方法用来阻塞线程，以及unpark来唤醒一个被阻塞的线程。

LockSupport提供的方法：

![img](https://img-blog.csdn.net/20180509155545369?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmdoYW4xMjIy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 在Java6中，LockSupport增加了park(Object blocker)，parkNanos(Object blocker,long nanos)和parkUtil(Object blocker,long deadline)三个方法，用于实现阻塞当前线程的功能。

其中blocker是用来标识当前线程在等待的对象。该对象主要用于问题排查和系统监控。

park方法

```java
public static void park(Object blocker) {
    Thread t = Thread.currentThread();
    setBlocker(t, blocker);
    UNSAFE.park(false, 0L);
    setBlocker(t, null);
}
```

unpark方法

```java
public static void unpark(Thread thread) {
    if (thread != null)
        UNSAFE.unpark(thread);
}
```

### CAS

#### CAS

CAS,compare and swap的缩写，中文翻译成比较并交换。

CAS 操作包含三个操作数 —— 内存位置（V）、预期原值（A）和新值(B)。 如果内存位置的值与预期原值相匹配，那么处理器会自动将该位置值更新为新值 。否则，处理器不做任何操作。无论哪种情况，它都会在 CAS 指令之前返回该 位置的值。（在 CAS 的一些特殊情况下将仅返回 CAS 是否成功，而不提取当前 值。）CAS 有效地说明了“我认为位置 V 应该包含值 A；如果包含该值，则将 B 放到这个位置；否则，不要更改该位置，只告诉我这个位置现在的值即可。”

#### CAS存在的问题

\1.  **ABA问题**。因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。ABA问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A－B－A 就会变成1A-2B－3A。

**从Java1**.5开始JDK的atomic包里提供了一个类AtomicStampedReference来解决ABA问题。这个类的compareAndSet方法作用是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。

**2. 循环时间长开销大**。自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。如果JVM能支持处理器提供的pause指令那么效率会有一定的提升，pause指令有两个作用，第一它可以延迟流水线执行指令（de-pipeline）,使CPU不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。第二它可以避免在退出循环的时候因内存顺序冲突（memory order violation）而引起CPU流水线被清空（CPU pipeline flush），从而提高CPU的执行效率。

**3. 只能保证一个共享变量的原子操作**。当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁，或者有一个取巧的办法，就是把多个共享变量合并成一个共享变量来操作。比如有两个共享变量i＝2,j=a，合并一下ij=2a，然后用CAS来操作ij。从Java1.5开始JDK提供了**AtomicReference类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行CAS操作。**

## 锁

### 悲观锁

悲观锁指对数据被外界修改持保守态度，认为数据很容易就会被其他线程修改，所以在数据被处理前先对数据进行加锁，并在整个数据处理过程中，使数据处于锁定状态。悲观锁的实现往往依靠数据库提供的锁机制，即在数据库中，在对数据记录操作前给记录加排它锁。如果获取锁失败，则说明数据正在被其他线程修改，当前线程则等待或者抛出异常。如果获取锁成功，则对记录进行操作，然后提交事务后释放排它锁。

### 乐观锁

乐观锁是相对悲观锁来说的，它认为数据在一般情况下不会造成冲突，所以在访问记录前不会加排它锁，而是在进行数据提交更新时，才会正式对数据冲突与否进行检测。

### 公平锁与非公平锁

根据线程获取锁的抢占机制，锁可以分为公平锁和非公平锁，公平锁表示线程获取锁的顺序是按照线程请求锁的时间早晚来决定的，也就是最早请求锁的线程将最早获取到锁。而非公平锁则在运行时闯入，也就是先来不一定先得。

### 独占锁与共享锁

独占锁保证任何时候都只有一个线程能得到锁，ReentrantLock就是以独占方式实现的。共享锁则可以同时由多个线程持有，例如ReadWriteLock读写锁，它允许一个资源可以被多线程同时进行读操作。独占锁是一种悲观锁，由于每次访问资源都先加上互斥锁，这限制了并发性，因为读操作并不会影响数据的一致性，而独占锁只允许在同一时间由一个线程读取数据，其他线程必须等待当前线程释放锁才能进行读取。共享锁则是一种乐观锁，它放宽了加锁的条件，允许多个线程同时进行读操作。

### 可重入锁

当一个线程要获取一个被其他线程持有的独占锁时，该线程会被阻塞，那么当一个线程再次获取它自己已经获取的锁时是否会被阻塞呢？如果不被阻塞，那么我们说该锁是可重入的，也就是只要该线程获取了该锁，那么可以有限次数地进入被该锁锁住的代码。

### 自旋锁

自旋锁则是，当前线程在获取锁时，如果发现锁已经被其他线程占有，它不马上阻塞自己，在不放弃CPU使用权的情况下，多次尝试获取（默认次数是10，可以使用-XX:PreBlockSpinsh参数设置该值），很有可能在后面几次尝试中其他线程已经释放了锁。如果尝试指定的次数后仍没有获取到锁则当前线程才会被阻塞挂起。

### MCS 自旋锁

MCS 的名称来自其发明人的名字：John Mellor-Crummey和Michael Scott。
 MCS 的实现是基于链表的，每个申请锁的线程都是链表上的一个节点，这些线程会一直轮询自己的本地变量，来知道它自己是否获得了锁。已经获得了锁的线程在释放锁的时候，负责通知其它线程，这样 CPU 之间缓存的同步操作就减少了很多，仅在线程通知另外一个线程的时候发生，降低了系统总线和内存的开销。实现如下所示：

```java
import java.util.concurrent.atomic.AtomicReferenceFieldUpdater;
public class MCSLock {
    public static class MCSNode {
        volatile MCSNode next;
        volatile boolean isWaiting = true; // 默认是在等待锁
    }
    volatile MCSNode queue;// 指向最后一个申请锁的MCSNode
    private static final AtomicReferenceFieldUpdater<MCSLock, MCSNode> UPDATER = AtomicReferenceFieldUpdater
            .newUpdater(MCSLock.class, MCSNode.class, "queue");

    public void lock(MCSNode currentThread) {
        MCSNode predecessor = UPDATER.getAndSet(this, currentThread);// step 1
        if (predecessor != null) {
            predecessor.next = currentThread;// step 2
            while (currentThread.isWaiting) {// step 3
            }
        } else { // 只有一个线程在使用锁，没有前驱来通知它，所以得自己标记自己已获得锁
            currentThread.isWaiting = false;
        }
    }

    public void unlock(MCSNode currentThread) {
        if (currentThread.isWaiting) {// 锁拥有者进行释放锁才有意义
            return;
        }

        if (currentThread.next == null) {// 检查是否有人排在自己后面
            if (UPDATER.compareAndSet(this, currentThread, null)) {// step 4
                // compareAndSet返回true表示确实没有人排在自己后面
                return;
            } else {
                // 突然有人排在自己后面了，可能还不知道是谁，下面是等待后续者
                // 这里之所以要忙等是因为：step 1执行完后，step 2可能还没执行完
                while (currentThread.next == null) { // step 5
                }
            }
        }
        currentThread.next.isWaiting = false;
        currentThread.next = null;// for GC
    }
}
```

MCS 的能够保证较高的效率，降低不必要的性能消耗，并且它是公平的自旋锁。

### CLH 自旋锁

CLH 锁与 MCS 锁的原理大致相同，都是各个线程轮询各自关注的变量，来避免多个线程对同一个变量的轮询，从而从 CPU 缓存一致性的角度上减少了系统的消耗。
 CLH 锁的名字也与他们的发明人的名字相关：Craig，Landin and Hagersten。
 CLH 锁与 MCS 锁最大的不同是，MCS 轮询的是当前队列节点的变量，而 CLH 轮询的是当前节点的前驱节点的变量，来判断前一个线程是否释放了锁。
 实现如下所示：

```java
import java.util.concurrent.atomic.AtomicReferenceFieldUpdater;
public class CLHLock {
    public static class CLHNode {
        private volatile boolean isWaiting = true; // 默认是在等待锁
    }
    private volatile CLHNode tail ;
    private static final AtomicReferenceFieldUpdater<CLHLock, CLHNode> UPDATER = AtomicReferenceFieldUpdater
            . newUpdater(CLHLock.class, CLHNode .class , "tail" );
    public void lock(CLHNode currentThread) {
        CLHNode preNode = UPDATER.getAndSet( this, currentThread);
        if(preNode != null) {//已有线程占用了锁，进入自旋
            while(preNode.isWaiting ) {
            }
        }
    }

    public void unlock(CLHNode currentThread) {
        // 如果队列里只有当前线程，则释放对当前线程的引用（for GC）。
        if (!UPDATER .compareAndSet(this, currentThread, null)) {
            // 还有后续线程
            currentThread.isWaiting = false ;// 改变状态，让后续线程结束自旋
        }
    }
}
```

从上面可以看到，MCS 和 CLH 相比，CLH 的代码比 MCS 要少得多；CLH是在前驱节点的属性上自旋，而MCS是在本地属性变量上自旋；CLH的队列是隐式的，通过轮询关注上一个节点的某个变量，隐式地形成了链式的关系，但CLHNode并不实际持有下一个节点，MCS的队列是物理存在的，而 CLH 的队列是逻辑上存在的；此外，CLH 锁释放时只需要改变自己的属性，MCS 锁释放则需要改变后继节点的属性。

CLH 队列是 J.U.C 中 AQS 框架实现的核心原理。

### 锁偏向

锁偏向是一种针对加锁操作的优化手段。它的核心思想是：如果一个线程获得了锁，那么锁就进入偏向模式。当这个线程再次请求锁时，无须再做任何同步操作。这样就节省了大量有关锁申请的操作，从而提高了程序性能。

使用Java虚拟机参数-XX:+UseBiasedLocking可以开启偏向锁。

当一个线程访问同步块并获取锁时，会在对象头和栈帧中的锁记录里存储锁偏向的线程ID，以后该线程在进入和退出同步块时不需要进行CAS操作来加锁和解锁，只需简单地测试一下对象头的Mark Word里是否存储着指向当前线程的偏向锁。如果测试成功，表示线程已经获得了锁。如果测试失败，则需要再测试一下Mark Word中偏向锁的标识是否设置成1（表示当前是偏向锁）：如果没有设置，则使用CAS竞争锁；如果设置了，则尝试使用CAS将对象头的偏向锁指向当前线程。

### 轻量级锁

如果偏向锁失败，那么虚拟机并不会立即挂起线程，它还会使用一种称为轻量级锁的优化手段。轻量级锁的操作也很方便，它只是简单地将对象头部作为指针指向持有锁的线程堆栈的内部，来判断一个线程是否持有对象锁。如果线程获得轻量级锁成功，则可以顺利进入临界区。如果轻量级锁加锁失败，则表示其他线程抢先争夺到了锁，那么当前线程的锁请求就会膨胀为重量级锁。

### 锁消除

锁消除是一种更彻底的锁优化。Java虚拟机在JIT编译时，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁。通过锁消除，可以节省毫无意义的请求锁时间。

![image-20200511044729398](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511044729398.png)

### synchronized

synchronized关键字用于为Java对象、方法、代码块提供线程安全的操作。synchronized属于独占式的悲观锁，同时属于可重入锁。在使用synchronized修饰对象时，同一时刻只能有一个线程对该对象进行访问；在synchronized修饰方法、代码块时，同一时刻只能有一个线程执行该方法体或代码块，其他线程只有等待当前线程执行完毕并释放锁资源后才能访问该对象或执行同步代码块。

Java中的每个对象都有个monitor对象，加锁就是在竞争monitor对象。对代码块加锁是通过在前后分别加上monitorenter和monitorexit指令实现的，对方法是否加锁是通过一个标记位来判断的。

#### synchronized的作用范围

synchronized的作用范围如下。

◎ synchronized作用于成员变量和非静态方法时，锁住的是对象的实例，即this对象。

◎ synchronized作用于静态方法时，锁住的是Class实例，因为静态方法属于Class而不属于对象。

◎ synchronized作用于一个代码块时，锁住的是所有代码块中配置的对象。

#### synchronized的实现原理

在synchronized内部包括ContentionList、EntryList、WaitSet、OnDeck、Owner、! Owner这6个区域，每个区域的数据都代表锁的不同状态。

◎ ContentionList：锁竞争队列，所有请求锁的线程都被放在竞争队列中。

◎ EntryList：竞争候选列表，在Contention List中有资格成为候选者来竞争锁资源的线程被移动到了Entry List中。

◎ WaitSet：等待集合，调用wait方法后被阻塞的线程将被放在WaitSet中。

◎ OnDeck：竞争候选者，在同一时刻最多只有一个线程在竞争锁资源，该线程的状态被称为OnDeck。

◎ Owner：竞争到锁资源的线程被称为Owner状态线程。

◎ ！Owner：在Owner线程释放锁后，会从Owner的状态变成！Owner。

synchronized在收到新的锁请求时首先自旋，如果通过自旋也没有获取锁资源，则将被放入锁竞争队列ContentionList中。

为了防止锁竞争时ContentionList尾部的元素被大量的并发线程进行CAS访问而影响性能，Owner线程会在释放锁资源时将ContentionList中的部分线程移动到EntryList中，并指定EntryList中的某个线程（一般是最先进入的线程）为OnDeck线程。Owner线程并没有直接把锁传递给OnDeck线程，而是把锁竞争的权利交给OnDeck，让OnDeck线程重新竞争锁。在Java中把该行为称为“竞争切换”，该行为牺牲了公平性，但提高了性能。

获取到锁资源的OnDeck线程会变为Owner线程，而未获取到锁资源的线程仍然停留在EntryList中。

Owner线程在被wait方法阻塞后，会被转移到WaitSet队列中，直到某个时刻被notify方法或者notifyAll方法唤醒，会再次进入EntryList中。ContentionList、EntryList、WaitSet中的线程均为阻塞状态，该阻塞是由操作系统来完成的（在Linux内核下是采用pthread_mutex_lock内核函数实现的）。

Owner线程在执行完毕后会释放锁的资源并变为！Owner状态，如图3-6所示。

![image-20200417051433176](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417051433176.png)

在synchronized中，在线程进入ContentionList之前，等待的线程会先尝试以自旋的方式获取锁，如果获取不到就进入ContentionList，该做法对于已经进入队列的线程是不公平的，因此synchronized是非公平锁。另外，自旋获取锁的线程也可以直接抢占OnDeck线程的锁资源。

synchronized是一个重量级操作，需要调用操作系统的相关接口，性能较低，给线程加锁的时间有可能超过获取锁后具体逻辑代码的操作时间。

### ReentrantLock

#### ReentrantLock

`ReentrantLock` 即可重入锁，实现了 Lock 和 Serializable 接口。

在 Java 环境下 `ReentrantLock` 和 `synchronized` 都是可重入锁。

- 可重入锁，也叫做递归锁，当一个线程请求得到一个对象锁后再次请求此对象锁，可以再次得到该对象锁。
- 调用 `get()` 方法，同一个线程 ID 会被连续输出两次

```java
public void get() {
        lock.lock();
        System.out.println(Thread.currentThread().getId());
        set();
        lock.unlock();
    }

public void set() {
        lock.lock();
        System.out.println(Thread.currentThread().getId());
        lock.unlock();
}
```

#### 公平锁与非公平锁实现

*公平锁*

- 公平锁表示线程获取锁的顺序是按照

  线程加锁的顺序

  来分配的，即先来先得的 FIFO 先进先出顺序。

  - 每个线程获取锁的过程是公平的，等待时间最长的会最先被唤醒获取锁。

```java
static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;

        final void lock() {
            acquire(1);
        }

        /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
}
```

公平锁 `tryAcquire()` 方法比非公平锁 `nonfairTryAcquire()` 方法多了一个 `hasQueuedPredecessors()` 方法。

- hasQueuedPredecessors 方法判断了头结点是否为当前线程。

```java
public final boolean hasQueuedPredecessors() {
        // The correctness of this depends on head being initialized
        // before tail and on head.next being accurate if the current
        // thread is first in queue.
        Node t = tail; // Read fields in reverse initialization order
        Node h = head;
        Node s;
        return h != t &&
            ((s = h.next) == null || s.thread != Thread.currentThread());
}
```

*非公平锁*

- 而非公平锁就是一种获取锁的抢占机制，是 

  随机获得锁

   的，和公平锁不一样的就是先来的不一定先得到锁，这个方式可能造成某些线程一直拿不到锁，结果也就是不公平的了。

  - 非公平锁可能使线程 " 饥饿 "。

```java
static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;

        /**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }

        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
}
```

#### ReentrantLock 的方法

![image-20200318104912895](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200318104912895.png)

#### synchronized和ReentrantLock的比较

synchronized和ReentrantLock的共同点如下。

◎ 都用于控制多线程对共享对象的访问。

◎ 都是可重入锁。

◎ 都保证了可见性和互斥性。

synchronized和ReentrantLock的不同点如下。

◎ ReentrantLock显式获取和释放锁；synchronized隐式获取和释放锁。为了避免程序出现异常而无法正常释放锁，在使用ReentrantLock时必须在finally控制块中进行解锁操作。

◎ ReentrantLock可响应中断、可轮回，为处理锁提供了更多的灵活性。

◎ ReentrantLock是API级别的，synchronized是JVM级别的。◎ ReentrantLock可以定义公平锁。

◎ ReentrantLock通过Condition可以绑定多个条件。

◎ 二者的底层实现不一样：synchronized是同步阻塞，采用的是悲观并发策略；Lock是同步非阻塞，采用的是乐观并发策略。

◎ Lock是一个接口，而synchronized是Java中的关键字，synchronized是由内置的语言实现的。

◎ 我们通过Lock可以知道有没有成功获取锁，通过synchronized却无法做到。

◎ Lock可以通过分别定义读写锁提高多个线程读操作的效率。

### ReentrantReadWriteLock

#### ReentrantReadWriteLock

现实中有这样一种场景：对共享资源有读和写的操作，且写操作没有读操作那么频繁。在没有写操作的时候，多个线程同时读一个资源没有任何问题，所以应该允许多个线程同时读取共享资源；但是如果一个线程想去写这些共享资源，就不应该允许其他线程对该资源进行读和写的操作了。

　针对这种场景，**JAVA的并发包提供了读写锁ReentrantReadWriteLock，它表示两个锁，一个是读操作相关的锁，称为共享锁；一个是写相关的锁，称为排他锁**，描述如下：

线程进入读锁的前提条件：

没有其他线程的写锁，

没有写请求或者**有写请求，但调用线程和持有锁的线程是同一个。**

线程进入写锁的前提条件：

没有其他线程的读锁

没有其他线程的写锁

而读写锁有以下三个重要的特性：

（1）公平选择性：支持非公平（默认）和公平的锁获取方式，吞吐量还是非公平优于公平。

（2）重进入：读锁和写锁都支持线程重进入。

（3）锁降级：遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级成为读锁。

#### 类的继承

ReentrantReadWriteLock实现了ReadWriteLock接口，ReadWriteLock接口定义了获取读锁和写锁的规范，具体需要实现类去实现；同时其还实现了Serializable接口，表示可以进行序列化，在源代码中可以看到ReentrantReadWriteLock实现了自己的序列化逻辑。

#### 类的内部类

ReentrantReadWriteLock有五个内部类，五个内部类之间也是相互关联的。内部类的关系如下图所示。

![img](https://images2015.cnblogs.com/blog/616953/201604/616953-20160421204144304-278034246.png)

#### 读写状态的设计

同步状态在重入锁的实现中是表示被同一个线程重复获取的次数，即一个整形变量来维护，但是之前的那个表示仅仅表示是否锁定，而不用区分是读锁还是写锁。而读写锁需要在同步状态（一个整形变量）上维护多个读线程和一个写线程的状态。

读写锁对于同步状态的实现是在一个整形变量上通过“按位切割使用”：将变量切割成两部分，高16位表示读，低16位表示写。

![http://static.open-open.com/lib/uploadImg/20151031/20151031223319_397.png](http://static.open-open.com/lib/uploadImg/20151031/20151031223319_397.png)

假设当前同步状态值为S，get和set的操作如下：

（1）获取写状态：

  S&0x0000FFFF:将高16位全部抹去

（2）获取读状态：

  S>>>16:无符号补0，右移16位

（3）写状态加1：

   S+1

（4）读状态加1：

　　S+（1<<16）即S + 0x00010000

在代码层的判断中，如果S不等于0，当写状态（S&0x0000FFFF），而读状态（S>>>16）大于0，则表示该读写锁的读锁已被获取。

#### 写锁的获取

![img](https://images2018.cnblogs.com/blog/249993/201806/249993-20180607130802005-1386429088.png)

####  写锁的释放

![img](https://images2018.cnblogs.com/blog/249993/201806/249993-20180607131006282-1633551158.png)

#### 读锁的获取

![img](https://images2018.cnblogs.com/blog/249993/201806/249993-20180607131704903-887096141.png)

#### 读锁的释放

![img](https://images2018.cnblogs.com/blog/249993/201806/249993-20180607132608771-1291388784.png)

### Condition

#### Condition

在Java程序中，任意一个Java对象，都拥有一组监视器方法（定义在java.lang.Object类上），主要包括wait()、wait(long)、notify()、notifyAll()方法，这些方法与synchronized关键字配合，可以实现等待/通知模式。Condition接口也提供了类似Object的监视器方法，与Lock配合可以实现等待/通知模式，但是这两者在使用方式以及功能特性上还是有区别的。Object的监视器方法与Condition接口对比如下：

![image-20200511053526152](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511053526152.png)

Condition提供了一系列的方法来对阻塞和唤醒线程：

```java
public interface Condition {
    void await() throws InterruptedException;
    void awaitUninterruptibly();
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    boolean await(long time, TimeUnit unit) throws InterruptedException;
    boolean awaitUntil(Date deadline) throws InterruptedException;
    void signal();
    void signalAll();
}
```

Condition的方法以及描述如下：

![image-20200318110559633](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200318110559633.png)

#### Condition的实现分析

可以通过Lock.newCondition()方法获取Condition对象，而我们知道Lock对于同步状态的实现都是通过内部的自定义同步器实现的，newCondition()方法也不例外，所以，Condition接口的唯一实现类是同步器AQS的内部类ConditionObject，因为Condition的操作需要获取相关的锁，所以作为同步器的内部类也比较合理，该类定义如下：

```java
public class ConditionObject implements Condition, java.io.Serializable
```

每个Condition对象都包含着一个队列（以下称为等待队列），该队列是Condition对象实现等待/通知功能的关键。

#### 等待队列

![img](https://img-blog.csdn.net/20180603113359845?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180603113842894?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 等待

![img](https://img-blog.csdn.net/20180603140010335?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 通知

![img](https://img-blog.csdn.net/20180603141604705?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### StampedLock

#### StampedLock

StampedLock是Java 8中引入的一种新的锁机制，可以认为它是读写锁的一个改进版本。读写锁虽然分离了读和写的功能，使得读与读之间可以完全并发。但是，读和写之间依然是冲突的。读锁会完全阻塞写锁，它使用的依然是悲观的锁策略，如果有大量的读线程，它也有可能引起写线程的“饥饿”。而StampedLock则提供了一种乐观的读策略。这种乐观的锁非常类似无锁的操作，使得乐观锁完全不会阻塞写线程。

#### StampedLock的引入

ReentrantReadWriteLock使得多个读线程同时持有读锁（只要写锁未被占用），而写锁是独占的。

但是，读写锁如果使用不当，很容易产生“饥饿”问题：比如在读线程非常多，写线程很少的情况下，很容易导致写线程“饥饿”，虽然使用“公平”策略可以一定程度上缓解这个问题，但是“公平”策略是以牺牲系统吞吐量为代价的。

#### StampedLock的特点

StampedLock的主要特点概括一下，有以下几点：

1. 所有获取锁的方法，都返回一个邮戳（Stamp），Stamp为0表示获取失败，其余都表示成功；
2. 所有释放锁的方法，都需要一个邮戳（Stamp），这个Stamp必须是和成功获取锁时得到的Stamp一致；
3. StampedLock是不可重入的；（如果一个线程已经持有了写锁，再去获取写锁的话就会造成死锁）
4. StampedLock有三种访问模式：
   ①Reading（读模式）：功能和ReentrantReadWriteLock的读锁类似
   ②Writing（写模式）：功能和ReentrantReadWriteLock的写锁类似
   ③Optimistic reading（乐观读模式）：这是一种优化的读模式。
5. StampedLock支持读锁和写锁的相互转换
   我们知道RRW中，当线程获取到写锁后，可以降级为读锁，但是读锁是不能直接升级为写锁的。
   StampedLock提供了读锁和写锁相互转换的功能，使得该类支持更多的应用场景。
6. 无论写锁还是读锁，都不支持Conditon等待

#### 3种读写模式的锁

StampedLock 给我们提供了3种读写模式的锁，如下：

1.写锁writeLock是一个独占锁，同时只有一个线程可以获取该锁，当一个线程获取该锁后，其他请求读锁和写锁的线程必须等待，这跟ReentrantReadWriteLock 的写锁很相似，不过要注意的是StampedLock的写锁是不可重入锁。

2.**悲观锁readLock**，是个共享锁，在没有线程获取独占写锁的情况下，同时多个线程可以获取该锁；如果已经有线程持有写锁，其他线程请求获取该锁会被阻塞，这类似ReentrantReadWriteLock 的读锁（不同在于这里的读锁是不可重入锁）。

3.**乐观读锁 tryOptimisticRead**，是相对于悲观锁来说的，在操作数据前并没有通过 CAS 设置锁的状态，仅仅是通过位运算测试；如果当前没有线程持有写锁，则简单的返回一个非 0 的 stamp 版本信息，获取该 stamp 后在具体操作数据前还需要调用 validate 验证下该 stamp 是否已经不可用，也就是看当调用 tryOptimisticRead 返回 stamp 后，到当前时间是否有其它线程持有了写锁，如果是那么 validate 会返回 0，否者就可以使用该 stamp 版本的锁对数据进行操作。由于 tryOptimisticRead 并没有使用 CAS 设置锁状态，所以不需要显示的释放该锁。该锁的一个特点是适用于读多写少的场景，因为获取读锁只是使用位操作进行检验，不涉及 CAS 操作，所以效率会高很多，但是同时由于没有使用真正的锁，在保证数据一致性上需要拷贝一份要操作的变量到方法栈，并且在操作数据时候可能其它写线程已经修改了数据，而我们操作的是方法栈里面的数据，也就是一个快照，所以最多返回的不是最新的数据，但是一致性还是得到保障的。

## 并发工具类

### CyclicBarrier

#### CyclicBarrier

CyclicBarrier也叫同步屏障，在JDK1.5被引入，可以让一组线程达到一个屏障时被阻塞，直到最后一个线程达到屏障时，所有被阻塞的线程才能继续执行。
 CyclicBarrier好比一扇门，默认情况下关闭状态，堵住了线程执行的道路，直到所有线程都就位，门才打开，让所有线程一起通过。

![img](https://upload-images.jianshu.io/upload_images/11464886-41e109b00bdb88c2.png?imageMogr2/auto-orient/strip|imageView2/2/w/428/format/webp)

#### **构造方法**

默认的构造方法是CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程调用await方法告诉CyclicBarrier已经到达屏障位置，线程被阻塞。

另外一个构造方法CyclicBarrier(int parties, Runnable barrierAction)，其中barrierAction任务会在所有线程到达屏障后执行。

![img](https://upload-images.jianshu.io/upload_images/2184951-b972911b7debef14.png?imageMogr2/auto-orient/strip|imageView2/2/w/566/format/webp)

#### CyclicBarrier的类图

![img](https://img-blog.csdn.net/20180603173118965?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过类图我们可以看到，CyclicBarrier内部使用了ReentrantLock和Condition两个类。它有两个构造函数：

```java
public CyclicBarrier(int parties) {
    this(parties, null);
}
 
public CyclicBarrier(int parties, Runnable barrierAction) {
    if (parties <= 0) throw new IllegalArgumentException();
    this.parties = parties;
    this.count = parties;
    this.barrierCommand = barrierAction;
}
```

CyclicBarrier默认的构造方法是CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程使用await()方法告诉CyclicBarrier我已经到达了屏障，然后当前线程被阻塞。

CyclicBarrier的另一个构造函数CyclicBarrier(int parties, Runnable barrierAction)，用于线程到达屏障时，优先执行barrierAction，方便处理更复杂的业务场景。

#### await方法

调用await方法的线程告诉CyclicBarrier自己已经到达同步点，然后当前线程被阻塞。直到parties个参与线程调用了await方法，CyclicBarrier同样提供带超时时间的await和不带超时时间的await方法：

```java
public int await() throws InterruptedException, BrokenBarrierException {
    try {
        // 不超时等待
        return dowait(false, 0L);
    } catch (TimeoutException toe) {
        throw new Error(toe); // cannot happen
    }
}
```

```java

public int await(long timeout, TimeUnit unit)
    throws InterruptedException,
            BrokenBarrierException,
            TimeoutException {
    return dowait(true, unit.toNanos(timeout));
}
```

CyclicBarrier使用独占锁来执行await方法，并发性可能不是很高

如果在等待过程中，线程被中断了，就抛出异常。但如果中断的线程所对应的CyclicBarrier不是这代的，比如，在最后一次线程执行signalAll后，并且更新了这个“代”对象。在这个区间，这个线程被中断了，那么，JDK认为任务已经完成了，就不必在乎中断了，只需要打个标记。该部分源码已在dowait(boolean, long)方法中进行了注释。

如果线程被其他的CyclicBarrier唤醒了，那么g肯定等于generation，这个事件就不能return了，而是继续循环阻塞。反之，如果是当前CyclicBarrier唤醒的，就返回线程在CyclicBarrier的下标。完成了一次冲过栅栏的过程。该部分源码已在dowait(boolean, long)方法中进行了注释。

### CountDownLatch

#### CountDownLatch

CountDownLatch是一个同步辅助类，它允许一个或多个线程一直等待直到其他线程执行完毕才开始执行。

用给定的**计数**初始化CountDownLatch，其含义是要被等待执行完的线程个数。

每次调用CountDown()，**计数**减1

主程序执行到await()函数会阻塞等待线程的执行，直到计数为0

![img](https://upload-images.jianshu.io/upload_images/11464886-8d35a1901ff4e80c.png?imageMogr2/auto-orient/strip|imageView2/2/w/414/format/webp)

#### 实现原理

计数器通过使用锁（共享锁、排它锁）实现。

#### 与CyclicBarrier区别

1、CyclicBarrier的某个线程运行到某个点后停止运行，直到所有线程都达到同一个点，所有线程才会重新运行；

​      CountDownLatch线程运行到某个点后，计数值-1，该线程继续运行，直到计数值为0，则停止运行；

2、CyclicBarrier只能唤醒一个任务；CountDownLatch可以唤醒多个任务；

3、CyccliBarrier可以重用，CountDownLatch不可重用，当计数值为0时，CountDownLatch就不可再用了。

### Semaphore

#### Semaphore

**Semaphore**，是**JDK1.5**的**java.util.concurrent并发包**中提供的一个并发工具类。

所谓**Semaphore**即 **信号量** 的意思。

Semaphore是一个计数信号量。
从概念上将，Semaphore包含一组许可证。
如果有需要的话，每个acquire()方法都会阻塞，直到获取一个可用的许可证。
每个release()方法都会释放持有许可证的线程，并且归还Semaphore一个可用的许可证。
然而，实际上并没有真实的许可证对象供线程使用，Semaphore只是对可用的数量进行管理维护。

#### Semaphore方法

——Semaphore(permits)

初始化许可证数量的构造函数

——Semaphore(permits,fair)

初始化许可证数量和是否公平模式的构造函数

——isFair()

是否公平模式FIFO

——availablePermits()

获取当前可用的许可证数量

——acquire()

当前线程尝试去阻塞的获取1个许可证。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了1个可用的许可证，则会停止等待，继续执行。
当前线程被中断，则会抛出InterruptedException异常，并停止等待，继续执行。
——acquire(permits)

当前线程尝试去阻塞的获取permits个许可证。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了n个可用的许可证，则会停止等待，继续执行。
当前线程被中断，则会抛出InterruptedException异常，并停止等待，继续执行。
——acquierUninterruptibly()

当前线程尝试去阻塞的获取1个许可证(不可中断的)。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了1个可用的许可证，则会停止等待，继续执行。
——acquireUninterruptibly(permits)

当前线程尝试去阻塞的获取permits个许可证。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了n个可用的许可证，则会停止等待，继续执行。
——tryAcquire()

当前线程尝试去获取1个许可证。

此过程是非阻塞的，它只是在方法调用时进行一次尝试。

如果当前线程获取了1个可用的许可证，则会停止等待，继续执行，并返回true。

如果当前线程没有获得这个许可证，也会停止等待，继续执行，并返回false。

——tryAcquire(permits)

当前线程尝试去获取permits个许可证。

此过程是非阻塞的，它只是在方法调用时进行一次尝试。

如果当前线程获取了permits个可用的许可证，则会停止等待，继续执行，并返回true。

如果当前线程没有获得permits个许可证，也会停止等待，继续执行，并返回false。

——tryAcquire(timeout,TimeUnit)

当前线程在限定时间内，阻塞的尝试去获取1个许可证。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了可用的许可证，则会停止等待，继续执行，并返回true。
当前线程等待时间timeout超时，则会停止等待，继续执行，并返回false。
当前线程在timeout时间内被中断，则会抛出InterruptedException一次，并停止等待，继续执行。
——tryAcquire(permits,timeout,TimeUnit)

当前线程在限定时间内，阻塞的尝试去获取permits个许可证。

此过程是阻塞的，它会一直等待许可证，直到发生以下任意一件事：

当前线程获取了可用的permits个许可证，则会停止等待，继续执行，并返回true。
当前线程等待时间timeout超时，则会停止等待，继续执行，并返回false。
当前线程在timeout时间内被中断，则会抛出InterruptedException一次，并停止等待，继续执行。
——release()

当前线程释放1个可用的许可证。

——release(permits)

当前线程释放permits个可用的许可证。

——drainPermits()

当前线程获得剩余的所有可用许可证。

——hasQueuedThreads()

判断当前Semaphore对象上是否存在正在等待许可证的线程。

——getQueueLength()

获取当前Semaphore对象上是正在等待许可证的线程数量。

#### Semaphore应用场景

**Semaphore**经常用于限制获取某种资源的线程数量。

###  Exchanger

####  Exchanger

Exchanger 是 JDK 1.5 开始提供的一个用于两个工作线程之间交换数据的封装工具类，简单说就是一个线程在完成一定的事务后想与另一个线程交换数据，则第一个先拿出数据的线程会一直等待第二个线程，直到第二个线程拿着数据到来时才能彼此交换对应数据。其定义为 `Exchanger` 泛型类型，其中 V 表示可交换的数据类型，对外提供的接口很简单，具体如下：

`Exchanger()：`无参构造方法。

`V exchange(V v)：`等待另一个线程到达此交换点（除非当前线程被中断），然后将给定的对象传送给该线程，并接收该线程的对象。

`V exchange(V v, long timeout, TimeUnit unit)：`等待另一个线程到达此交换点（除非当前线程被中断或超出了指定的等待时间），然后将给定的对象传送给该线程，并接收该线程的对象。

### Phaser

Phaser使我们能够建立在逻辑线程需要才去执行下一步的障碍等。

我们可以协调多个执行阶段，为每个程序阶段重用Phaser实例。每个阶段可以有不同数量的线程等待前进到另一个阶段。我们稍后会看一个使用阶段的示例。

要参与协调，线程需要使用Phaser实例 register() 本身。请注意：这只会增加注册方的数量，我们无法检查当前线程是否已注册 - 我们必须将实现子类化以支持此操作。

线程通过调用 arriAndAwaitAdvance() 来阻止它到达屏障，这是一种阻塞方法。当数量到达等于注册的数量时，程序的执行将继续，并且数量将增加。我们可以通过调用getPhase（）方法获取当前数量。

当线程完成其工作时，我们应该调用arrivalAndDeregister（）方法来表示在此特定阶段不再考虑当前线程。

Phaser对象有两种状态。

• Active：Phaser在接受新参与者注册及每个阶段结束后同步时，便进入该状态。在该状态下，Phaser以本节介绍的方式进行工作。在Java API中，未明确提出该状态。

• Termination：当Phaser的所有参与者都注销时，默认就会进入终止状态。更进一步说，是Phaser的onAdvance()方法在参与者数量为0时，返回了true，该方法返回true就会导致Phaser进入终止状态。可以重写该方法来修改这种默认行文。当Phaser处于该状态时，同步方法arriveAndAwaitAdvance()将立即返回，不会有任何同步操作。

![image-20200422141922168](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200422141922168.png)

## Thread

### 并发

并发(concurrency)：指在同一时刻只能有一条指令执行，但多个进程指令被快速的轮换执行，使得在宏观上具有多个进程同时执行的效果，但在微观上并不是同时执行的，只是把时间分成若干段，使多个进程快速交替的执行。

### 并行

并行(parallel)：指在同一时刻，有多条指令在多个处理器上同时执行。所以无论从微观还是从宏观来看，二者都是一起执行的。

### 临界区

临界区用来表示一种公共资源或者说共享数据，可以被多个线程使用。但是每一次，只能有一个线程使用它，一旦临界区资源被占用，其他线程要想使用这个资源就必须等待。

### 阻塞

比如一个线程占用了临界区资源，那么其他所有需要这个资源的线程就必须在这个临界区中等待。等待会导致线程挂起，这种情况就是阻塞。

### 非阻塞

非阻塞的意思与之相反，它强调没有一个线程可以妨碍其他线程执行，所有的线程都会尝试不断前向执行

### 进程线程

进程是代码在数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，线程则是进程的一个执行路径，一个进程中至少有一个线程，进程中的多个线程共享进程的资源。

### 使用场景

1、耗时的操作使用线程（异步操作），提高应用程序响应
 2、并行操作时使用线程，如C/S架构的服务器端并发线程响应用户的请求（多线程）。
 3 、多CPU系统中，使用线程提高CPU利用率
 4、改善程序结构。一个既长又复杂的进程可以考虑分为多个线程，成为几个独立或半独立的运行部分，这样的程序会利于理解和修改。

### 线程的状态

![img](https://upload-images.jianshu.io/upload_images/5259260-a3302b084dcb1a71.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)



|   状态   |   含义   | 内容                                                         |
| :------: | :------: | :----------------------------------------------------------- |
|   New    | 新建状态 | 当线程对象对创建后，即进入了新建状态，如：Thread t = new MyThread(); |
| Runnable | 就绪状态 | 当调用线程对象的start()方法（t.start();），线程即进入就绪状态。处于就绪状态的线程，只是说明此线程已经做好了准备，随时等待CPU调度执行，并不是说执行了t.start()此线程立即就会执行； |
| Running  | 运行状态 | 当CPU开始调度处于就绪状态的线程时，此时线程才得以真正执行，即进入到运行状态。注：就     绪状态是进入到运行状态的唯一入口，也就是说，线程要想进入运行状态执行，首先必须处于就绪状态中； |
| Blocked  | 阻塞状态 | 处于运行状态中的线程由于某种原因，暂时放弃对CPU的使用权，停止执行，此时进入阻塞状态，直到其进入到就绪状态，才 有机会再次被CPU调用以进入到运行状态。根据阻塞产生的原因不同，阻塞状态又可以分为三种： |
|    -     | 等待阻塞 | 运行状态中的线程执行wait()方法，使本线程进入到等待阻塞状态； |
|    -     | 同步阻塞 | 线程在获取synchronized同步锁失败(因为锁被其它线程所占用)，它会进入同步阻塞状态； |
|    -     | 其他阻塞 | 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。 |
|   Dead   | 死亡状态 | 线程执行完了或者因异常退出了run()方法，该线程结束生命周期。  |

### 上下文切换

CPU通过时间片分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再加载这个任务的状态。所以任务从保存到再加载的过程就是一次上下文切换。

### 线程创建与运行

Java中有三种线程创建方式，分别为实现Runnable接口的run方法，继承Thread类并重写run的方法，使用FutureTask方式。

使用继承方式的好处是方便传参，你可以在子类里面添加成员变量，通过set方法设置参数或者通过构造函数进行传递，而如果使用Runnable方式，则只能使用主线程里面被声明为final的变量。不好的地方是Java不支持多继承，如果继承了Thread类，那么子类不能再继承其他类，而Runable则没有这个限制。前两种方式都没办法拿到任务的返回结果，但是Futuretask方式可以。

### **创建线程的方式**

1 继承 Thread 类创建线程；
        A）定义 Thread 类的子类，并重写该类的 run() 方法，该 run() 方法的方法体就代表了线程要完成的任务，因此把 run() 方法称为执行体；

​       B）创建 Thread 子类的实例，即创建了线程对象；

​       C）调用线程对象的 start() 方法来启动该线程。

2 实现 Runnable 接口创建线程；
         A）定义 runnable 接口的实现类，并重写该接口的 run() 方法，该 run() 方法的方法体同样是该线程的线程执行体；

​        B）创建 Runnable 接口实现类的实例，并依此实例作为 Thread 的 target 来创建 Thread 对象，该 Thread 对象才是真正的线程对象；

​         C）调用线程对象的 start() 方法来启动该线程。

3 使用 Callable 和 Future 创建线程。

​         A）创建 Callable 接口的实现类，并实现 call() 方法，该 call() 方法将作为线程执行体，并且有返回值；

​         B）创建 Callable 实现类的实例，使用 Future Task 类来包装 Callable 对象，该 Future Task 对象封装了该 Callable 对象的 call() 方法的返回值；

​         C）使用 Future Task 对象作为 Thread 对象的 target 创建并启动新线程；

​         D）调用 Future Task 对象的 get() 方法来获得子线程执行结束后的返回值。

###  wait()函数

当一个线程调用一个共享变量的wait（）方法时，该调用线程会被阻塞挂起，直到发生下面几件事情之一才返回：

（1）其他线程调用了该共享对象的notify（）或者notifyAll（）方法；

（2）其他线程调用了该线程的interrupt（）方法，该线程抛出InterruptedException异常返回。

### 虚假唤醒

另外需要注意的是，一个线程可以从挂起状态变为可以运行状态（也就是被唤醒），即使该线程没有被其他线程调用notify（）、notifyAll（）方法进行通知，或者被中断，或者等待超时，这就是所谓的虚假唤醒。

### interrupt()方法

Thread.interrupt()方法是一个实例方法。它通知目标线程中断，也就是设置中断标志位。中断标志位表示当前线程已经被中断了。Thread.isInterrupted()方法也是实例方法，它判断当前线程是否被中断（通过检查中断标志位）。最后的静态方法Thread.interrupted()也可用来判断当前线程的中断状态，但同时会清除当前线程的中断标志位状态。

isInterrupted()方法和interrupted()方法之间有一个重要的不同点：isInterrupted()方法不会修改线程的是否中断属性，而interrupted()方法会将中断属性设置为false。

### notify() 方法

一个线程调用共享对象的notify（）方法后，会唤醒一个在该共享变量上调用wait系列方法后被挂起的线程。一个共享变量上可能会有多个线程在等待，具体唤醒哪个等待的线程是随机的。

### notifyAll() 方法

不同于在共享变量上调用notify（）函数会唤醒被阻塞到该共享变量上的一个线程，notifyAll（）方法则会唤醒所有在该共享变量上由于调用wait系列方法而被挂起的线程。

1）使用wait()、notify()和notifyAll()时需要先对调用对象加锁。

2）调用wait()方法后，线程状态由RUNNING变为WAITING，并将当前线程放置到对象的等待队列。

3）notify()或notifyAll()方法调用后，等待线程依旧不会从wait()返回，需要调用notify()或notifAll()的线程释放锁之后，等待线程才有机会从wait()返回。

4）notify()方法将等待队列中的一个等待线程从等待队列中移到同步队列中，而notifyAll()方法则是将等待队列中所有的线程全部移到同步队列，被移动的线程状态由WAITING变为BLOCKED。

5）从wait()方法返回的前提是获得了调用对象的锁。

### suspend()方法

不推荐使用suspend()方法去挂起线程是因为suspend()方法在导致线程暂停的同时，并不会释放任何锁资源。此时，其他任何线程想要访问被它占用的锁时，都会被牵连，导致无法正常继续运行（如图2.7所示）。直到对应的线程上进行了resume()方法操作，被挂起的线程才能继续，从而其他所有阻塞在相关锁上的线程也可以继续执行。但是，如果resume()方法操作意外地在suspend()方法前就执行了，那么被挂起的线程可能很难有机会被继续执行。并且，更严重的是：它所占用的锁不会被释放，因此可能会导致整个系统工作不正常。而且，对于被挂起的线程，从它的线程状态上看，居然还是Runnable，这也会严重影响我们对系统当前状态的判断。

### run（）方法与start（）方法

通常，系统通过调用线程类的start（）方法来启动一个线程，此时该线程处于就绪状态，而非运行状态，也就意味着这个线程可以被JVM来调度执行。在调度过程中，JVM通过调用线程类的run（）方法来完成实际的操作，当run（）方法结束后，此线程就会终止。

如果直接调用线程类的run（）方法，这会被当作一个普通的函数调用，程序中仍然只有主线程这一个线程，也就是说，start方法（）能够异步地调用run（）方法，但是直接调用run（）方法却是同步的，因此也就无法达到多线程的目的。

### sleep方法

Thread类中有一个静态的sleep方法，当一个执行中的线程调用了Thread的sleep方法后，调用线程会暂时让出指定时间的执行权，也就是在这期间不参与CPU的调度，但是该线程所拥有的监视器资源，比如锁还是持有不让出的。指定的睡眠时间到了后该函数会正常返回，线程就处于就绪状态，然后参与CPU的调度，获取到CPU资源后就可以继续运行了。如果在睡眠期间其他线程调用了该线程的interrupt（）方法中断了该线程，则该线程会在调用sleep方法的地方抛出InterruptedException异常而返回。

### yield方法

Thread类中有一个静态的yield方法，当一个线程调用yield方法时，实际就是在暗示线程调度器当前线程请求让出自己的CPU使用，但是线程调度器可以无条件忽略这个暗示。

### Future

Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。

　　Future类位于java.util.concurrent包下，它是一个接口：

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

在Future接口中声明了5个方法，下面依次解释每个方法的作用：

- cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
- isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
- isDone方法表示任务是否已经完成，若任务完成，则返回true；
- get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
- get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。

　　也就是说Future提供了三种功能：

　　1）判断任务是否完成；

　　2）能够中断任务；

　　3）能够获取任务执行结果。

### FutureTask

FutureTask代表了一个可被取消的异步计算任务，该类实现了Future接口，比如提供了启动和取消任务、查询任务是否完成、获取计算结果的接口。

FutureTask任务的结果只有当任务完成后才能获取，并且只能通过get系列方法获取，当结果还没出来时，线程调用get系列方法会被阻塞。另外，一旦任务被执行完成，任务将不能重启，除非运行时使用了runAndReset方法。FutureTask中的任务可以是Callable类型，也可以是Runnable类型（因为FutureTask实现了Runnable接口）, FutureTask类型的任务可以被提交到线程池执行。

### CompletableFuture

使用Future确实可以获取异步任务的执行结果，但是获取其结果还是会阻塞调用线程的，并没有实现完全异步化处理，所以在JDK8中提供了CompletableFuture来弥补其缺点。CompletableFuture类允许以非阻塞方式和基于通知的方式处理结果，其通过设置回调函数方式，让主线程彻底解放出来，实现了实际意义上的异步处理。

用CompletableFuture时，当异步单元返回futureB后，调用线程可以在其上调用whenComplete方法设置一个回调函数action，然后调用线程就会马上返回，等异步任务执行完毕后会使用异步线程来执行回调函数action，而无须调用线程干预。

![image-20200311201137658](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200311201137658.png)

### LockSupport

#### LockSupport

LockSupport是一个非常方便实用的线程阻塞工具，它可以在线程内任意位置让线程阻塞。与Thread.suspend()方法相比，它弥补了由于resume()方法发生导致线程无法继续执行的情况。和Object.wait()方法相比，它不需要先获得某个对象的锁，也不会抛出InterruptedException异常。

使用wait，notify来实现等待唤醒功能至少有两个缺点：

- 1.由上面的例子可知,wait和notify都是Object中的方法,在调用这两个方法前必须先获得锁对象，这限制了其使用场合:只能在同步代码块中。
- 2.另一个缺点可能上面的例子不太明显，当对象的等待队列中有多个线程时，notify只能随机选择一个线程唤醒，无法唤醒指定的线程。

而使用LockSupport的话，我们可以在任何场合使线程阻塞，同时也可以指定要唤醒的线程，相当的方便。

#### LockSupport方法

> **阻塞线程**

1. void park()：阻塞当前线程，如果调用unpark方法或者当前线程被中断，从能从park()方法中返回
2. void park(Object blocker)：功能同方法1，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；
3. void parkNanos(long nanos)：阻塞当前线程，最长不超过nanos纳秒，增加了超时返回的特性；
4. void parkNanos(Object blocker, long nanos)：功能同方法3，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；
5. void parkUntil(long deadline)：阻塞当前线程，知道deadline；
6. void parkUntil(Object blocker, long deadline)：功能同方法5，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；

> **唤醒线程**

void unpark(Thread thread):唤醒处于阻塞状态的指定线程。

LockSupport就是通过控制变量`_counter`来对线程阻塞唤醒进行控制的。原理有点类似于信号量机制。

- 当调用`park()`方法时，会将_counter置为0，同时判断前值，小于1说明前面被`unpark`过,则直接退出，否则将使该线程阻塞。
- 当调用`unpark()`方法时，会将_counter置为1，同时判断前值，小于1会进行线程唤醒，否则直接退出。
  形象的理解，线程阻塞需要消耗凭证(permit)，这个凭证最多只有1个。当调用park方法时，如果有凭证，则会直接消耗掉这个凭证然后正常退出；但是如果没有凭证，就必须阻塞等待凭证可用；而unpark则相反，它会增加一个凭证，但凭证最多只能有1个。
- 为什么可以先唤醒线程后阻塞线程？
  因为unpark获得了一个凭证,之后调用park因为有凭证消费，故不会阻塞。
- 为什么唤醒两次后阻塞两次会阻塞线程。
  因为凭证的数量最多为1，连续调用两次unpark和调用一次unpark效果一样，只会增加一个凭证；而调用两次park却需要消费两个凭证。

### 线程死锁

死锁是指两个或两个以上的线程在执行过程中，因争夺资源而造成的互相等待的现象，在无外力作用的情况下，这些线程会一直相互等待而无法继续运行下去。

那么为什么会产生死锁呢？学过操作系统的朋友应该都知道，死锁的产生必须具备以下四个条件。

● 互斥条件：指线程对已经获取到的资源进行排它性使用，即该资源同时只由一个线程占用。如果此时还有其他线程请求获取该资源，则请求者只能等待，直至占有资源的线程释放该资源。

● 请求并持有条件：指一个线程已经持有了至少一个资源，但又提出了新的资源请求，而新资源已被其他线程占有，所以当前线程会被阻塞，但阻塞的同时并不释放自己已经获取的资源。

● 不可剥夺条件：指线程获取到的资源在自己使用完之前不能被其他线程抢占，只有在自己使用完毕后才由自己释放该资源。

● 环路等待条件：指在发生死锁时，必然存在一个线程—资源的环形链，即线程集合{T0, T1, T2, …, Tn}中的T0正在等待一个T1占用的资源，T1正在等待T2占用的资源，……Tn正在等待已被T0占用的资源。

### 守护线程与用户线程

Java中的线程分为两类，分别为daemon线程（守护线程）和user线程（用户线程）。在JVM启动时会调用main函数，main函数所在的线程就是一个用户线程，其实在JVM内部同时还启动了好多守护线程，比如垃圾回收线程。那么守护线程和用户线程有什么区别呢？区别之一是当最后一个非守护线程结束时，JVM会正常退出，而不管当前是否有守护线程，也就是说守护线程是否结束并不影响JVM的退出。言外之意，只要有一个用户线程还没结束，正常情况下JVM就不会退出。

### 线程调度

#### 抢占式调度

抢占式调度指每个线程都以抢占的方式获取CPU资源并快速执行，在执行完毕后立刻释放CPU资源，具体哪些线程能抢占到CPU资源由操作系统控制，在抢占式调度模式下，每个线程对CPU资源的申请地位是相等，从概率上讲每个线程都有机会获得同样的CPU执行时间片并发执行。抢占式调度适用于多线程并发执行的情况，在这种机制下一个线程的堵塞不会导致整个进程性能下降。具体流程如图3-12所示。

![image-20200417051945756](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417051945756.png)

#### 协同式调度

协同式调度指某一个线程在执行完后主动通知操作系统将CPU资源切换到另一个线程上执行。线程对CPU的持有时间由线程自身控制，线程切换更加透明，更适合多个线程交替执行某些任务的情况。

同式调度有一个缺点：如果其中一个线程因为外部原因（可能是磁盘I/O阻塞、网络I/O阻塞、请求数据库等待）运行阻塞，那么可能导致整个系统阻塞甚至崩溃。具体流程如图3-13所示。

![image-20200417052009356](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417052009356.png)

#### Java线程调度

Java采用抢占式调度的方式实现内部的线程调度，Java会为每个线程都按照优先级高低分配不同的CPU时间片，且优先级高的线程优先执行。优先级低的线程只是获取CPU时间片的优先级被降低，但不会永久分配不到CPU时间片。Java的线程调度在保障效率的前提下尽可能保障线程调度的公平性。

线程让出CPU的情况如下。◎ 当前运行的线程主动放弃CPU，例如运行中的线程调用yield()放弃CPU的使用权。◎ 当前运行的线程进入阻塞状态，例如调用文件读取I/O操作、锁等待、Socket等待。◎ 当前线程运行结束，即运行完run()里面的任务。

### 进程调度算法

进程调度算法包括优先调度算法、高优先权优先调度算法和基于时间片的轮转调度算法。其中，优先调度算法分为先来先服务调度算法和短作业优先调度算法；高优先权优先调度算法分为非抢占式优先权算法、抢占式优先权调度算法和高响应比优先调度算法。基于时间片的轮转调度算法分为时间片轮转算法和多级反馈队列调度算法。

#### 优先调度算法

优先调度算法包含先来先服务调度算法和短作业（进程）优先调度算法。

1．先来先服务调度算法

先来先服务调度算法指每次调度时都从队列中选择一个或多个最早进入该队列的作业，为其分配资源、创建进程和放入就绪队列。调度算法在获取到可用的CPU资源时会从就绪队列中选择一个最早进入队列的进程，为其分配CPU资源并运行。该算法优先运行最早进入的任务，实现简单且相对公平。

2．短作业优先调度算法	

短作业优先调度算法指每次调度时都从队列中选择一个或若干个预估运行时间最短的作业，为其分配资源、创建进程和放入就绪队列。调度算法在获取到可用的CPU资源时，会从就绪队列中选出一个预估运行时间最短的进程，为其分配CPU资源并运行。该算法优先运行短时间作业，以提高CPU整体的利用率和系统运行效率，某些大任务可能会出现长时间得不到调度的情况。

#### 高优先权优先调度算法

高优先权优先调度算法在定义任务的时候为每个任务都设置不同的优先权，在进行任务调度时优先权最高的任务首先被调度，这样资源的分配将更加灵活，具体包含非抢占式优先调度算法、抢占式优先调度算法和高响应比优先调度算法。

1．非抢占式优先调度算法

非抢占式优先调度算法在每次调度时都从队列中选择一个或多个优先权最高的作业，为其分配资源、创建进程和放入就绪队列。调度算法在获取到可用的CPU资源时会从就绪队列中选出一个优先权最高的进程，为其分配CPU资源并运行。进程在运行过程中一直持有该CPU，直到进程执行完毕或发生异常而放弃该CPU。该算法优先运行优先权高的作业，且一旦将CPU分配给某个进程，就不会主动回收CPU资源，直到任务主动放弃。

2．抢占式优先调度算法

抢占式优先调度算法首先把CPU资源分配给优先权最高的任务并运行，但如果在运行过程中出现比当前运行任务优先权更高的任务，调度算法就会暂停运行该任务并回收CPU资源，为其分配新的优先权更高的任务。该算法真正保障了CPU在整个运行过程中完全按照任务的优先权分配资源，这样如果临时有紧急作业，则也可以保障其第一时间被执行。

3．高响应比优先调度算法

高响应比优先调度算法使用了动态优先权的概念，即任务的执行时间越短，其优先权越高，任务的等待时间越长，优先权越高，这样既保障了快速、并发地执行短作业，也保障了优先权低但长时间等待的任务也有被调度的可能性。

该优先权的变化规律如下。

◎ 在作业的等待时间相同时，运行时间越短，优先权越高，在这种情况下遵循的是短作业优先原则。

◎ 在作业的运行时间相同时，等待时间越长，优先权越高，在这种情况下遵循的是先来先服务原则。

◎ 作业的优先权随作业等待时间的增加而不断提高，加大了长作业获取CPU资源的可能性。

#### 时间片的轮转调度算法

时间片的轮转调度算法将CPU资源分成不同的时间片，不同的时间片为不同的任务服务，具体包括时间片轮转法和多级反馈队列调度算法。

1．时间片轮转法

时间片轮转法指按照先来先服务原则从就绪队列中取出一个任务，并为该任务分配一定的CPU时间片去运行，在进程使用完CPU时间片后由一个时间计时器发出时钟中断请求，调度器在收到时钟中断请求信号后停止该进程的运行并将该进程放入就绪队列的队尾，然后从就绪队列的队首取出一个任务并为其分配CPU时间片去执行。这样，就绪队列中的任务就将轮流获取一定的CPU时间片去运行。

2．多级反馈队列调度算法

多级反馈队列调度算法在时间片轮询算法的基础上设置多个就绪队列，并为每个就绪队列都设置不同的优先权。队列的优先权越高，队列中的任务被分配的时间片就越大。默认第一个队列优先权最高，其他次之。

多级反馈队列调度算法的调度流程为：在系统收到新的任务后，首先将其放入第一个就绪队列的队尾，按先来先服务调度算法排队等待调度。若该进程在规定的CPU时间片内运行完成或者运行过程中出现错误，则退出进程并从系统中移除该任务；如果该进程在规定的CPU时间片内未运行完成，则将该进程转入第2队列的队尾调度执行；如果该进程在第2队列中运行一个CPU时间片后仍未完成，则将其放入第3队列，以此类推，在一个长作业从第1队列依次降到第n队列后，在第n队列中便以时间片轮转的方式运行。

多级反馈队列调度算法遵循以下原则。

◎ 仅在第一个队列为空时，调度器才调度第2队列中的任务。

◎ 仅在第1～(n-1)队列均为空时，调度器才会调度第n队列中的进程。

◎ 如果处理器正在为第n队列中的某个进程服务，此时有新进程进入优先权较高的队列（第1～(n-1)中的任何一个队列），则此时新进程将抢占正在运行的进程的处理器，即调度器停止正在运行的进程并将其放回第 n队列的末尾，把处理器分配给新来的高优先权进程。

## ThreadLocal

### ThreadLocal

ThreadLocal，即线程变量，是一个以ThreadLocal对象为键、任意对象为值的存储结构。这个结构被附带在线程上，也就是说一个线程可以根据一个ThreadLocal对象查询到绑定在这个线程上的一个值。

可以通过set(T)方法来设置一个值，在当前线程下再通过get()方法获取到原先设置的值。

多线程访问同一个共享变量的时候容易出现并发问题，特别是多个线程对一个变量进行写入的时候，为了保证线程安全，一般使用者在访问共享变量的时候需要进行额外的同步措施才能保证线程安全性。ThreadLocal是除了加锁这种同步方式之外的一种保证一种规避多线程访问出现线程不安全的方法，当我们在创建一个变量后，如果每个线程对其进行访问的时候访问的都是线程自己的变量这样就不会存在线程不安全问题。

### ThreadLocal的实现原理

![img](https://img2018.cnblogs.com/blog/1368768/201906/1368768-20190614000329689-872917045.png)

下面是ThreadLocal的类图结构，从图中可知：Thread类中有两个变量threadLocals和inheritableThreadLocals，二者都是ThreadLocal内部类ThreadLocalMap类型的变量，我们通过查看内部内ThreadLocalMap可以发现实际上它类似于一个HashMap。

#### set方法源码

```java
public void set(T value) {
    //(1)获取当前线程（调用者线程）
    Thread t = Thread.currentThread();
    //(2)以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //(3)如果map不为null，就直接添加本地变量，key为当前线程，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //(4)如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
}
```

#### get方法源码

　在get方法的实现中，首先获取当前调用者线程，如果当前线程的threadLocals不为null，就直接返回当前线程绑定的本地变量值，否则执行setInitialValue方法初始化threadLocals变量。在setInitialValue方法中，类似于set方法的实现，都是判断当前线程的threadLocals变量是否为null，是则添加本地变量（这个时候由于是初始化，所以添加的值为null），否则创建threadLocals变量，同样添加的值为null。

```java
public T get() {
    //(1)获取当前线程
    Thread t = Thread.currentThread();
    //(2)获取当前线程的threadLocals变量
    ThreadLocalMap map = getMap(t);
    //(3)如果threadLocals变量不为null，就可以在map中查找到本地变量的值
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //(4)执行到此处，threadLocals为null，调用该更改初始化当前线程的threadLocals变量
    return setInitialValue();
}

private T setInitialValue() {
    //protected T initialValue() {return null;}
    T value = initialValue();
    //获取当前线程
    Thread t = Thread.currentThread();
    //以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //如果map不为null，就直接添加本地变量，key为当前线程，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
    return value;
}
```

#### remove方法的实现

remove方法判断该当前线程对应的threadLocals变量是否为null，不为null就直接删除当前线程中指定的threadLocals变量。

```java
public void remove() {
    //获取当前线程绑定的threadLocals
     ThreadLocalMap m = getMap(Thread.currentThread());
     //如果map不为null，就移除当前线程中指定ThreadLocal实例的本地变量
     if (m != null)
         m.remove(this);
 }
```

### ThreadLocal特性

ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是

- Synchronized是通过线程等待，牺牲时间来解决访问冲突
- ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。

正因为ThreadLocal的线程隔离特性，使他的应用场景相对来说更为特殊一些。在android中Looper、ActivityThread以及AMS中都用到了ThreadLocal。当某些数据是以线程为作用域并且不同线程具有不同的数据副本的时候，就可以考虑采用ThreadLocal。

### ThreadLocal内存泄漏

![在这里插入图片描述](https://img-blog.csdn.net/2018092021395091?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6ZzEyMjkwNTk3MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如上图所示，我们在作为key的ThreadLocal对象没有外部强引用，下一次gc必将产生key值为null的数据，若线程没有及时结束必然出现，一条强引用链
Threadref–>Thread–>ThreadLocalMap–>Entry，所以这将导致内存泄漏。

下面我们模拟复现ThreadLocal导致内存泄漏：
为了效果更佳明显我们将我们的treadlocals的存储值value设置为1万字符串的列表：

```java
class ThreadLocalMemory {
    // Thread local variable containing each thread's ID
    public ThreadLocal<List<Object>> threadId = new ThreadLocal<List<Object>>() {
        @Override
        protected List<Object> initialValue() {
            List<Object> list = new ArrayList<Object>();
            for (int i = 0; i < 10000; i++) {
                list.add(String.valueOf(i));
            }
            return list;
        }
    };
    // Returns the current thread's unique ID, assigning it if necessary
    public List<Object> get() {
        return threadId.get();
    }
    // remove currentid
    public void remove() {
        threadId.remove();
    }
}
```

测试代码如下：

```java
public static void main(String[] args)
            throws InterruptedException {

        //  为了复现key被回收的场景，我们使用临时变量
        ThreadLocalMemory memeory = new ThreadLocalMemory();

        // 调用
        incrementSameThreadId(memeory);

        System.out.println("GC前：key:" + memeory.threadId);
        System.out.println("GC前：value-size:" + refelectThreadLocals(Thread.currentThread()));

        // 设置为null，调用gc并不一定触发垃圾回收，但是可以通过java提供的一些工具进行手工触发gc回收。
        memeory.threadId = null;
        System.gc();

        System.out.println("GC后：key:" + memeory.threadId);
        System.out.println("GC后：value-size:" + refelectThreadLocals(Thread.currentThread()));

        // 模拟线程一直运行
        while (true) {
        }
    }
```

使用ThreadLocal时会发生内存泄漏的前提条件：
①ThreadLocal引用被设置为null，且后面没有set，get,remove操作。
②线程一直运行，不停止。（线程池）
③触发了垃圾回收。（Minor GC或Full GC）

 我们看到ThreadLocal出现内存泄漏条件还是很苛刻的，所以我们只要破坏其中一个条件就可以避免内存泄漏，单但为了更好的避免这种情况的发生我们使用ThreadLocal时遵守以下两个小原则:

 ①ThreadLocal申明为private static final。
         Private与final 尽可能不让他人修改变更引用，
         Static 表示为类属性，只有在程序结束才会被回收。

 ②ThreadLocal使用后务必调用remove方法。
        最简单有效的方法是使用后将其移除。

## Fork/Join

### Fork/Join

ForkJoin是由JDK1.7后提供多线并发处理框架。ForkJoin的框架的基本思想是分而治之。什么是分而治之？分而治之就是将一个复杂的计算，按照设定的阈值进行分解成多个计算，然后将各个计算结果进行汇总。相应的ForkJoin将复杂的计算当做一个任务。而分解的多个计算则是当做一个子任务。

![img](https://upload-images.jianshu.io/upload_images/3047136-2044de505345b0a1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### Fork/Join框架基本使用

这里是一个简单的Fork/Join框架使用示例，在这个示例中我们计算了1-1001累加后的值：

```java

/**
 * 这是一个简单的Join/Fork计算过程，将1—1001数字相加
 */
public class TestForkJoinPool {
 
    private static final Integer MAX = 200;
 
    static class MyForkJoinTask extends RecursiveTask<Integer> {
        // 子任务开始计算的值
        private Integer startValue;
 
        // 子任务结束计算的值
        private Integer endValue;
 
        public MyForkJoinTask(Integer startValue , Integer endValue) {
            this.startValue = startValue;
            this.endValue = endValue;
        }
 
        @Override
        protected Integer compute() {
            // 如果条件成立，说明这个任务所需要计算的数值分为足够小了
            // 可以正式进行累加计算了
            if(endValue - startValue < MAX) {
                System.out.println("开始计算的部分：startValue = " + startValue + ";endValue = " + endValue);
                Integer totalValue = 0;
                for(int index = this.startValue ; index <= this.endValue  ; index++) {
                    totalValue += index;
                }
                return totalValue;
            }
            // 否则再进行任务拆分，拆分成两个任务
            else {
                MyForkJoinTask subTask1 = new MyForkJoinTask(startValue, (startValue + endValue) / 2);
                subTask1.fork();
                MyForkJoinTask subTask2 = new MyForkJoinTask((startValue + endValue) / 2 + 1 , endValue);
                subTask2.fork();
                return subTask1.join() + subTask2.join();
            }
        }
    }
 
    public static void main(String[] args) {
        // 这是Fork/Join框架的线程池
        ForkJoinPool pool = new ForkJoinPool();
        ForkJoinTask<Integer> taskFuture =  pool.submit(new MyForkJoinTask(1,1001));
        try {
            Integer result = taskFuture.get();
            System.out.println("result = " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace(System.out);
        }
    }
}
```

```

开始计算的部分：startValue = 1;endValue = 126
开始计算的部分：startValue = 127;endValue = 251
开始计算的部分：startValue = 252;endValue = 376
开始计算的部分：startValue = 377;endValue = 501
开始计算的部分：startValue = 502;endValue = 626
开始计算的部分：startValue = 627;endValue = 751
开始计算的部分：startValue = 752;endValue = 876
开始计算的部分：startValue = 877;endValue = 1001
result = 501501
```

### 工作顺序图

![这里写图片描述](https://img-blog.csdn.net/20170511170511140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlud2Vuamll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图中最顶层的任务使用submit方式被提交到Fork/Join框架中，后者将前者放入到某个线程中运行，工作任务中的compute方法的代码开始对这个任务T1进行分析。如果当前任务需要累加的数字范围过大（代码中设定的是大于200），则将这个计算任务拆分成两个子任务（T1.1和T1.2），每个子任务各自负责计算一半的数据累加，请参见代码中的fork方法。如果当前子任务中需要累加的数字范围足够小（小于等于200），就进行累加然后返回到上层任务中。

### ForkJoinPool构造函数

ForkJoinPool有四个构造函数，其中参数最全的那个构造函数如下所示：

```java

public ForkJoinPool(int parallelism,
                        ForkJoinWorkerThreadFactory factory,
                        UncaughtExceptionHandler handler,
                        boolean asyncMode)
```

- parallelism：可并行级别，Fork/Join框架将依据这个并行级别的设定，决定框架内并行执行的线程数量。并行的每一个任务都会有一个线程进行处理，但是千万不要将这个属性理解成Fork/Join框架中最多存在的线程数量，也不要将这个属性和ThreadPoolExecutor线程池中的corePoolSize、maximumPoolSize属性进行比较，因为ForkJoinPool的组织结构和工作方式与后者完全不一样。而后续的讨论中，读者还可以发现Fork/Join框架中可存在的线程数量和这个参数值的关系并不是绝对的关联（有依据但并不全由它决定）。
- factory：当Fork/Join框架创建一个新的线程时，同样会用到线程创建工厂。只不过这个线程工厂不再需要实现ThreadFactory接口，而是需要实现ForkJoinWorkerThreadFactory接口。后者是一个函数式接口，只需要实现一个名叫newThread的方法。在Fork/Join框架中有一个默认的ForkJoinWorkerThreadFactory接口实现：DefaultForkJoinWorkerThreadFactory。
- handler：异常捕获处理器。当执行的任务中出现异常，并从任务中被抛出时，就会被handler捕获。
- asyncMode：这个参数也非常重要，从字面意思来看是指的异步模式，它并不是说Fork/Join框架是采用同步模式还是采用异步模式工作。Fork/Join框架中为每一个独立工作的线程准备了对应的待执行任务队列，这个任务队列是使用数组进行组合的双向队列。即是说存在于队列中的待执行任务，即可以使用先进先出的工作模式，也可以使用后进先出的工作模式。

![这里写图片描述](https://img-blog.csdn.net/20170512085043577?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlud2Vuamll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 当asyncMode设置为ture的时候，队列采用先进先出方式工作；反之则是采用后进先出的方式工作，该值默认为false

### fork方法和join方法

Fork/Join框架中提供的fork方法和join方法，可以说是该框架中提供的最重要的两个方法，它们和parallelism“可并行任务数量”配合工作，可以导致拆分的子任务T1.1、T1.2甚至TX在Fork/Join框架中不同的运行效果。例如TX子任务或等待其它已存在的线程运行关联的子任务，或在运行TX的线程中“递归”执行其它任务，又或者启动一个新的线程运行子任务……

fork方法用于将新创建的子任务放入当前线程的work queue队列中，Fork/Join框架将根据当前正在并发执行ForkJoinTask任务的ForkJoinWorkerThread线程状态，决定是让这个任务在队列中等待，还是创建一个新的ForkJoinWorkerThread线程运行它，又或者是唤起其它正在等待任务的ForkJoinWorkerThread线程运行它。

这里面有几个元素概念需要注意，ForkJoinTask任务是一种能在Fork/Join框架中运行的特定任务，也只有这种类型的任务可以在Fork/Join框架中被拆分运行和合并运行。ForkJoinWorkerThread线程是一种在Fork/Join框架中运行的特性线程，它除了具有普通线程的特性外，最主要的特点是**每一个ForkJoinWorkerThread线程都具有一个独立的任务等待队列（work queue）**，这个任务队列用于存储在本线程中被拆分的若干子任务。

![这里写图片描述](https://img-blog.csdn.net/20170514084721521?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlud2Vuamll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## Java并发集合

### ConcurrentHashMap

#### ConcurrentHashMap

ConcurrentHashMap是Java中的一个**线程安全且高效的HashMap实现**。平时涉及高并发如果要用map结构，那第一时间想到的就是它。

与HashMap不同，ConcurrentHashMap采用分段锁的思想实现并发操作，因此是线程安全的。ConcurrentHashMap由多个Segment组成（Segment的数量也是锁的并发度），每个Segment均继承自ReentrantLock并单独加锁，所以每次进行加锁操作时锁住的都是一个Segment，这样只要保证每个Segment都是线程安全的，也就实现了全局的线程安全。ConcurrentHashMap的数据结构如图2-4所示。

![image-20200417045606984](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417045606984.png)

在ConcurrentHashMap中有个concurrencyLevel参数表示并行级别，默认是16，也就是说ConcurrentHashMap默认由16个Segments组成，在这种情况下最多同时支持16个线程并发执行写操作，只要它们的操作分布在不同的Segment上即可。并行级别concurrencyLevel可以在初始化时设置，一旦初始化就不可更改。ConcurrentHashMap的每个Segment内部的数据结构都和HashMap相同。Java 8在ConcurrentHashMap中引入了红黑树，具体的数据结构如图2-5所示。

![image-20200417045625202](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200417045625202.png)

#### 原理解析

利用 ==CAS + synchronized== 来保证并发更新的安全
底层使用==数组+链表+红黑树==来实现

![img](https://upload-images.jianshu.io/upload_images/13150128-df642c9afb040961.png?imageMogr2/auto-orient/strip|imageView2/2/w/781/format/webp)

#### 成员变量

table：默认为null，初始化发生在第一次插入操作，默认大小为16的数组，用来存储Node节点数据，扩容时大小总是2的幂次方。

nextTable：默认为null，扩容时新生成的数组，其大小为原数组的两倍。

sizeCtl ：默认为0，用来控制table的初始化和扩容操作，具体应用在后续会体现出来。
-1 代表table正在初始化
-N 表示有N-1个线程正在进行扩容操作

其余情况：
1、如果table未初始化，表示table需要初始化的大小。
2、如果table初始化完成，表示table的容量，默认是table大小的0.75倍，居然用这个公式算0.75（n - (n >>> 2)）。

Node：保存key，value及key的hash值的数据结构。
其中value和next都用volatile修饰，保证并发的可见性。

TreeNode
树节点类，另外一个核心的数据结构。当链表长度过长的时候，会转换为TreeNode。但是与HashMap不相同的是，它并不是直接转换为红黑树，而是把这些结点包装成TreeNode放在TreeBin对象中，由TreeBin完成对红黑树的包装。而且TreeNode在ConcurrentHashMap集成自Node类，而并非HashMap中的集成自LinkedHashMap.Entry<K,V>类，也就是说TreeNode带有next指针，这样做的目的是方便基于TreeBin的访问。

TreeBin
这个类并不负责包装用户的key、value信息，而是包装的很多TreeNode节点。它代替了TreeNode的根节点，也就是说在实际的ConcurrentHashMap“数组”中，存放的是TreeBin对象，而不是TreeNode对象，这是与HashMap的区别。另外这个类还带有了读写锁。

ForwardingNode：一个特殊的Node节点，hash值为-1，其中存储nextTable的引用。
只有table发生扩容的时候，ForwardingNode才会发挥作用，作为一个占位符放在table中表示当前节点为null或则已经被移动。

#### 实例初始化

实例化ConcurrentHashMap时倘若声明了table的容量，在初始化时会根据参数调整table大小，==确保table的大小总是2的幂次方==。默认的table大小为16.

table的初始化操作回延缓到第一put操作再进行，并且初始化只会执行一次。

```java
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
//如果一个线程发现sizeCtl<0，意味着另外的线程执行CAS操作成功，当前线程只需要让出cpu时间片
        if ((sc = sizeCtl) < 0) 
            Thread.yield(); // lost initialization race; just spin
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            try {
                if ((tab = table) == null || tab.length == 0) {
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    sc = n - (n >>> 2);  //0.75*capacity
                }
            } finally {
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```

#### put过程描述

假设table已经初始化完成，put操作采用==CAS+synchronized==实现并发插入或更新操作：
- 当前bucket为空时，使用CAS操作，将Node放入对应的bucket中。
- 出现hash冲突，则采用synchronized关键字。倘若当前hash对应的节点是链表的头节点，遍历链表，若找到对应的node节点，则修改node节点的val，否则在链表末尾添加node节点；倘若当前节点是红黑树的根节点，在树结构上遍历元素，更新或增加节点。
- 倘若当前map正在扩容f.hash == MOVED， 则跟其他线程一起进行扩容

#### table 扩容

什么时候会触发扩容？
- 如果新增节点之后，所在的链表的元素个数大于等于8，则会调用treeifyBin把链表转换为红黑树。在转换结构时，若tab的长度小于MIN_TREEIFY_CAPACITY，默认值为64，则会将数组长度扩大到原来的两倍，并触发transfer，重新调整节点位置。（只有当tab.length >= 64, ConcurrentHashMap才会使用红黑树。）
- 新增节点后，addCount统计tab中的节点个数大于阈值（sizeCtl），会触发transfer，重新调整节点位置。

#### transfer

当table的元素数量达到容量阈值sizeCtl，需要对table进行扩容：
\- 构建一个nextTable，大小为table两倍
\- 把table的数据复制到nextTable中。
在扩容过程中，依然支持并发更新操作；也支持并发插入。

![这里写图片描述](https://img-blog.csdn.net/20180327171001300?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJfYXQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如何在扩容时，并发地复制与插入？
1. 遍历整个table，当前节点为空，则采用CAS的方式在当前位置放入fwd
2. 当前节点已经为fwd(with hash field “MOVED”)，则已经有有线程处理完了了，直接跳过 ，这里是控制并发扩容的核心
3. 当前节点为链表节点或红黑树，重新计算链表节点的hash值，移动到nextTable相应的位置（构建了一个反序链表和顺序链表，分别放置在i和i+n的位置上）。移动完成后，用Unsafe.putObjectVolatile在tab的原位置赋为为fwd, 表示当前节点已经完成扩容。

#### get操作

读取操作，不需要同步控制，比较简单
\1. 空tab，直接返回null
\2. 计算hash值，找到相应的bucket位置，为node节点直接返回，否则返回null

#### 统计size

ConcurrentHashMap的元素个数等于baseCounter和数组里每个CounterCell的值之和，这样做的原因是，当多个线程同时执行CAS修改baseCount值，失败的线程会将值放到CounterCell中。所以统计元素个数时，要把baseCount和counterCells数组都考虑。

#### 删除元素

清空tab的过程：
遍历tab中每一个bucket，
\1. 当前bucket正在扩容，先协助扩容
\2. 给当前bucket上锁，删除元素
\3. 更新map的size

#### ConcurrentHashMap 在1.7与1.8中的不同

![这里写图片描述](https://img-blog.csdn.net/20180327171253589?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJfYXQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![这里写图片描述](https://img-blog.csdn.net/20180327171318297?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJfYXQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![image-20200318125924364](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200318125924364.png)

### ConcurrentLinkedQueue

#### ConcurrentLinkedQueue

一个基于链接节点的无界线程安全队列。此队列按照 FIFO（先进先出）原则对元素进行排序。队列的头部 是队列中时间最长的元素。队列的尾部 是队列中时间最短的元素。
新的元素插入到队列的尾部，队列获取操作从队列头部获得元素。当多个线程共享访问一个公共 collection 时，ConcurrentLinkedQueue 是一个恰当的选择。此队列不允许使用 null 元素。

![img](https://img-blog.csdn.net/20180625103613871?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 构造方法

ConcurrentLinkedQueue类有下面两个构造方法：

```java
// 默认构造方法，head节点存储的元素为空，tail节点等于head节点
public ConcurrentLinkedQueue() {
    head = tail = new Node<E>(null);
}
 
// 根据其他集合来创建队列
public ConcurrentLinkedQueue(Collection<? extends E> c) {
    Node<E> h = null, t = null;
    // 遍历节点
    for (E e : c) {
        // 若节点为null，则直接抛出NullPointerException异常
        checkNotNull(e);
        Node<E> newNode = new Node<E>(e);
        if (h == null)
            h = t = newNode;
        else {
            t.lazySetNext(newNode);
            t = newNode;
        }
    }
    if (h == null)
        h = t = new Node<E>(null);
    head = h;
    tail = t;
}
```

#### 入队操作

![img](https://img-blog.csdn.net/20180625144049602?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

上图所示的元素添加过程如下：

添加元素1：队列更新head节点的next节点为元素1节点。又因为tail节点默认情况下等于head节点，所以它们的next节点都指向元素1节点。
添加元素2：队列首先设置元素1节点的next节点为元素2节点，然后更新tail节点指向元素2节点。
添加元素3：设置tail节点的next节点为元素3节点。
添加元素4：设置元素3的next节点为元素4节点，然后将tail节点指向元素4节点。

入队操作主要做两件事情，第一是将入队节点设置成当前队列尾节点的下一个节点。第二是更新tail节点，如果tail节点的next节点不为空，则将入队节点设置成tail节点，如果tail节点的next节点为空，则将入队节点设置成tail的next节点，所以tail节点不总是尾节点，理解这一点很重要。

ConcurrentLinkedQueue的add(E e)入队方法：

```java
public boolean add(E e) {
    return offer(e);
}
 
public boolean offer(E e) {
    // 如果e为null，则直接抛出NullPointerException异常
    checkNotNull(e);
    // 创建入队节点
    final Node<E> newNode = new Node<E>(e);
 
    // 循环CAS直到入队成功
    // 1、根据tail节点定位出尾节点（last node）；2、将新节点置为尾节点的下一个节点；3、casTail更新尾节点
    for (Node<E> t = tail, p = t;;) {
        // p用来表示队列的尾节点，初始情况下等于tail节点
        // q是p的next节点
        Node<E> q = p.next;
        // 判断p是不是尾节点，tail节点不一定是尾节点，判断是不是尾节点的依据是该节点的next是不是null
        // 如果p是尾节点
        if (q == null) {
            // p is last node
            // 设置p节点的下一个节点为新节点，设置成功则casNext返回true；否则返回false，说明有其他线程更新过尾节点
            if (p.casNext(null, newNode)) {
                // Successful CAS is the linearization point
                // for e to become an element of this queue,
                // and for newNode to become "live".
                // 如果p != t，则将入队节点设置成tail节点，更新失败了也没关系，因为失败了表示有其他线程成功更新了tail节点
                if (p != t) // hop two nodes at a time
                    casTail(t, newNode);  // Failure is OK.
                return true;
            }
            // Lost CAS race to another thread; re-read next
        }
        // 多线程操作时候，由于poll时候会把旧的head变为自引用，然后将head的next设置为新的head
        // 所以这里需要重新找新的head，因为新的head后面的节点才是激活的节点
        else if (p == q)
            // We have fallen off list.  If tail is unchanged, it
            // will also be off-list, in which case we need to
            // jump to head, from which all live nodes are always
            // reachable.  Else the new tail is a better bet.
            p = (t != (t = tail)) ? t : head;
        // 寻找尾节点
        else
            // Check for tail updates after two hops.
            p = (p != t && t != (t = tail)) ? t : q;
    }
}
```

让tail节点永远作为队列的尾节点，这样实现代码量非常少，而且逻辑非常清楚和易懂。但是这么做有个缺点就是每次都需要使用循环CAS更新tail节点。如果能减少CAS更新tail节点的次数，就能提高入队的效率。

在JDK 1.7的实现中，doug lea使用hops变量来控制并减少tail节点的更新频率，并不是每次节点入队后都将 tail节点更新成尾节点，而是当tail节点和尾节点的距离大于等于常量HOPS的值（默认等于1）时才更新tail节点，tail和尾节点的距离越长使用CAS更新tail节点的次数就会越少，但是距离越长带来的负面效果就是每次入队时定位尾节点的时间就越长，因为循环体需要多循环一次来定位出尾节点，但是这样仍然能提高入队的效率，因为从本质上来看它通过增加对volatile变量的读操作来减少了对volatile变量的写操作，而对volatile变量的写操作开销要远远大于读操作，所以入队效率会有所提升。

在JDK 1.8的实现中，tail的更新时机是通过p和t是否相等来判断的，其实现结果和JDK 1.7相同，即当tail节点和尾节点的距离大于等于1时，更新tail。
ConcurrentLinkedQueue的入队操作整体逻辑如下图所示：

![img](https://img-blog.csdn.net/20180625162704934?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 出队操作

出队列的就是从队列里返回一个节点元素，并清空该节点对元素的引用。让我们通过每个节点出队的快照来观察下head节点的变化：

![img](https://img-blog.csdn.net/20180625144156632?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从上图可知，并不是每次出队时都更新head节点，当head节点里有元素时，直接弹出head节点里的元素，而不会更新head节点。只有当head节点里没有元素时，出队操作才会更新head节点。采用这种方式也是为了减少使用CAS更新head节点的消耗，从而提高出队效率。让我们再通过源码来深入分析下出队过程。

```java
public E poll() {
    restartFromHead:
    for (;;) {
        // p节点表示首节点，即需要出队的节点
        for (Node<E> h = head, p = h, q;;) {
            E item = p.item;
 
            // 如果p节点的元素不为null，则通过CAS来设置p节点引用的元素为null，如果成功则返回p节点的元素
            if (item != null && p.casItem(item, null)) {
                // Successful CAS is the linearization point
                // for item to be removed from this queue.
                // 如果p != h，则更新head
                if (p != h) // hop two nodes at a time
                    updateHead(h, ((q = p.next) != null) ? q : p);
                return item;
            }
            // 如果头节点的元素为空或头节点发生了变化，这说明头节点已经被另外一个线程修改了。
            // 那么获取p节点的下一个节点，如果p节点的下一节点为null，则表明队列已经空了
            else if ((q = p.next) == null) {
                // 更新头结点
                updateHead(h, p);
                return null;
            }
            // p == q，则使用新的head重新开始
            else if (p == q)
                continue restartFromHead;
            // 如果下一个元素不为空，则将头节点的下一个节点设置成头节点
            else
                p = q;
        }
    }
}
```

该方法的主要逻辑就是首先获取头节点的元素，然后判断头节点元素是否为空，如果为空，表示另外一个线程已经进行了一次出队操作将该节点的元素取走，如果不为空，则使用CAS的方式将头节点的引用设置成null，如果CAS成功，则直接返回头节点的元素，如果不成功，表示另外一个线程已经进行了一次出队操作更新了head节点，导致元素发生了变化，需要重新获取头节点。

在入队和出队操作中，都有p == q的情况，那这种情况是怎么出现的呢？我们来看这样一种操作：
![img](https://img-blog.csdn.net/20180625154414464?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjkzNTY0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在弹出一个节点之后，tail节点有一条指向自己的虚线，这是什么意思呢？我们来看poll()方法，在该方法中，移除元素之后，会调用updateHead方法：

```java
final void updateHead(Node<E> h, Node<E> p) {
    if (h != p && casHead(h, p))
        // 将旧的头结点h的next域指向为h
        h.lazySetNext(h);
}
```

我们可以看到，在更新完head之后，会将旧的头结点h的next域指向为h，上图中所示的虚线也就表示这个节点的自引用。

如果这时，再有一个线程来添加元素，通过tail获取的next节点则仍然是它本身，这就出现了p == q的情况，出现该种情况之后，则会触发执行head的更新，将p节点重新指向为head，所有“活着”的节点（指未删除节点），都能从head通过遍历可达，这样就能通过head成功获取到尾节点，然后添加元素了。

#### 非阻塞算法实现

ConcurrentLinkedQueue 的非阻塞算法实现可概括为下面 5 点：

使用 CAS 原子指令来处理对数据的并发访问，这是非阻塞算法得以实现的基础。

head/tail 并非总是指向队列的头 / 尾节点，也就是说允许队列处于不一致状态。 这个特性把入队 / 出队时，原本需要一起原子化执行的两个步骤分离开来，从而缩小了入队 / 出队时需要原子化更新值的范围到唯一变量。这是非阻塞算法得以实现的关键。

由于队列有时会处于不一致状态。为此，ConcurrentLinkedQueue 使用三个不变式来维护非阻塞算法的正确性。

以批处理方式来更新 head/tail，从整体上减少入队 / 出队操作的开销。

为了有利于垃圾收集，队列使用特有的 head 更新机制；为了确保从已删除节点向后遍历，可到达所有的非删除节点，队列使用了特有的向后推进策略。

### **ConcurrentSkipListMap**

#### **ConcurrentSkipListMap**

ConcurrentSkipListMap是线程安全的有序的哈希表，适用于高并发的场景。
ConcurrentSkipListMap和TreeMap，它们虽然都是有序的哈希表。但是，第一，它们的线程安全机制不同，TreeMap是非线程安全的，而ConcurrentSkipListMap是线程安全的。第二，ConcurrentSkipListMap是通过跳表实现的，而TreeMap是通过红黑树实现的。

在4线程1.6万数据的条件下，ConcurrentHashMap 存取速度是ConcurrentSkipListMap 的4倍左右。
但ConcurrentSkipListMap有几个ConcurrentHashMap 不能比拟的优点：
1、ConcurrentSkipListMap 的key是有序的。
2、ConcurrentSkipListMap 支持更高的并发。ConcurrentSkipListMap 的存取时间是log（N），和线程数几乎无关。也就是说在数据量一定的情况下，并发的线程越多，ConcurrentSkipListMap越能体现出他的优势。
在非多线程的情况下，应当尽量使用TreeMap。此外对于并发性相对较低的并行程序可以使用Collections.synchronizedSortedMap将TreeMap进行包装，也可以提供较好的效率。对于高并发程序，应当使用ConcurrentSkipListMap，能够提供更高的并发度。

#### **ConcurrentSkipListMap数据结构**

ConcurrentSkipListMap的数据结构，如下图所示：

![img](https://images0.cnblogs.com/blog/497634/201312/30221944-21a18064c0114e65bffa68c5dadd4f0b.jpg)

#### **成员属性**

```java
static final class Node<K,V> {
    final K key;
    volatile Object value;
    volatile Node<K,V> next;

    Node(K key, Object value, Node<K,V> next) {
        this.key = key;
        this.value = value;
        this.next = next;
    }
    //省略其它的一些基于当前结点的 CAS 方法
}
```

### ConcurrentSkipListSet

#### ConcurrentSkipListSet

ConcurrentSkipListSet是线程安全的有序的集合，适用于高并发的场景。
ConcurrentSkipListSet和[TreeSet](http://www.cnblogs.com/skywang12345/p/3311268.html)，它们虽然都是有序的集合。但是，第一，它们的线程安全机制不同，TreeSet是非线程安全的，而ConcurrentSkipListSet是线程安全的。第二，ConcurrentSkipListSet是通过[ConcurrentSkipListMap](http://www.cnblogs.com/skywang12345/p/3498556.html)实现的，而TreeSet是通过TreeMap实现的。

#### **ConcurrentSkipListSet原理和数据结构**

![img](https://images0.cnblogs.com/blog/497634/201312/30231618-c5642dcd06d344269bfda4f05f431951.jpg)

**说明**：
(01) ConcurrentSkipListSet继承于AbstractSet。因此，它本质上是一个集合。
(02) ConcurrentSkipListSet实现了NavigableSet接口。因此，ConcurrentSkipListSet是一个有序的集合。
(03) ConcurrentSkipListSet是通过ConcurrentSkipListMap实现的。它包含一个ConcurrentNavigableMap对象m，而m对象实际上是ConcurrentNavigableMap的实现类ConcurrentSkipListMap的实例。ConcurrentSkipListMap中的元素是key-value键值对；而ConcurrentSkipListSet是集合，它只用到了ConcurrentSkipListMap中的key！

#### ConcurrentSkipListSet函数列表

```java
// 构造一个新的空 set，该 set 按照元素的自然顺序对其进行排序。
ConcurrentSkipListSet()
// 构造一个包含指定 collection 中元素的新 set，这个新 set 按照元素的自然顺序对其进行排序。
ConcurrentSkipListSet(Collection<? extends E> c)
// 构造一个新的空 set，该 set 按照指定的比较器对其元素进行排序。
ConcurrentSkipListSet(Comparator<? super E> comparator)
// 构造一个新 set，该 set 所包含的元素与指定的有序 set 包含的元素相同，使用的顺序也相同。
ConcurrentSkipListSet(SortedSet<E> s)

// 如果此 set 中不包含指定元素，则添加指定元素。
boolean add(E e)
// 返回此 set 中大于等于给定元素的最小元素；如果不存在这样的元素，则返回 null。
E ceiling(E e)
// 从此 set 中移除所有元素。
void clear()
// 返回此 ConcurrentSkipListSet 实例的浅表副本。
ConcurrentSkipListSet<E> clone()
// 返回对此 set 中的元素进行排序的比较器；如果此 set 使用其元素的自然顺序，则返回 null。
Comparator<? super E> comparator()
// 如果此 set 包含指定的元素，则返回 true。
boolean contains(Object o)
// 返回在此 set 的元素上以降序进行迭代的迭代器。
Iterator<E> descendingIterator()
// 返回此 set 中所包含元素的逆序视图。
NavigableSet<E> descendingSet()
// 比较指定对象与此 set 的相等性。
boolean equals(Object o)
// 返回此 set 中当前第一个（最低）元素。
E first()
// 返回此 set 中小于等于给定元素的最大元素；如果不存在这样的元素，则返回 null。
E floor(E e)
// 返回此 set 的部分视图，其元素严格小于 toElement。
NavigableSet<E> headSet(E toElement)
// 返回此 set 的部分视图，其元素小于（或等于，如果 inclusive 为 true）toElement。
NavigableSet<E> headSet(E toElement, boolean inclusive)
// 返回此 set 中严格大于给定元素的最小元素；如果不存在这样的元素，则返回 null。
E higher(E e)
// 如果此 set 不包含任何元素，则返回 true。
boolean isEmpty()
// 返回在此 set 的元素上以升序进行迭代的迭代器。
Iterator<E> iterator()
// 返回此 set 中当前最后一个（最高）元素。
E last()
// 返回此 set 中严格小于给定元素的最大元素；如果不存在这样的元素，则返回 null。
E lower(E e)
// 获取并移除第一个（最低）元素；如果此 set 为空，则返回 null。
E pollFirst()
// 获取并移除最后一个（最高）元素；如果此 set 为空，则返回 null。
E pollLast()
// 如果此 set 中存在指定的元素，则将其移除。
boolean remove(Object o)
// 从此 set 中移除包含在指定 collection 中的所有元素。
boolean removeAll(Collection<?> c)
// 返回此 set 中的元素数目。
int size()
// 返回此 set 的部分视图，其元素范围从 fromElement 到 toElement。
NavigableSet<E> subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive)
// 返回此 set 的部分视图，其元素从 fromElement（包括）到 toElement（不包括）。
NavigableSet<E> subSet(E fromElement, E toElement)
// 返回此 set 的部分视图，其元素大于等于 fromElement。
NavigableSet<E> tailSet(E fromElement)
// 返回此 set 的部分视图，其元素大于（或等于，如果 inclusive 为 true）fromElement。
NavigableSet<E> tailSet(E fromElement, boolean inclusive)
```

### CopyOnWriteArrayList

#### CopyOnWrite

写入时复制（CopyOnWrite，简称COW）思想是计算机程序设计领域中的一种优化策略。其核心思想是，如果有多个调用者（Callers）同时要求相同的资源（如内存或者是磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者视图修改资源内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是透明的（transparently）。此做法主要的优点是如果调用者没有修改资源，就不会有副本（private copy）被创建，因此多个调用者只是读取操作时可以共享同一份资源。

#### CopyOnWriteArrayList

```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    
}
```

在很多应用场景中，读操作可能会远远大于写操作。由于读操作根本不会修改原有的数据，因此如果每次读取都进行加锁操作，其实是一种资源浪费。我们应该允许多个线程同时访问 List 的内部数据，毕竟读操作是线程安全的。

这和 `ReentrantReadWriteLock` 读写锁的思想非常类似，也就是 **读读共享、写写互斥、读写互斥、写读互斥**。JDK中提供了 `CopyOnWriteArrayList` 类，相比于在读写锁的思想又更进一步。为了将读取的性能发挥到极致，`CopyOnWriteArrayList` 读取是完全不用加锁的，并且更厉害的是：写入也不会阻塞读取操作，只有写入和写入之间需要进行同步等待，读操作的性能得到大幅度提升。

#### CopyOnWrite的缺点

　　CopyOnWrite容器有很多优点，但是同时也存在两个问题，即内存占用问题和数据一致性问题。所以在开发的时候需要注意一下。

　　**内存占用问题**。因为CopyOnWrite的写时复制机制，所以在进行写操作的时候，内存里会同时驻扎两个对象的内存，旧的对象和新写入的对象（注意:在复制的时候只是复制容器里的引用，只是在写的时候会创建新对象添加到新容器里，而旧容器的对象还在使用，所以有两份对象内存）。如果这些对象占用的内存比较大，比如说200M左右，那么再写入100M数据进去，内存就会占用300M，那么这个时候很有可能造成频繁的Yong GC和Full GC。之前我们系统中使用了一个服务由于每晚使用CopyOnWrite机制更新大对象，造成了每晚15秒的Full GC，应用响应时间也随之变长。

　　针对内存占用问题，可以通过压缩容器中的元素的方法来减少大对象的内存消耗，比如，如果元素全是10进制的数字，可以考虑把它压缩成36进制或64进制。或者不使用CopyOnWrite容器，而使用其他的并发容器，如[ConcurrentHashMap](http://ifeve.com/concurrenthashmap/)。

　　**数据一致性问题**。CopyOnWrite容器只能保证数据的最终一致性，不能保证数据的实时一致性。所以如果你希望写入的的数据，马上能读到，请不要使用CopyOnWrite容器。

## atomic

![image-20200511044758675](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200511044758675.png)



### Atomic

atomic类是通过自旋CAS操作volatile变量实现的。

　　CAS是compare and swap的缩写，即比较后(比较内存中的旧值与预期值)交换(将旧值替换成预期值)。它是sun.misc包下Unsafe类提供的功能，需要底层硬件指令集的支撑。

　　使用volatile变量是为了多个线程间变量的值能及时同步。

### 原子更新基本类型

用于通过原子的方式更新基本类型，Atomic包提供了以下三个类：

- AtomicBoolean：原子更新布尔类型。
- AtomicInteger：原子更新整型。
- AtomicLong：原子更新长整型。

#### AtomicInteger

AtomicInteger的常用方法如下：

- AtomicInteger(int initialValue)：创建一个AtomicInteger实例，初始值由参数指定。不带参的构造方法初始值为0。
- int addAndGet(int delta) ：以原子方式将输入的数值与实例中的值（AtomicInteger里的value）相加，并返回结果，与getAndAdd(int delta)相区分，从字面意思即可区分，前者返回相加后结果，后者先返回再相加。
- boolean compareAndSet(int expect, int update) ：如果当前值等于预期值，则以原子方式将该值设置为输入的值。
- int getAndIncrement()：以原子方式将当前值加1，注意：这里返回的是自增前的值。
- void lazySet(int newValue)：最终会设置成newValue，使用lazySet设置值后，可能导致其他线程在之后的一小段时间内还是可以读到旧的值。
- int getAndSet(int newValue)：以原子方式设置为newValue的值，并返回旧值。

#### AtomicLong

有些机器是32位的，所以会把64位long类型volatile拆分成2个32位进行计算，但有些不是的。所以在实现AtomicLong的时候，如果是32位，那就需要加上锁来实现CAS操作。JVM特别的还加了一个判断，来区分是32位：

```java
 /**
     * Records whether the underlying JVM supports lockless
     * compareAndSwap for longs. While the Unsafe.compareAndSwapLong
     * method works in either case, some constructions should be
     * handled at Java level to avoid locking user-visible locks.
     */
    static final boolean VM_SUPPORTS_LONG_CAS = VMSupportsCS8();

    /**
     * Returns whether underlying JVM supports lockless CompareAndSet
     * for longs. Called only once and cached in VM_SUPPORTS_LONG_CAS.
     */
    private static native boolean VMSupportsCS8();
```

#### AtomicBoolean

在AtomicBoolean中，作为volatile变量的，并不是boolean类型，而是一个int类型的0和1来分别表示false和true。

```java
   private volatile int value;

    /**
     * Creates a new {@code AtomicBoolean} with the given initial value.
     *
     * @param initialValue the initial value
     */
    public AtomicBoolean(boolean initialValue) {
        value = initialValue ? 1 : 0;
    }
```

### 原子更新数组

Atomic数组，顾名思义，就是能以原子的方式，操作数组中的元素。

JDK提供了三种类型的原子数组：`AtomicIntegerArray`、`AtomicLongArray`、`AtomicReferenceArray`。

### 原子更新引用

原子更新基本类型的AtomicInteger，只能更新一个变量，如果要原子更新多个变量，就需要使用这个原子更新引用类型提供的类。Atomic包提供了以下3个类。

AtomicReference：原子更新引用类型。

AtomicReferenceFieldUpdater：原子更新引用类型里的字段。

AtomicMarkableReference：原子更新带有标记位的引用类型。


### 原子更新字段

如果需原子地更新某个类里的某个字段时，就需要使用原子更新字段类，Atomic包提供了以下3个类进行原子字段更新。
AtomicIntegerFieldUpdater：原子更新整型的字段的更新器。
AtomicLongFieldUpdater：原子更新长整型字段的更新器。
AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于原子的更新数据和数据的版本号，可以解决使用CAS进行原子更新时可能出现的ABA问题。

## 阻塞队列

### 阻塞队列

![img](https://img2018.cnblogs.com/blog/1645058/201904/1645058-20190402165526994-1585585944.png)

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

阻塞队列提供了四种处理方法：

![img](https://img2018.cnblogs.com/blog/1645058/201904/1645058-20190402175753071-1811310426.png)

- 抛出异常：是指当阻塞队列满时候，再往队列里插入元素，会抛出IllegalStateException(“Queue full”)异常。当队列为空时，从队列里获取元素时会抛出NoSuchElementException异常 。
- 返回特殊值：插入方法会返回是否成功，成功则返回true。移除方法，则是从队列里拿出一个元素，如果没有则返回null
- 一直阻塞：当阻塞队列满时，如果生产者线程往队列里put元素，队列会一直阻塞生产者线程，直到拿到数据，或者响应中断退出。当队列空时，消费者线程试图从队列里take元素，队列也会阻塞消费者线程，直到队列可用。
- 超时退出：当阻塞队列满时，队列会阻塞生产者线程一段时间，如果超过一定的时间，生产者线程就会退出。

JDK7提供了7个阻塞队列。分别是

- ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
- LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
- PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。
- DelayQueue：一个使用优先级队列实现的无界阻塞队列。
- SynchronousQueue：一个不存储元素的阻塞队列。
- LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
- LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

###  **ArrayBlockingQueue**

ArrayBlockingQueue是一个用数组实现的**有界阻塞队列**。此队列按照先进先出（FIFO）的原则对元素进行排序。默认情况下不保证访问者公平的访问队列，所谓公平访问队列是指阻塞的所有生产者线程或消费者线程，当队列可用时，可以按照阻塞的先后顺序访问队列，即先阻塞的生产者线程，可以先往队列里插入元素，先阻塞的消费者线程，可以先从队列里获取元素。通常情况下为了保证公平性会降低吞吐量。

实现：ReentrantLock+Condition

### LinkedBlockingQueue

LinkedBlockingQueue是一个阻塞队列，内部由两个ReentrantLock来实现出入队列的线程安全，由各自的Condition对象的await和signal来实现等待和唤醒功能。它和ArrayBlockingQueue的不同点在于：

1. 队列大小有所不同，ArrayBlockingQueue是有界的初始化必须指定大小，而LinkedBlockingQueue可以是有界的也可以是无界的(Integer.MAX_VALUE)，对于后者而言，当添加速度大于移除速度时，在无界的情况下，可能会造成内存溢出等问题。
2. 数据存储容器不同，ArrayBlockingQueue采用的是数组作为数据存储容器，而LinkedBlockingQueue采用的则是以Node节点作为连接对象的链表。
3. 由于ArrayBlockingQueue采用的是数组的存储容器，因此在插入或删除元素时不会产生或销毁任何额外的对象实例，而LinkedBlockingQueue则会生成一个额外的Node对象。这可能在长时间内需要高效并发地处理大批量数据的时，对于GC可能存在较大影响。
4. 两者的实现队列添加或移除的锁不一样，ArrayBlockingQueue实现的队列中的锁是没有分离的，即添加操作和移除操作采用的同一个ReenterLock锁，而LinkedBlockingQueue实现的队列中的锁是分离的，其添加采用的是putLock，移除采用的则是takeLock，这样能大大提高队列的吞吐量，也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。

### PriorityBlockingQueue

PriorityBlockingQueue是一个支持优先级的无界阻塞队列。默认情况下元素采用自然顺序升序排列。也可以自定义类实现compareTo()方法来指定元素排序规则，或者初始化PriorityBlockingQueue时，指定构造参数Comparator来对元素进行排序。但需要注意的是不能保证同优先级元素的顺序。

#### 构造方法

PriorityBlockingQueue有四个构造方法：

```java
// 默认的构造方法，该方法会调用this(DEFAULT_INITIAL_CAPACITY, null)，即默认的容量是11
public PriorityBlockingQueue()
// 根据initialCapacity来设置队列的初始容量
public PriorityBlockingQueue(int initialCapacity)
// 根据initialCapacity来设置队列的初始容量，并根据comparator对象来对数据进行排序
public PriorityBlockingQueue(int initialCapacity, Comparator<? super E> comparator)
// 根据集合来创建队列
public PriorityBlockingQueue(Collection<? extends E> c)
```

### DelayQueue

 DelayedQueue是一个用来延时处理的队列，delayQueue其实就是在每次往优先级队列中添加元素,然后以元素的delay/过期值作为排序的因素,以此来达到先过期的元素会拍在队首,每次从队列里取出来都是最先要过期的元素

- 所谓延时处理就是说可以为队列中元素设定一个过期时间，
- 相关的操作受到这个设定时间的控制。

#### DelayQueue类图

![img](https://upload-images.jianshu.io/upload_images/6302559-d6cf399ea11eb20e.png?imageMogr2/auto-orient/strip|imageView2/2/w/778/format/webp)

#### DelayQueue类变量和构造函数

DelayQueue的类变量当中有两个核心变量值得考虑：

- DelayQueue的PriorityQueue表明DelayQueue**内部使用PriorityQueue的最小堆保证有序**
- E extends Delayed标明存入DelayQueue的变量必须实现Delayed接口，实现getDelay和compareTo接口。

### SynchronousQueue

ynchronousQueue 是一个不存储元素的阻塞队列。每一个 put 操作必须等待一个 take 操作，否则不能继续添加元素。

SynchronousQueue 可以看成是一个传球手，负责把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合于传递性场景,比如在一个线程中使用的数据，传递给另外一个线程使用，SynchronousQueue 的吞吐量高于 LinkedBlockingQueue 和ArrayBlockingQueue。

### LinkedTransferQueue

LinkedTransferQueue是一个由链表结构组成的无界阻塞TransferQueue队列。相对于其他阻塞队列，LinkedTransferQueue多了tryTransfer和transfer方法。

LinkedTransferQueue采用一种预占模式。意思就是消费者线程取元素时，如果队列不为空，则直接取走数据，若队列为空，那就生成一个节点（节点元素为null）入队，然后消费者线程被等待在这个节点上，后面生产者线程入队时发现有一个元素为null的节点，生产者线程就不入队了，直接就将元素填充到该节点，并唤醒该节点等待的线程，被唤醒的消费者线程取走元素，从调用的方法返回。我们称这种节点操作为“匹配”方式。

LinkedTransferQueue是ConcurrentLinkedQueue、SynchronousQueue（公平模式下转交元素）、LinkedBlockingQueue（阻塞Queue的基本方法）的超集。而且LinkedTransferQueue更好用，因为它不仅仅综合了这几个类的功能，同时也提供了更高效的实现。


#### LinkedTransferQueue（后称LTQ）算法实现

LTQ的算法实现可以总结为以下几点：

- **双重队列**：
   和典型的单向链表结构不同，LTQ 的 Node 存储了一个`isData`的 boolean 型字段，也就是说它的节点可以代表一个数据或者是一个请求，称为**双重队列（Dual Queue）**。上面说过，在消费者获取元素时，如果队列为空，当前消费者就会作为一个“元素为null”的节点被放入队列中等待，所以 LTQ中 的节点存储了生产者节点（item不为null）和消费者节点（item为null），这两种节点就是通过`isData`来区分的。
- **松弛度**：
   为了节省 CAS 操作的开销，LTQ 引入了“松弛度”的概念：在节点被匹配（被删除）之后，不会立即更新head/tail，而是当 head/tail 节点和最近一个未匹配的节点之间的距离超过一个“**松弛阀值**”之后才会更新（在 LTQ 中，这个值为 2）。这个“松弛阀值”一般为1-3，如果太大会降低缓存命中率，并且会增加遍历链的长度；太小会增加 CAS 的开销。
- **节点自链接**：
   已匹配节点的 next 引用会指向自身。
   如果GC延迟回收，已删除节点链会积累的很长，此时垃圾收集会耗费高昂的代价，并且所有刚匹配的节点也不会被回收。为了避免这种情况，我们在 CAS 向后推进 head 时，会把已匹配的 head 的"next"引用指向自身（即“**自链接节点**”），这样就限制了连接已删除节点的长度（我们也采取类似的方法，清除在其他节点字段中可能的垃圾保留值）。如果在遍历时遇到一个自链接节点，那就表明当前线程已经滞后于另外一个更新 head 的线程，此时就需要重新获取 head 来遍历。

#### 核心参数

```java
//队列头节点，第一次入列之前为空
transient volatile Node head;

//队列尾节点，第一次添加节点之前为空
private transient volatile Node tail;

//累计到一定次数再清除无效node
private transient volatile int sweepVotes;

//当一个节点是队列中的第一个waiter时，在多处理器上进行自旋的次数(随机穿插调用thread.yield)
private static final int FRONT_SPINS   = 1 << 7;

// 当前继节点正在处理，当前节点在阻塞之前的自旋次数，也为FRONT_SPINS
// 的位变化充当增量，也可在自旋时作为yield的平均频率
private static final int CHAINED_SPINS = FRONT_SPINS >>> 1;

//sweepVotes的阀值
static final int SWEEP_THRESHOLD = 32;
/*
 * Possible values for "how" argument in xfer method.
 * xfer方法类型
 */
private static final int NOW   = 0; // for untimed poll, tryTransfer
private static final int ASYNC = 1; // for offer, put, add
private static final int SYNC  = 2; // for transfer, take
private static final int TIMED = 3; // for timed poll, tryTransfer
```

### LinkedBlockingDeque

LinkedBlockingDeque是一个由链表结构组成的双向阻塞队列，即可以从队列的两端插入和移除元素。双向队列因为多了一个操作队列的入口，在多线程同时入队时，也就减少了一半的竞争。

相比于其他阻塞队列，LinkedBlockingDeque多了addFirst、addLast、peekFirst、peekLast等方法，以first结尾的方法，表示插入、获取获移除双端队列的第一个元素。以last结尾的方法，表示插入、获取获移除双端队列的最后一个元素。

LinkedBlockingDeque是可选容量的，在初始化时可以设置容量防止其过度膨胀，如果不设置，默认容量大小为Integer.MAX_VALUE

#### 构造方法

LinkedBlockingDeque类有三个构造方法：

```java
public LinkedBlockingDeque()
public LinkedBlockingDeque(int capacity)
public LinkedBlockingDeque(Collection<? extends E> c)
```

## 线程池

### 线程池

线程池，本质上是一种对象池，用于管理线程资源。
 在任务执行前，需要从线程池中拿出线程来执行。
 在任务执行完成之后，需要把线程放回线程池。
 通过线程的这种反复利用机制，可以有效地避免直接创建线程所带来的坏处。

我们先来看看线程池带来了哪些好处。

1. 降低资源的消耗。线程本身是一种资源，创建和销毁线程会有CPU开销；创建的线程也会占用一定的内存。
2. 提高任务执行的响应速度。任务执行时，可以不必等到线程创建完之后再执行。
3. 提高线程的可管理性。线程不能无限制地创建，需要进行统一的分配、调优和监控。

接下来，我们看看不使用线程池有哪些坏处。

1. 频繁的线程创建和销毁会占用更多的CPU和内存
2. 频繁的线程创建和销毁会对GC产生比较大的压力
3. 线程太多，线程切换带来的开销将不可忽视
4. 线程太少，多核CPU得不到充分利用，是一种浪费

因此，我们有必要对线程池进行比较完整地说明，以便能对线程池进行正确地治理。

### 线程池的执行流程

![img](https://img-blog.csdn.net/2018041900353665)

### T向线程池提交任务

可以使用两个方法向线程池提交任务，分别为execute()和submit()方法。

execute()方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功。

submit()方法用于提交需要返回值的任务。线程池会返回一个future类型的对象，通过这个future对象可以判断任务是否执行成功，并且可以通过future的get()方法来获取返回值，get()方法会阻塞当前线程直到任务完成，而使用get（long timeout，TimeUnit unit）方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。

### 关闭线程池

可以通过调用线程池的shutdown或shutdownNow方法来关闭线程池。它们的原理是遍历线程池中的工作线程，然后逐个调用线程的interrupt方法来中断线程，所以无法响应中断的任务可能永远无法终止。但是它们存在一定的区别，shutdownNow首先将线程池的状态设置成STOP，然后尝试停止所有的正在执行或暂停任务的线程，并返回等待执行任务的列表，而shutdown只是将线程池的状态设置成SHUTDOWN状态，然后中断所有没有正在执行任务的线程。

只要调用了这两个关闭方法中的任意一个，isShutdown方法就会返回true。当所有的任务都已关闭后，才表示线程池关闭成功，这时调用isTerminaed方法会返回true。至于应该调用哪一种方法来关闭线程池，应该由提交到线程池的任务特性决定，通常调用shutdown方法来关闭线程池，如果任务不一定要执行完，则可以调用shutdownNow方法。

### 线程池的监控

如果在系统中大量使用线程池，则有必要对线程池进行监控，方便在出现问题时，可以根据线程池的使用状况快速定位问题。可以通过线程池提供的参数进行监控，在监控线程池的时候可以使用以下属性。

❑ taskCount：线程池需要执行的任务数量。

❑ completedTaskCount：线程池在运行过程中已完成的任务数量，小于或等于taskCount。

❑ largestPoolSize：线程池里曾经创建过的最大线程数量。通过这个数据可以知道线程池是否曾经满过。如该数值等于线程池的最大大小，则表示线程池曾经满过。

❑ getPoolSize：线程池的线程数量。如果线程池不销毁的话，线程池里的线程不会自动销毁，所以这个大小只增不减。

❑ getActiveCount：获取活动的线程数。

## Executor

在Java中，使用线程来异步执行任务。Java线程的创建与销毁需要一定的开销，如果我们为每一个任务创建一个新线程来执行，这些线程的创建与销毁将消耗大量的计算资源。同时，为每一个任务创建一个新线程来执行，这种策略可能会使处于高负荷状态的应用最终崩溃。

Java的线程既是工作单元，也是执行机制。从JDK 5开始，把工作单元与执行机制分离开来。工作单元包括Runnable和Callable，而执行机制由Executor框架提供。

### Executor框架

![这里写图片描述](https://img-blog.csdn.net/20180403100455088?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJvZ3JhbW1lcl9hdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Executor框架主要由3大部分组成如下。

❑ 任务。包括被执行任务需要实现的接口：Runnable接口或Callable接口。

❑ 任务的执行。包括任务执行机制的核心接口Executor，以及继承自Executor的ExecutorService接口。Executor框架有两个关键类实现了ExecutorService接口（ThreadPoolExecutor和ScheduledThreadPoolExecutor）。

❑ 异步计算的结果。包括接口Future和实现Future接口的FutureTask类。

### Executor框架的成员

**（1）ThreadPoolExecutor**

ThreadPoolExecutor通常使用工厂类Executors来创建。Executors可以创建3种类型的ThreadPoolExecutor：SingleThreadExecutor、FixedThreadPool和CachedThreadPool。

1）FixedThreadPool。创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。

2）SingleThreadExecutor。创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。

3）CachedThreadPool。创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

**（2）ScheduledThreadPoolExecutor**

创建一个定长线程池，支持定时及周期性任务执行。

ScheduledThreadPoolExecutor通常使用工厂类Executors来创建。Executors可以创建2种类型的ScheduledThreadPoolExecutor，如下。

❑ ScheduledThreadPoolExecutor。包含若干个线程的ScheduledThreadPoolExecutor。

❑ SingleThreadScheduledExecutor。只包含一个线程的ScheduledThreadPoolExecutor。

**（3）Future接口**

Future接口和实现Future接口的FutureTask类用来表示异步计算的结果。当我们把Runnable接口或Callable接口的实现类提交（submit）给ThreadPoolExecutor或ScheduledThreadPoolExecutor时，ThreadPoolExecutor或ScheduledThreadPoolExecutor会 向我们返回一个FutureTask对象。

**（4）Runnable接口和Callable接口**

Runnable接口和Callable接口的实现类，都可以被ThreadPoolExecutor或Scheduled-ThreadPoolExecutor执行。它们之间的区别是Runnable不会返回结果，而Callable可以返回结果。

除了可以自己创建实现Callable接口的对象外，还可以使用工厂类Executors来把一个Runnable包装成一个Callable。

### 不推荐使用Executors创建线程池

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuaG9sbGlzY2h1YW5nLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxOC8xMC8xNTQwNjI1NDEyMTEzMS5qcGc?x-oss-process=image/format,png)

### ThreadPoolExecutor构造方法

```java
// Java线程池的完整构造函数
public ThreadPoolExecutor(
  int corePoolSize, // 线程池长期维持的线程数，即使线程处于Idle状态，也不会回收。
  int maximumPoolSize, // 线程数的上限
  long keepAliveTime, TimeUnit unit, // 超过corePoolSize的线程的idle时长，
                                     // 超过这个时间，多余的线程会被回收。
  BlockingQueue<Runnable> workQueue, // 任务的排队队列
  ThreadFactory threadFactory, // 新线程的产生方式
  RejectedExecutionHandler handler) // 拒绝策略
```

- corePoolSize：核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；
- maximumPoolSize：线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程；
- keepAliveTime：表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize，即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；
- unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性：

```java
TimeUnit.DAYS;               //天
TimeUnit.HOURS;             //小时
TimeUnit.MINUTES;           //分钟
TimeUnit.SECONDS;           //秒
TimeUnit.MILLISECONDS;      //毫秒
TimeUnit.MICROSECONDS;      //微妙
TimeUnit.NANOSECONDS;       //纳秒
```

- workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：

```java
ArrayBlockingQueue;
LinkedBlockingQueue;
SynchronousQueue;
```

　　ArrayBlockingQueue和PriorityBlockingQueue使用较少，一般使用LinkedBlockingQueue和Synchronous。线程池的排队策略与BlockingQueue有关。

- threadFactory：线程工厂，主要用来创建线程；
- handler：表示当拒绝处理任务时的策略，有以下四种取值：

```java
ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 
ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 
ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务 
```



### ThreadPoolExecutor类中几个重要的方法：

　execute()方法实际上是Executor中声明的方法，在ThreadPoolExecutor进行了具体的实现，这个方法是ThreadPoolExecutor的核心方法，通过这个方法可以向线程池提交一个任务，交由线程池去执行。

　submit()方法是在ExecutorService中声明的方法，在AbstractExecutorService就已经有了具体的实现，在ThreadPoolExecutor中并没有对其进行重写，这个方法也是用来向线程池提交任务的，但是它和execute()方法不同，它能够返回任务执行的结果，去看submit()方法的实现，会发现它实际上还是调用的execute()方法，只不过它利用了Future来获取任务执行结果（Future相关内容将在下一篇讲述）。

　shutdown()和shutdownNow()是用来关闭线程池的。

### 任务的执行

execute –> addWorker –>runworker （getTask）
线程池的工作线程通过Woker类实现，在ReentrantLock锁的保证下，把Woker实例插入到HashSet后，并启动Woker中的线程。
从Woker类的构造方法实现可以发现：线程工厂在创建线程thread时，将Woker实例本身this作为参数传入，当执行start方法启动线程thread时，本质是执行了Worker的runWorker方法。
firstTask执行完成之后，通过getTask方法从阻塞队列中获取等待的任务，如果队列中没有任务，getTask方法会被阻塞并挂起，不会占用cpu资源；

runWorker方法是线程池的核心：
1. 线程启动之后，通过unlock方法释放锁，设置AQS的state为0，表示运行可中断；
2. Worker执行firstTask或从workQueue中获取任务：
2.1. 进行加锁操作，保证thread不被其他线程中断（除非线程池被中断）
2.2. 检查线程池状态，倘若线程池处于中断状态，当前线程将中断。
2.3. 执行beforeExecute
2.4 执行任务的run方法
2.5 执行afterExecute方法
2.6 解锁操作

### **线程池状态**

在ThreadPoolExecutor中定义了一个volatile变量，另外定义了几个static final变量表示线程池的各个状态：

```java
volatile int runState;
static final int RUNNING    = 0;
static final int SHUTDOWN   = 1;
static final int STOP       = 2;
static final int TERMINATED = 3;
```

 　runState表示当前线程池的状态，它是一个volatile变量用来保证线程之间的可见性；

　　下面的几个static final变量表示runState可能的几个取值。

　　当创建线程池后，初始时，线程池处于RUNNING状态；

　　如果调用了shutdown()方法，则线程池处于SHUTDOWN状态，此时线程池不能够接受新的任务，它会等待所有任务执行完毕；

　　如果调用了shutdownNow()方法，则线程池处于STOP状态，此时线程池不能接受新的任务，并且会去尝试终止正在执行的任务；

　　当线程池处于SHUTDOWN或STOP状态，并且所有工作线程已经销毁，任务缓存队列已经清空或执行结束后，线程池被设置为TERMINATED状态。

### 线程池提交

![image-20200318135907030](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200318135907030.png)

![这里写图片描述](https://img-blog.csdn.net/20180403100716171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJvZ3JhbW1lcl9hdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 获取处理结果和异常

线程池的处理结果、以及处理过程中的异常都被包装到`Future`中，并在调用`Future.get()`方法时获取，执行过程中的异常会被包装成`ExecutionException`，`submit()`方法本身不会传递结果和任务执行过程中的异常。获取执行结果的代码可以这样写：

```java
ExecutorService executorService = Executors.newFixedThreadPool(4);
Future<Object> future = executorService.submit(new Callable<Object>() {
        @Override
        public Object call() throws Exception {
            throw new RuntimeException("exception in call~");// 该异常会在调用Future.get()时传递给调用者
        }
    });
    
try {
  Object result = future.get();
} catch (InterruptedException e) {
  // interrupt
} catch (ExecutionException e) {
  // exception in Callable.call()
  e.printStackTrace();
}
```

## 并发编程实践

### 生产者和消费者模式

在并发编程中使用生产者和消费者模式能够解决绝大多数并发问题。该模式通过平衡生产线程和消费线程的工作能力来提高程序整体处理数据的速度。

在线程世界里，生产者就是生产数据的线程，消费者就是消费数据的线程。在多线程开发中，如果生产者处理速度很快，而消费者处理速度很慢，那么生产者就必须等待消费者处理完，才能继续生产数据。同样的道理，如果消费者的处理能力大于生产者，那么消费者就必须等待生产者。为了解决这种生产消费能力不均衡的问题，便有了生产者和消费者模式。

生产者和消费者模式是通过一个容器来解决生产者和消费者的强耦合问题。生产者和消费者彼此之间不直接通信，而是通过阻塞队列来进行通信，所以生产者生产完数据之后不用等待消费者处理，直接扔给阻塞队列，消费者不找生产者要数据，而是直接从阻塞队列里取，阻塞队列就相当于一个缓冲区，平衡了生产者和消费者的处理能力。

### wait() / notify()方法

```java
//仓库
import java.util.LinkedList;

public class Storage {

    // 仓库容量
    private final int MAX_SIZE = 10;
    // 仓库存储的载体
    private LinkedList<Object> list = new LinkedList<>();

    public void produce() {
        synchronized (list) {
            while (list.size() + 1 > MAX_SIZE) {
                System.out.println("【生产者" + Thread.currentThread().getName()
		                + "】仓库已满");
                try {
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            list.add(new Object());
            System.out.println("【生产者" + Thread.currentThread().getName()
                    + "】生产一个产品，现库存" + list.size());
            list.notifyAll();
        }
    }

    public void consume() {
        synchronized (list) {
            while (list.size() == 0) {
                System.out.println("【消费者" + Thread.currentThread().getName() 
						+ "】仓库为空");
                try {
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            list.remove();
            System.out.println("【消费者" + Thread.currentThread().getName()
                    + "】消费一个产品，现库存" + list.size());
            list.notifyAll();
        }
    }
}


//生产者
public class Producer implements Runnable{
    private Storage storage;

    public Producer(){}

    public Producer(Storage storage , String name){
        this.storage = storage;
    }

    @Override
    public void run(){
        while(true){
            try{
                Thread.sleep(1000);
                storage.produce();
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }
}


//消费者
public class Consumer implements Runnable{
    private Storage storage;

    public Consumer(){}

    public Consumer(Storage storage , String name){
        this.storage = storage;
    }

    @Override
    public void run(){
        while(true){
            try{
                Thread.sleep(3000);
                storage.consume();
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }
}


//主函数
public class Main {

    public static void main(String[] args) {
        Storage storage = new Storage();
        Thread p1 = new Thread(new Producer(storage));
        Thread p2 = new Thread(new Producer(storage));
        Thread p3 = new Thread(new Producer(storage));

        Thread c1 = new Thread(new Consumer(storage));
        Thread c2 = new Thread(new Consumer(storage));
        Thread c3 = new Thread(new Consumer(storage));

        p1.start();
        p2.start();
        p3.start();
        c1.start();
        c2.start();
        c3.start();
    }
}


//输出
【生产者p1】生产一个产品，现库存1
【生产者p2】生产一个产品，现库存2
【生产者p3】生产一个产品，现库存3
【生产者p1】生产一个产品，现库存4
【生产者p2】生产一个产品，现库存5
【生产者p3】生产一个产品，现库存6
【生产者p1】生产一个产品，现库存7
【生产者p2】生产一个产品，现库存8
【消费者c1】消费一个产品，现库存7
【生产者p3】生产一个产品，现库存8
【消费者c2】消费一个产品，现库存7
【消费者c3】消费一个产品，现库存6
【生产者p1】生产一个产品，现库存7
【生产者p2】生产一个产品，现库存8
【生产者p3】生产一个产品，现库存9
【生产者p1】生产一个产品，现库存10
【生产者p2】仓库已满
【生产者p3】仓库已满
【生产者p1】仓库已满
【消费者c1】消费一个产品，现库存9
【生产者p1】生产一个产品，现库存10
【生产者p3】仓库已满
。。。。。。以下省略

```

###  await() / signal()方法

```java
import java.util.LinkedList;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Storage {

    // 仓库最大存储量
    private final int MAX_SIZE = 10;
    // 仓库存储的载体
    private LinkedList<Object> list = new LinkedList<Object>();
    // 锁
    private final Lock lock = new ReentrantLock();
    // 仓库满的条件变量
    private final Condition full = lock.newCondition();
    // 仓库空的条件变量
    private final Condition empty = lock.newCondition();

    public void produce()
    {
        // 获得锁
        lock.lock();
        while (list.size() + 1 > MAX_SIZE) {
            System.out.println("【生产者" + Thread.currentThread().getName()
		             + "】仓库已满");
            try {
                full.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        list.add(new Object());
        System.out.println("【生产者" + Thread.currentThread().getName() 
				 + "】生产一个产品，现库存" + list.size());

        empty.signalAll();
        lock.unlock();
    }

    public void consume()
    {
        // 获得锁
        lock.lock();
        while (list.size() == 0) {
            System.out.println("【消费者" + Thread.currentThread().getName()
		             + "】仓库为空");
            try {
                empty.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        list.remove();
        System.out.println("【消费者" + Thread.currentThread().getName()
		         + "】消费一个产品，现库存" + list.size());

        full.signalAll();
        lock.unlock();
    }
}

```

### BlockingQueue阻塞队列方法

```java
import java.util.concurrent.LinkedBlockingQueue;

public class Storage {

    // 仓库存储的载体
    private LinkedBlockingQueue<Object> list = new LinkedBlockingQueue<>(10);

    public void produce() {
        try{
            list.put(new Object());
            System.out.println("【生产者" + Thread.currentThread().getName()
                    + "】生产一个产品，现库存" + list.size());
        } catch (InterruptedException e){
            e.printStackTrace();
        }
    }

    public void consume() {
        try{
            list.take();
            System.out.println("【消费者" + Thread.currentThread().getName()
                    + "】消费了一个产品，现库存" + list.size());
        } catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

### 信号量

```java
import java.util.LinkedList;
import java.util.concurrent.Semaphore;

public class Storage {

    // 仓库存储的载体
    private LinkedList<Object> list = new LinkedList<Object>();
	// 仓库的最大容量
    final Semaphore notFull = new Semaphore(10);
    // 将线程挂起，等待其他来触发
    final Semaphore notEmpty = new Semaphore(0);
    // 互斥锁
    final Semaphore mutex = new Semaphore(1);

    public void produce()
    {
        try {
            notFull.acquire();
            mutex.acquire();
            list.add(new Object());
            System.out.println("【生产者" + Thread.currentThread().getName()
                    + "】生产一个产品，现库存" + list.size());
        }
        catch (Exception e) {
            e.printStackTrace();
        } finally {
            mutex.release();
            notEmpty.release();
        }
    }

    public void consume()
    {
        try {
            notEmpty.acquire();
            mutex.acquire();
            list.remove();
            System.out.println("【消费者" + Thread.currentThread().getName()
                    + "】消费一个产品，现库存" + list.size());
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            mutex.release();
            notFull.release();
        }
    }
}
```

### 管道

#### PipedInputStream / PipedOutputStream （操作字节流）

```java
//生产者

import java.io.IOException;
import java.io.PipedOutputStream;

public class Producer implements Runnable {

    private PipedOutputStream pipedOutputStream;

    public Producer() {
        pipedOutputStream = new PipedOutputStream();
    }

    public PipedOutputStream getPipedOutputStream() {
        return pipedOutputStream;
    }

    @Override
    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                pipedOutputStream.write(("This is a test, Id=" + i + "!\n").getBytes());
            }
            pipedOutputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


//消费者

import java.io.IOException;
import java.io.PipedInputStream;

public class Consumer implements Runnable {
    private PipedInputStream pipedInputStream;

    public Consumer() {
        pipedInputStream = new PipedInputStream();
    }

    public PipedInputStream getPipedInputStream() {
        return pipedInputStream;
    }

    @Override
    public void run() {
        int len = -1;
        byte[] buffer = new byte[1024];
        try {
            while ((len = pipedInputStream.read(buffer)) != -1) {
                System.out.println(new String(buffer, 0, len));
            }
            pipedInputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


//主函数


import java.io.IOException;

public class Main {

    public static void main(String[] args) {
        Producer p = new Producer();
        Consumer c = new Consumer();
        Thread t1 = new Thread(p);
        Thread t2 = new Thread(c);
        try {
            p.getPipedOutputStream().connect(c.getPipedInputStream());
            t2.start();
            t1.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

####  PipedReader / PipedWriter （操作字符流）

```java
//生产者
import java.io.IOException;
import java.io.PipedWriter;

public class Producer implements Runnable {

    private PipedWriter pipedWriter;

    public Producer() {
        pipedWriter = new PipedWriter();
    }

    public PipedWriter getPipedWriter() {
        return pipedWriter;
    }

    @Override
    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                pipedWriter.write("This is a test, Id=" + i + "!\n");
            }
            pipedWriter.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


//消费者
import java.io.IOException;
import java.io.PipedReader;

public class Consumer implements Runnable {
    private PipedReader pipedReader;

    public Consumer() {
        pipedReader = new PipedReader();
    }

    public PipedReader getPipedReader() {
        return pipedReader;
    }

    @Override
    public void run() {
        int len = -1;
        char[] buffer = new char[1024];
        try {
            while ((len = pipedReader.read(buffer)) != -1) {
                System.out.println(new String(buffer, 0, len));
            }
            pipedReader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


//主函数

import java.io.IOException;

public class Main {

    public static void main(String[] args) {
        Producer p = new Producer();
        Consumer c = new Consumer();
        Thread t1 = new Thread(p);
        Thread t2 = new Thread(c);
        try {
            p.getPipedWriter().connect(c.getPipedReader());
            t2.start();
            t1.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

