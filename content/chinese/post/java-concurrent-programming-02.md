---
title: "Java内存模型"
date: 2022-03-30T12:44:33+08:00

# post thumb

images:

- "images/java-concurrent-programing/java-memory-model/thumb.jpg"

# author

author: "郁金香啊"

# description

description: "Java内存模型"

# Taxonomies

categories: ["Java","并发编程"]
tags: ["Java","并发编程","内存模型"]
type: "regular"
draft: false
---
在我们学习操作系统时，有一个章节会讲数据存储相关的的内容，那个经典的金字塔模型。哈哈，又一个**Trade off**
的完美诠释。这个金字塔模型中指出，通常我们可以很容易地获得一些存储设备，其数据容量大，成本低廉，但是访问速度较慢，比如我们常见的磁盘，甚至可以扩展到网盘。我们还有一些存储介质，其容量非常小，小到当我们使用这类存储介质所能提供的空间时，不得不小心翼翼，而且此类介质一般价格十分高昂，但是由于这类存储介质相对来说更加靠近我们的计算单元，所以其访问速度非常非常快，几乎可以忽略不记，比如我们的寄存器。

在我们讨论这个抽象的金字塔概念之前，让我们先看一个生活中比较常见的例子。比如你是一个木匠，你准备做点科研，造个什么木制品出来，这个作品相当精妙，以至于需要十几种甚至更多的工具来帮助你完成，假定这些工具目前放在你的车库。

现实中，当你用工具的时候你并不会总是去车库找工具，你可能会先找一个小盒子将你认为在不远的将来你可能会用到的工具先一次性取出来。此外你甚至不会说是每次用什么工具都会去盒子里面找一下，一般你总会保证伸手就够得着的地方有最近需要使用的那么几件工具，此外，你的手上可能握着正在使用的一到两件工具。你会发现，从车库到盒子到手够得着的位置再到你的手上，能够维护的工具数量越来越少，车库总是能够放上很多的工具，箱子里面可能有十几个，在身边就能够着的区域可能就只能放四五个了，手上可能拿两个工具已经是极限了。

但是不可否认，这的确会对你的工作效率产生很大的提升，其实这得益于两个定理，第一个，现在正在使用的工具在不远的未来更可能会被用到，和这个很好理解，同一个工具你可能会用很长一段时间；一个工具被用到，对于它来说比较接近，比如功能相似的一些工具在不远的将来更有可能被用到，比如各种型号的木头抛光器。

当然了，有时候你会把手上的工具换下来，然后可能会发现工具没在旁边，你可能还是会去翻盒子，如果盒子里面找不到，比如你压根儿就忘了从车库里取出来，你那么你就不得不跑到车库里面再取一遍了。而且如果还有其他人也依赖于这个车库来获取工具的话，你可能还得及时的将工具还回去，如果每次用完就还回去，你可能会在车库和工作室之间跑来跑去，很影响工作效率；但是如果每次都是到最后作品完成再还回去，可能其他人要抱怨了。聪明的你应该又看到另一个**
Trade off**的影子了吧，哈哈。

让我们再回到之前的金字塔底部，我们现在好像瞥见到了两个极端，一个是金字塔的底部，另一个则是金字塔的顶端。便宜，大容量但是速度慢；速度足够快但是价格高昂且容量很小。好在金字塔不止有底部和顶端，它还有中间的部分，这也是我们的**Trade
off**策略得以游走的空间。我们从金字塔的最底部慢慢向上攀爬探索：

* 越过磁盘，首先我们可能接触的内存，虽然断电后数据就会丢失，但是其访问速度相较于磁盘已经有了质的提升，当然了，相对于磁盘，其价格更高，容量也要小很多，在1TB的固态硬盘充斥在人们的生活中时，我们通常可以操作的内存空间一般只有8G，16G等；很多的操作系统也通过虚拟内存突破了物理内存的限制，所以一般16G的内存对于绝大多数人来说都已经足够了。

* 我们继续向上攀爬，此时我们会遇到称为高速缓存的东西，一般有两级，我们称之为二级缓存和以一级缓存，实际上我们的CPU尝试加载数据时，并不会直接和我们的内存打交道，我们首先会将需要访问的内存连同周边临近的内存区域先加载到二级缓存中，然后再加载一部分到一级缓存中。现在当CPU尝试去加载数据的时候，花在IO上的时间就很短了。

* 然后就是寄存器了，如果你写过汇编或者有看过反编译后的Java的字节码指令，你可能会看到，我们是如何精细地一个一个存储单元的进行访问和管理的。空间很小，但是因为他们是最接近CPU的位置，它们的存在让我们的指令序列得以流畅的执行，而不必每次都花费大量时间等待IO。

Java作为一门编程语言以及一个平台，其底层实现的时候，其实也逃不掉这些模式的束缚。只不过我们上面讨论的是硬件资源上的一些**Trade off**，Java内存模型是在这个基础之上又进行了一层抽象，但是其内在原理还是相通的。

本系列文章在编写时大量参考了《Java并发编程艺术》一书，原书将Java内存模型放在第三章，将Java语言中的一些特性以及实现原理，比如synchronized，volatile等关键字放在第二章。当我在准备第二篇文章的时候，老师感觉写起来不是很顺畅，因为里面有很多的概念其实是依赖于Java内存模型中的很多内容的。所以就调整了一下顺序，将JMM提到前面来写一下。

# 目录
* [Java内存模型](#java-memory-model)
  * [Java内存模型的抽象结构](#java-memory-model-abstract-structure)
  * [指令重排序](#instruction-reordering)
    * [处理器指令重排序](#cpu-instruction-reordering)
    * [内存屏障](#memory-barriers)
    * [Happens-before原则](#happens-before-rules)
    * [重排序](#reordering)
      * [数据依赖性](#data-dependencies)
      * [as-if-serial语义](#as-if-serial-semantic)
      * [程序顺序规则](#procedural-order-rule)
      * [重排序对多线程的影响](#reordering-effects)
    * [顺序一致性](#sequential-consistency)
      * [数据竞争与顺序一致性](#data-race-and-sequentil-consistency)
      * [顺序一致性内存模型](#sequential-consistency-memory-model)
      * [同步程序的顺序一致性效果](#sc-of-sysnchronized-program)
      * [未同步程序的执行特性](#execution-behaviour-of-non-synchronized-program)
    * [Volatile的内存语义](#volatile-memory-semantic)
    * [Volatile写-读建立的happens-before关系](#volatile-happens-before-relationship)
    * [volatile内存语义的实现](#volatile-memory-semantic-impl)
    * [锁的内存语义](#lock-memory-semantic)
      * [锁的释放与获取](#lock-release-and-accquire)
      * [锁内存语义的实现](#lock-memory-semantic-impl)
      * [ReentrantLock-公平锁](#fair-reentrant-lock)
      * [ReentrantLock-非公平锁](#non-fair-reentrant-lock)
  * [Concurrent包的实现](#concurrent-module-impl)
  * [final域的内存语义](#final-memory-semantic)
    * [final域的重排序规则](#final-reordering-rule)
      * [写final域的重排序规则](#write-final-reordering-rule)
      * [读final域的重排序规则](#read-final-reordering-rule)
      * [final域为引用类型](#final-reference)
  * [happens-before](#happens-before-detail)
    * [Java内存模型的设计](#java-memory-module-design)
    * [happens-before的定义](#happens-before-defination)
    * [happens-before规则](#all-happens-before-rules)
  * [双重检查锁&延迟初始化](#lazy-initialization-and-double-check-lock)
    * [基于volatile的解决方案](#lazy-initialization-using-volatile)
    * [基于类初始化的解决方案](#lazy-initialization-using-class-initialization)
  * [总结](#sumarry)
    * [处理器的内存模型](#processor-memory-modal)
    * [各种内存模型之间的关系](#relationship-between-different-memory-model)
    * [Java内存模型的内存可见性保证](#jmm-and-memory-visibility)

# Java内存模型 <div id="java-memory-model" />

在并发编程中，我们有两个问题需要解决，当多个线程共同合作完成一个或者多个特定问题时，我们定义好线程之间如何进行通信以及如何进行同步的。通信是指线程之间如何交换信息，包括获取处理的入参输出处理的结果等等。线程之前通信的方式两种，消息传递和共享内存。

其中消息传递这种模式，在一些移动开发时可能会遇到，比如Android开发时，我们会有一个UI线程，还会有一些子线程，子线程一般用于去获取数据，比如调用一个接口获取数据，而主线程我们也叫UI线程的话，顾名思义，主要工作是进行UI的渲染。我们不希望在UI线程里面去进行IO操作，这样可能会导致线程阻塞相当长的时间，界面就无法进行组件的渲染，更无法响应用户的操作，这对用户来说是完全不可接受的。真因为这样我们才会在子线程中做IO相关的操作。当子线程成功加载好数据之后，我们会使用一种消息传递的机制来通知UI线程，我已经成功获取了数据，你可以拿最新的数据进行渲染了。

相比于消息传递，共享内存这种方式来进行数据共享就相对来说要常见的多了。多个线程往往通过读写内存中的公共状态来进行隐式通信。

不管是消息传递还是共享内存，我们都需要做一件事情，那就是指定一个数据的同步访问机制，避免由于大家同时对数据进行读写导致最终获得一个错误的结果。在消息传递模型中，消息的接收一定在消息的发送之后，因此共享数据的访问一直都是串行的，同步是隐式进行的。在共享内存模型中，数据访问的顺序无法预见，所以程序员必须显式的指定某个方法或者某个代码片段需要在线程之间互斥的执行，同步是显式进行的。

Java并发采用的是共享内存模型，默认情况下，Java线程之前的通信总是隐式进行的，整个通信过程对程序员完全透明，如果程序员没有意识到这个并且不手动加以控制就不可能得到正确的结果。

## Java内存模型的抽象结构  <div id="java-memory-model-abstract-structure" />

Java定义了一套抽象的模型来控制线程间在进行内存共享时共享内容在各个线程之间的可见性，这个模型决定了一个线程对一个共享变量的写入何时对另一个线程可见。

从抽象角度来看，JMM定义了主存和线程工作内存之间的关系。线程间的共享变量存储在主存中，而每一个线程都有一个线程私有的本地内存，我们也称之为工作内存，工作内存中存储了该线程读写共享变量的副本。

{{< notice "note" >}} 工作内存是JMM的一个抽象概念，并不是真实存在的，它涵盖了寄存器，高速缓存，写缓冲区以及其他硬件以及编译器优化。 {{< /notice >}}

假定两个线程A，B同时访问一个共享变量var，根据JMM，除了主存中的数据var，线程A，B在工作内存中还存在两个副本，var-A，var-B。当线程A，B对共享变量进行读取或者修改时，他们直接操作工作内存中的副本。

那么当其中一个线程对数据进行了更新时，如果另一个线程想要读取到最新的结果，我们就必须将更新过后的值刷新到主内存中去，然后另一个线程在读取数据时，不能直接从工作内存的副本中读取过期的数据，其必须直接从主存中加载最新的结果。而JMM正是通过控制朱迅与每个线程的本地内存之间的交互，来为Java程序员提供内存可见性的保证。

## 指令重排序 <div id="instruction-reordering" />

在执行程序时，为了提高性能，编译器和处理器常常会对指令做重排序，其分为以下三种类型：

* 编译器优化的指令重排序 - 编译器在不改变单线程语义的情况下，可以重新安排语句的执行顺序
* 指令级并行重排序 - 如果多条指令之间不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序。
* 内存系统的重排序 - 由于处理器使用缓存和读写缓冲区，使得加载和存储操作看上去可能在乱序执行。

重排序能够提升性能，但是也可能带来一些内存可见性问题，比如在单例模式实现的例子中就会讲到，指令重排序是如何导致可见性问题并使用volatile关键字来解决它的。

对于编译器，JMM的编译器重排序规则会禁止特定类型的编译器重排序。对于指令级并行重排序，JMM的处理器重排序规则会要求Java编译器在生成指令序列时插入特定的内存屏障指令，通过这种指令来禁止特定类型的指令级并行重排序。

JMM属于语言级的内存模型，它确保在不同比那一起和不同的处理器平台之上，通过禁特定类型的编译器重排序和处理器重排序，为程序员提供一致的内存可见性保证。

### 处理器指令重排序  <div id="cpu-instruction-reordering" />

现代的处理器使用写缓冲区临时保存向内存写入的数据，写缓冲区可以保证指令流水线持续运行，它可以避免由于处理器停下来等待向内存写入数据而产生的延迟。同时，通过以批处理的形式刷新写缓冲区，以及合并写缓冲区对同一内存地址的多次写，减少对内存总线的占用。

虽然写缓冲区有很多的好处，但是每个处理器上的写缓冲区仅仅对他所在的处理器可见。这个特性会对内存操作的执行顺序产生重要影响，即处理器对内存的读写操作的执行顺序，不一定与内存时间发生的读、写顺序一致。

假定我们主存中有两个变量a，b，它们的初始值都为0；我们现在有两个处理器，Processor A，Processor B；Processor A， Processor
B分别加载a，b，并将其值更改为1。我们此时期望现在两个变量值都是1，但是我们刚刚的写操作可能仅仅发生在缓冲区中。此时我们Processor A，Processor
B再分别加载b，a然后将写缓冲区中的内容刷新到主存中。此时工作内存中就存在a，b两个变量的无效的副本，a=0，b=0。

从内存操作实际发生的顺序来看，直到处理器最终刷新数据到主存中，我们的写操作才算真正执行了。我们期望的是我们分别将工作内存中的两个变量副本写回到主存中，然后再分别加载这两个变量的最新值到变量副本中，但是因为存在写缓存，导致我们的内存操作顺序发生了变化，期望的写->
读，变成了读->写。这种情况我们就称之为处理器的内存操作被重排序了。

这里的关键是由于写缓冲区仅仅对自己的处理器可见，它会导致处理器执行内存操作的顺序与世界操作执行的顺序不一致。现在大多数处理器都会使用写缓冲区，因此现代的处理器都允许对写-读操作进行重排序。

### 内存屏障 <div id="memory-barriers" />

为了保证内存可见性，Java编译器在生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序，Java内存模型会将内存屏障指令氛围四类，

|        屏障类型      |           指令示例          |                                   说明                                    |
|---------------------|---------------------------|--------------------------------------------------------------------------|
| LoadLoad Barriers   | Load1；LoadLoad；Load2     | 确保Load1数据的装载先于Load2及所有后续装载指令的装载                             |
| StoreStore Barriers | Store1；StoreStore；Store2 | 确保Store1数据对其他处理器可见，指刷新到内存，优先于Store2及所有后续存储指令的存储     |
| LoadStore Barriers  | Load1；LoadStore；Store2   | 确保Load1数据装载限于Store2及所有后续的存储指令刷新到内存                         |
| StoreLoad Barriers  | Store1；StoreLoad；Load2   | 确保Store1数据对其它处理器变得可见，指刷新到内存，优先于Load2以及所有后续装载指令的装载 |

StoreLoad barriers是一个全能型屏障，它同时具有其它三个屏障的效果。现代的多处理器大多支持该屏障。执行该屏障开销会很高昂，因为需要将当前处理器的写缓冲区中的数据全部刷新到内存中去（Buffer Fully Flush）。

### Happens-before原则 <div id="happens-before-rules" />

从JDK5开始，Java使用洗的呢JSR-133内存模型。JSR-133使用happens-before的概念来阐述操作之间的内存可见性。在Java内存模型中，一个操作的执行结果需要对另一个操作可见，那么这两个操作之间必须要存在happens-before关系。这里提到的两个操作既可以是在同一个线程内部，也可以是在不同线程之间。

与程序员密切相关的happens-before规则如下：

* 程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。即同一个线程中的多个操作天然满足happens-before规则。
* 监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。即当前同步代码块的结果对使用一个锁的下一个同步代码块全部可见。
* Volatile变量规则：对一个volatile域的写，happens-before于随后对这个域的读。
* 传递性：如果A happens-before B且B happens-before C，那么A happens-before C；

{{< notice "note" >}}
两个操作之间具有happens-before关系，并不意味着前一个操作必须要在后一个操作之前执行，happens-before仅仅要求前一个操作执行的结果对后一个操作可见，且前一个操作按顺序排在第二个操作之前。 
{{< /notice >}}

一个happens-before规则对应于一个或者多个编译器重排序规则和处理重排序规则。对于程序员来说，happens-before规则简单易懂，它避免程序员为了理解Java内存模型提供的内存可见性保证去学习复杂的编译器、处理器重排序规则以及这些规则的具体实现方法。

### 重排序  <div id="reordering" />

重排序是指编译器核处理器为了优化性能而对指令序列进行重新排序的一种手段。

#### 数据依赖性  <div id="data-dependencies" />

如果两个操作访问同一个变量，且这两个操作中一个是写操作一个是读操作，此时这两个操作之间就存在数据依赖性。数据依赖性有三种情况：

* 写后读 - 写一个变量之后再读这个位置
* 读后写 - 读一个变量之后再写这个位置
* 写后写 - 写一个变量之后再写这个位置

上面这三种情况只要发生了重排序，程序的执行结果就会发生变化。

编译器和处理器会对操作做重排序，编译器和处理器在重排序时，会遵守数据依赖性，编译器和处理器不会改变存在数据依赖关系的两个操作的执行顺序。当然了，这里说的数据依赖性仅针对当个处理器中执行的指令序列和单个线程中执行的操作。不同处理器之间的和不同线程之间的数据依赖性不会被编译器和处理器考虑。总而言之，编译器重排序和处理器重排序不会影响单个线程的预期执行结果。

#### as-if-serial语义  <div id="as-if-serial-semantic" />

as-if-serial语义的意思是，不管怎么重排序，编译器和处理器重排序的目的是提高并行度，提高执行速度，但是程序的执行记过不能被改变，编译器，runtime和处理器都必须遵守as-if-serial语义。

为了遵循as-if-serial语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果，但是如果操作之间不存在数据依赖关系，这些操作就可能别编译器和处理器重排序。比如我们看一段计算圆面积的代码：

```java
public class Solution {
    public void calculateArea() {
        double pi = 3.14; // A 
        double r = 1.0; // B 
        double area = pi * r * r; // C
    }
}
```

ABC三个操作的依赖关系为，C依赖A，C依赖B，C不能被重排序到A和B的前面，否则程序执行的结果可能被改变。但是A和B之间没有依赖关系，编译器和处理器合一重排序A，B两个操作的执行顺序。实际执行顺序可能是A->B->C或者B->A->
C，最终程序执行结果都会是3.14。

as-if-serial语义把单线程程序保护了起来，遵守as-if-serial语义的编译器，runtime和处理器共同为编写单线程程序的程序员创建了一个幻觉，单线程程序是按照程序的编写顺序来执行的，as-if-serial语义是单线程程序员无需担心重排序会干扰他们，也无需担心内存可见性问题。

#### 程序顺序规则  <div id="procedural-order-rule" />

根据happens-before的程序顺序规则，上面计算圆面积的代满足三个happens-before原则。

1. A happens-before B
2. B happens-before C
3. A happens-before C

这里A happens-before B，但是实际执行时，B可以排在A之前执行，如果A happens-before
B，Java内存模型并不会要求A一定要在B之前执行。Java内存模型仅仅要求前一个操作执行的结果内后一个操作可见。且前一个操作排在后一个操作之前，即在程序员的视角来看，代码的编写顺序，或者说是程序员理解的最终指令编译的顺序上来说，前一个操作排在后一个操作之前。

要求前一个操作（执行的结果）对后一个操作可见意味着，如果不需要这种可见性，也就是不存在数据依赖关系的话，也即我们可以对这两个指令甚至宏观上来说这两行代码进行重排序而不影响最终的执行结果的话，JMM就认为这种重排序并不非法（not
illegal）。

在计算机中，软件技术和硬件技术有一个共同的目标，在不改变程序执行结果的前提下，尽可能高的提高并行度。编译器和处理器遵从这一目标，从happens-before的定义我们也可以看出，Java内存模型也遵从这一目标。

#### 重排序对多线程的影响 <div id="reordering-effects" />

让我们看一个示例代码：

```java
public class Solution {
    int a = 0;
    boolean flag = false;

    public void write() {
        a = 1;    // 1
        flag = true;    // 2 
    }

    public void read() {
        if (flag) {    // 3 
            int i = a * a;    // 4
        }
    }
}
```

flag是一个标志，用来标识变量a是否已经被写入。这里假设有两个线程A和B，A首先执行write方法，随后线程B接着执行reader方法，线程B在执行操作4时，能否看到线程A在操作1对变量A的写入呢。答案是不一定。

由于操作1和操作2没有数据依赖关系，编译器和处理器你可以对这两个操作重排序。同理，操作3和操作4也没有数据依赖关系（有控制依赖但是没有数据依赖，编译器处理器为了提高并行度会采用猜测执行），编译器和处理器也可以对这两个操作机械能重排序。

当操作1和操作2重排序时，假定我们的执行顺序是2->3->4->1，第四步我们读取的a并不是写入后的a而是初始值0，多线程的语义被重排序破坏掉了。
当操作3和操作4重排序时，操作3和操作4寸控制依赖关系，有控制依赖但是没有数据依赖时会影响指令序列的执行并行度，编译器处理器为了提高并行度会采用猜测执行来克服控制相关性对并行度的影响，已处理器猜测执行为例，执行线程B的处理器可以提前读取a并执行第四步的计算，然后将结果存储在一个重排序缓冲（Reorder
Buffer，ROB）的硬件缓存中，当第三步的条件判断为真的时候，就把缓冲区中的计算结果写入结果变量中。也就是执行顺序实际上等价于4->1->2->
3。在我们进行if条件判断之前，前读取a完成了第四步的计算，最终结果仍然是0，多线程的语义再次被重排序破坏掉了。

在单线程中，对存在空盒子依赖的操作重排序并不会改变执行结果，这也是为什么as-if-serial语义允许对存在控制依赖的操作做重排序。但是在多线程程序中，对存在控制依赖的操作重排序，可能会改变程序的执行结果。

### 顺序一致性 <div id="sequential-consistency" />

顺序一致性内存模型是一个理论参考模型，在设计的时候后，处理器的内存模型和编程语言的内存的模型都会一顺序一致性的内存模型作为参照。

#### 数据竞争与顺序一致性 <div id="data-race-and-sequentil-consistency" />

在程序未正确同步是，就可能会存在数据竞争。Java内存模型规范对数据竞争的定义如下

* 在一个线程中写一个变量
* 在另一个线程中读取同一个变量
* 而且读和写没有通过同步来控制

当代码包含数据竞争时，程序的执行往往产生违反直觉的结果。此时我们只能通过真确的数据同步来保证程序每次执行都会有一致的期望的行为。我们也称作是程序的执行具有顺序一致性，程序执行的结果与该程序在顺序一致性内存模型中的执行结果相同。怎么理解呢，就是我们把位于两个不同线程中的两次执行过程进行展开，然后放到同一个线程中执行，其是等价的。

#### 顺序一致性内存模型 <div id="sequential-consistency-memory-model" />

顺序一致性模型是一个被计算机科学家理想化了的理论参考模型，他为程序员提供了极强的内存可见性保证。其有两个特点

* 一个线程中的所有操作必须按照程序的顺序来执行
* 不管程序是否同步，所有线程都只能看到一个单一的操作执行顺序，在顺序一致性内存模型中，每个操作都必须原子执行且立刻对所有线程可见。

再概念上，顺序一致性模型有一个单一的全局内存，这个内存通过一个左右摆动的开关来实现在任何一个时刻仅连接到任意一个线程，同时，每一个线程必须按照程序的顺序来执行内存读写操作，那么也意味着在任意时间点最多只能有一个线程可以来接到内存，这样一来，所有线程的所有内存读写操作就被串行化了，在顺序一致性模型中，所有操作之间存在全序关系。

这只是一个理论模型，它非常简单，最容易理解，但是记得我们之前说的**Trade off**
吗，这里其实又有提现。一个模型或者一种机制约简单，理解起来就越简单，但是同事其效率和可用性可能就越低。反过来说，一个模型或者说一个机制设计的越复杂，考虑的越多，其实效率和可用性可能就会越高，但是理解和最终实现起来肯定会花费更多的时间。我们不可能将内存模型设计成这样，那样我们的程序运行起来可能像乌龟在地上爬一样。本文开头讲的存储空间金字塔的概念就是通过一些相对复杂的机制，在既能获得最高性能的同时又保证了存储空间控制在一个合理的价格范围之内。

假定我们两个线程A，B，每个线程分别执行三个操作A1->A2->A3和B1->B2->B3。当我们依赖于监视器锁对程序进行同步时我们的执行顺序可能是A1->A2->A3->B1->B2->B3，当我们没有同步时，执行顺序可能是A1->
B1->B2->B3->A2->A3，所有线程都能够看到一个一致的整体执行顺序，之所以有这个保证是因为顺序一致性模型中的每个操作立即对任意线程可见。

但是顺序一致性内存模型只是一个理论上的模型，在实际的Java内存模型中就没有和这个保证。未同步的程序在Java内存模型中不但招人那个题的执行顺序是无序的，而且所有线程可能看到的操作执行顺序也可能不一致。因为写缓冲区的关系，当前线程执行了写操作，但是数据并没有刷新到主存，这个写操作对当前线程可见，但是对其他线程是不可见的，其它线程会认为当前线程并没有做什么写入操作。只有当前线程将数据刷新到主存中时，这个写操作才能对其他线程可见。此时每个线程看到的总体的执行顺序就不是一致的。

#### 同步程序的顺序一致性效果 <div id="sc-of-sysnchronized-program" />

那么我们怎么在实际的内存模型中获得同理论的顺序一致性模型中一样的顺序一致性呢，答案就是同步，让我们看下使用监视器锁对示例代码进行同步之后的代码

```java
public class Solution {
    int a = 0;
    boolean flag = false;

    public synchronized void write() {
        a = 1;    // 1
        flag = true;    // 2 
    }

    public synchronized void read() {
        if (flag) {    // 3 
            int i = a * a;    // 4
        }
    }
}
```

假设A线程执行了write方法，B线程执行read方法，其执行顺序是1234或者2134，可看出，其执行结果和该程序在顺序一致性模型中的执行结果相同。

在Java内存模型中，临界区内的代码可以重排序且不会被其他线程感知，在进入临界区和退出临界区这两个关键时间点会做一些特殊的处理，是的线程在这两个时间点具有与顺序一致性模型相同的内存视图。有了同步控制，我们其提高了执行效率，也没有改变程序的执行结果。

#### 未同步程序的执行特性 <div id="execution-behaviour-of-non-synchronized-program" />

对于未同步或未正确同步的多线程程序，Java内存模型只提供最小的安全性，线程执行时读取到的值，要么是之前某个线程写入的值，要么是默认值，它保证线程读取到的值不会无中生有。JVM在内配对象的存储空间时，首先会对内存空间清零。

未同步程序在Java内存模型中执行时，整体上是无序的，其执行结果无法预知，未同步程序在顺序一致性模型和Java内存模型中执行特性有以下几个差异

* 顺序一致性模型保证单线程内的操作会按照程序的顺序执行，而Java内存模型不可以，前面有讲到重排序的情况。
* 顺序一致性模型保证所有线程只能看到一致的操作执行顺序，儿Java内存模型不可以，前面有讲到由于写缓冲区导致的多个线程看到的总体的执行顺序不一致的情况。
* 顺序一致性模型保证对所有内存读写操作都具有原子性，但是Java内存模型不保证对64位的long型和double型变量的写操作具有原子性。

第三个差异与处理器总线的工作机制密切相关，在计算机中，数据通过总线在处理器和内存之间传递，每次数据传递都是用过一系列步骤来完成的，我们称之为总线事务。总线事务有读写两种。读事务从内存传送数据到处理器，写数据从处理器传送数据到内存，每个事务会读写内存中一个或者多个物理内存上的连续的空间。在32位的处理器上，如果我们尝试读取或者写入64位数据，我们可能会涉及到需要将读操作或者写操作拆分成两个读操作或者写操作来执行。这两个拆分后的操作可能会被放到两个不同的总线事务中执行，此时就不能保证操作的原子性了。

### Volatile的内存语义 <div id="volatile-memory-semantic" />

理解volatile特性的一个好方法就是把对volatile变量的单个读写看成是使用同一个锁对这些单个读写操作进行了同步。哈哈，我们可能又要解释下进行了同步的话会有什么效果了。

锁的happens-before规则保证释放锁和获取锁的两个线程之间的内存可见性，这意味着对一个volatile变量的读总是能看到对这个变量的最后的写入。

锁的语义决定了临界区代码的执行具有原子性，这意味着，即使64位的long型和double型变量，只要它是volatile变量，对这个变量的读写就具有原子性。

{{< notice "note" >}} 如果是多个volatile操作或者类似于volatile++（其等价于读取，+1计算以及写入三个操作）这种复核操作整体上不具有原子性。 {{< /notice >}}

总而言之，volatile变量具有这些特性

* 可见性，对一个volatile变量的读，总是能够看到任意线程对这个volatile变量最后的写入。
* 原子性，对任意一个volatile变量的读写具有原子性，这里指的是单个的读或者写，类似于volatile++这种复合操作不具有原子性。

### Volatile写-读建立的happens-before关系 <div id="volatile-happens-before-relationship" />

从内存语义来说，volatile的写-读与锁的释放-获取有相同的内存效果：volatile写和锁的释放有相同的内存语义；volatile读与锁的获取有相同的内存语义。

当写一个volatile变量时，Java内存模型会把该线程对应的本地内存中的共享变量值刷新到主存。

当读一个volatile变量时，Java内存模型会把该线程对应的工作内存中的副本置为无效，线程接下来将从主内存中读取共享变量。

```java
public class Solution {
    int a = 0;
    volatile boolean flag = false;

    public synchronized void write() {
        a = 1;    // 1
        flag = true;    // 2 
    }

    public synchronized void read() {
        if (flag) {    // 3 
            int i = a * a;    // 4
        }
    }
}
```

回到我们的示例程序，如果我们的flag被调整成一个volatile变量，当我们将读操作和写操作放到一起来看时，在线程B读取flag这个volatile变量是，线程A在写这个volatile变量之前所有可见的共享变量的值都将立即变得对读线程B可见。

总而言之

* 线程A写一个volatile变量，实质上是线程A向接下来将要读这个volatile变量的某个线程发出了其对共享变量所做修改的消息
* 线程B读一个volatile变量，实质上是线程B接收了之前某个线程发出的，在写这个共享变量之前对共享变量所做修改的消息
* 线程A写一个volatile变量，随后线程B读这个volatile变量，这个过程实质上是线程A通过主内存向线程B发送消息。

### volatile内存语义的实现 <div id="volatile-memory-semantic-impl" />

为了实现volatile内存语义，Java内存模型会分别限制编译器重排序和处理器重排序。

* 当第二个操作是volatile写时，不管第一个操作是什么（普通读写，volatile读写），都不能重排序，这个规则确保volatile写之前的操作不会白编译器重排序到volatile写之后。
* 当第一个操作是volatile读时，不管第二个操作是什么，都不能重排序，这个规则确保volatile读之后的操作不会被编译器重排序到volatile读之前。
* 当第一个操作是volatile写，第二个操作是volatile读时，不能重排序。

为了实现volatile的内存语义，编译器在生成字节码时，会在指令序列中插入内存屏障来禁止特定理性的处理器重排序。对于编译器来说，发现一个最优布置来最小化插入屏障的总数几乎是不可能的。因此Java内存模型采取保守策略。

* 在每个volatile写操作前面插入一个StoreStore屏障。
* 在每个volatile写操作后面插入一个StoreLoad屏障。
* 在每个volatile读操作后面插入一个LoadLoad屏障。
* 在每个volatile读操作后面插入一个LoadStore屏障。

以volatile写为例

* StoreStore屏障可以保证在volatile写之前，其前面的所有普通写操作已经对任意处理器可见了。这是因为StoreStore屏障将保障上面所有普通写在volatile写之前刷新到主内存。
* Volatile写后面的StoreLoad屏障的多用是避免volatile写与后面可能有的volatile读写操作重排序。

因为编译器通常无法准确判断在一个volatile写的后面是否需要插入一个StoreLoad屏障，比如程序直接返回。为了保证正确实现volatile的语义，此处采取了保守策略，在每个volatile写的后面，或者在每个volatile读的前民插入一个StoreLoad屏障。从整体执行效率的角度考虑，Java内存模型最终选择了在每个volatile写的后面插入一个StoreLoad屏障。因为volatile写读的内存语义常见的一个使用模式是，一个写线程写volatile变量，多个读线程读取同一个volatile变量，当读线程数量大超过写线程数量是，选择在volatile写之后插入StoreLoad屏障将带来可可观的执行效率的提升。

以volatile读为例

* LoadLoad屏障用来禁止处理器把上面的volatile读与下面的普通读重排序。
* LoadStore屏障用来禁止处理器把上面的volatile读与下面的普通写重排序。

{{< notice "info" >}}
为了提供一种比锁更轻量级的线程之间通信的机制，JSR-133增强了volatile的内存语义，严格限制编译器和处理器对volatile变量与普通变量的重排序,确保volatile的写-读和锁的释放获取具有相同的内存语义。 {{<
/notice >}}

volatile仅仅保证对单个volatile变量的读写具有原子性，而锁的互斥执行的特性可以确保对整个临界区代码的执行具有原子性。在功能上，锁比volatile更强大，在可伸缩性和执行性能上，volatile更有优势。

### 锁的内存语义 <div id="lock-memory-semantic" />

锁是Java并发编程中最重要的同步机制，锁除了让临界区互斥执行外，还可以让释放锁的线程向获取同一个锁的线程发送消息。

#### 锁的释放与获取 <div id="lock-release-and-accquire" />

当线程释放锁时，Java内存模型会把该线程对应的本地内存中的共享变量刷新到主内存中。

当线程获取锁时，Java内存模型会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须从主存中读取共享变量。

对比锁回获取-释放的内存语义与volatile读-写的内存语义可以看出，锁获取与volatile读有相同的内存语义，锁释放与volatile写有相同的内存语义。

总而言之：

* 线程A释放一个锁，实质上是线程A向接下来将要获取这个锁的某个线程发出了（线程A对共享变量所做修改）的消息
* 线程B获取一个锁，实质上是线程B接收了之前某个线程发出的（在释放这个锁之前对共享变量所做修改）的消息
* 线程A释放锁，随后线程B获取锁，这个过程实质上是线程A通过主存想线程B发送消息。

#### 锁内存语义的实现 <div id="lock-memory-semantic-impl" />

让我们看看下面这一段代码

```java
import java.util.concurrent.locks.ReentrantLock;

public class Solution {
    int a = 0;
    volatile boolean flag = false;

    ReentrantLock lock = new ReentrantLock();

    public void write() {
        try {
            lock.lock();

            a = 1;    // 1
        } finally {
            lock.unlock();
        }
    }

    public void read() {
        try {
            lock.lock();

            int i = a * a;    // 2
        } finally {
            lock.unlock();
        }
    }
}
```

此处我们使用了一个ReentrantLock可重入锁实现来对我们的代码做了同步控制。我们调用lock方法获取锁，调用unlock方法释放锁。

ReentrantLock的实现依赖于Java同步器框架AbstractQueuedSynchronizer，以下简称AQS。AQS使用一个名为state的整型volatile变量来维护同步状态。这个volatile变量是ReentrantLock内存语义实现的关键。

#### ReentrantLock - 公平锁 <div id="fair-reentrant-lock" />

当使用公平锁时，整个加锁过程如下

* --> ReentrantLock:lock()
    * --> FairSync:lock()
        * --> AbstractQueuedSynchronizer:acquire(int args)
            * --> ReentrantLock:tryAcquire(int acquires)

第四步真正开始加锁

```java
public class ReentrantLock {
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState(); // 获取锁的开始，首先读volatile变量state 
        if (c == 0) {
            if (isFirst(current) && compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        } else if (current == getExclusiveOwnerThread()) {
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

从源代码中可以看出，加锁方法先读取state的值

* 如果state值为0，说明这个锁没有被获取过，然后使用compareAndSet操作将这个值加上需要获取的数量，如果set成功那么加锁成功，那么加锁成功，设置当前线程为锁的拥有者并返回成功。
*

如果state值不为0，说明这个锁目前已经被线程持有，此时我们对比持有锁的线程和当前线程是不是同一个线程，如果不是，那么加锁失败，如果是，在未超过加锁上限Integer.MAX_VALUE的情况下，同一个线程可以再次加锁成功，这也就是**
可重入**的概念。

当使用公平锁时，整个解锁过程如下

* --> ReentrantLock:unlock()
    * --> AbstractQueuedSynchronizer:release(int arg)
        * --> Sync:tryRelease(int releases)

在第三步真正开始释放锁，下面是该方法的源代码

```java
public class Sync {
    protected final boolean tryRelease(int releases) {
        int c = getState() - releases;

        if (Thread.currentThread() != getExclusiveOwnerThread()) {
            throw new IllegalMonitorStateException();
        }

        boolean free = false;

        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c); // 释放锁的最后，写volatile变量state 

        return free;
    }
}
```

可以看出，在释放锁时，我们先判断当前线程是不是这个锁的拥有者，如果不是，无权进行锁释放，如果是，那么我们将需要释放的数量从state上减去。如果state数量不为零，此时意味着当前锁被当前线程多次重入，需要继续进行锁释放；如果state数量为零，说明当前锁已经被完全释放。

需要注意的是，公平锁在释放锁的最后写volatile变量，在获取锁时首先读和这个volatile变量。根据volatile的happens-before规则，释放锁的线程在写volatile变量之前可见的共享变量，在获取锁的线程读取同一个volatile变量后将立即变得对获取锁的线程可见。

#### ReentrantLock - 非公平锁 <div id="non-fair-reentrant-lock" />

非公平锁的释放和公平锁完全一样，所以这里仅仅分析非公平锁的获取，使用非公平锁时，加锁过程如下

* --> ReentrantLock:lock()
    * --> NonfairSync:lock()
        * --> AbstractQueuedSynchronizer:compareAndSetState(int args)

在第三步真正开始加锁，让我们看看源代码

```java
public class AbstractQueuedSynchronizer {
    protected final boolean compareAndSetState(int expect, int update) {
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
}
```
该方法以原子操作的方式更新state变量，本文把Java的compareAndSet方法调用简称为CAS。CAS方法的作用是，如果当前状态值等于预期值，则以原子方式将同步状态设置为给定的更新值。此操作具有volatile读和写的内存语义。

让我们从编译器和处理器的角度来分析，CAS如何同时具有volatile读和volatile写的内存语义。

前文我们提到过，编译器不会对volatile读与volatile读之后的任意内存操作重排序；编译器不会对volatile写与volatile写前面的任意内存操作重排序。组合这两个条件，意味着为了同时实现volatile读和volatile写的内存语义，编译器不能对CAS与CAS前面和后面的任意内存操作重排序。

让我们看一下Intel x86处理器上的源代码实现
```txt
inline jint Atomic::cmpxchg (jint exchange_value, volatile jint* dest, jint compare_value) { 
    // alternative for InterlockedCompareExchange 
    int mp = os::is_MP(); 
    __asm { 
        mov edx, dest 
        mov ecx, exchange_value 
        mov eax, compare_value 
        LOCK_IF_MP(mp) 
        cmpxchg dword ptr [edx], ecx 
    } 
}
```
我们看`LOCK_IF_MP`，MP是指Multiple Processor，表示是否是多处理器，如果是多处理器，程序会为`cmpxchg`指令加上lock前缀，在Pentium以及之前的处理器中，其会花费昂贵的代价锁定总线，以保证只有当前处理器能够读写主存；如果程序是在单处理器上运行，那就省略lock前缀（单处理器或维护处理器内的顺序一致性）。

intel的手册对lock前缀的说明如下
* 确保对内存的读-改-写操作原子执行
* 禁止该指令与之前和之后的读写指令重排序
* 把写缓冲区中的所有数据刷新的内存中

这里的第二点和第三点足以同时实现volatile读和volatile写的内存语义。

总结
* 公平锁和非公平锁释放时，最后都要写一个volatile变量state
* 公平锁获取是首先会去读volatile变量
* 非公平锁获取时，首先会使用CAS更新volatile变量，这个操作同时具有volatile读和volatile写的内存语义。

锁释放-获取的内存语义的实现至少有两种方式。利用volatile变量的写-读所具有的内存语义；利用CAS所附带的volatile读和写的内存语义。

## Concurrent包的实现 <div id="concurrent-module-impl" />
由于Java的CAS同时具有volatile读和volatile写的内存语义，因此Java线程之间的通信有四种实现方式：
* A线程写volatile变量，随后B线程读这个volatile变量
* A线程写volatile变量，随后B线程用CAS更新这个volatile变量
* A线程用CAS更新一个volatile变量，随后B线程用CAS更新的这个变量
* A线程用CAS更新一个变量，随后B线程读这个volatile变量

Java的CAS会使用现代处理器上提供的高效机器级别的原子指令，这些原子指令以原子方式对内存进读-改-写操作，这是在多处理器中实现同步的关键。同事volatile变量的读写和CAS可以实现线程之间的通信。把这些特性整合在一起，就形成了这个concurrent包得以实现的基石。

如果我们仔细分析concurrent包的源代码实现，会发现一个通用化的实现模式：
* 首先，声明共享变量为volatile。
* 然后，使用CAS的原子条件更新来实现线程之间的同步。
* 同时，配合以volatile的读写和CAS所具有的volatile读和写的内存语义来实现线程的通信。

## final域的内存语义 <div id="final-memory-semantic" />
与前面介绍的锁和volatile相比，对final域的读写更像是普通的变量访问，下面介绍final的内存语义。

### final域的重排序规则 <div id="final-reordering-rule" />
对于final域，编译器和处理器需要遵守两个重排序规则。
* 在构造函数内对一个final域的写入，与随后把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。
* 初次读一个包含final域的对象的引用，与随后初次读这个final域，这两个操作之间不能重排序。

#### 写final域的重排序规则 <div id="write-final-reordering-rule" />
写final域的重排序规则禁止吧final域的写重排序到构造函数之外。
* Java内存模型禁止编译器吧final域的写重排序到构造函数之外。
* 编译器会在final域的写之后，构造函数return之前，插入一个StoreStore屏障。这个屏障会禁止处理器吧final域的写重排序到构造函数之外。

让我们看一段代码
```java
public class FinalExample {
    int i;    //普通变量
    final int j;    //final变量
    static FinalExample singleton;
    
    public FinalExample() {    //构造函数
        i = 1;    //写普通域
        j = 2;    //写final域
    }
    
    public static void write() {    //写线程A执行
        obj = new FinalExample();
    }
    
    public static void read() {    //读线程B执行
        FinalExample instance = singleton;    //读对象引用
        
        int a = instance.i;    //读普通域
        int b = instance.j;    //读final域
    }
}
```

我们假设一个线程A执行write方法，线程B执行read方法。我们通过两个线程的交互来说明这两个规则。

我们来思考一种可能的重排序情况，由于没有任何保证，普通域的写可以被重排序到构造函数之外，所以线程B在读取普通域i之前，可能线程A还没有完成普通域i的初始化，线程B错误的读取了初始化之前的值，这里就是对象内存分配时对内存空间置的零值。而对于final域的写操作，被上面提到的规则限定在了构造函数之内，那么线程B就能够正确读取final域初始化后的值。

{{< notice "info" >}}
注意，此处其实我们有一个前置条件，那就是线程B的读对象引用和读对象成员域之间没有重排序。
{{< /notice >}}

#### 读final域的重排序规则 <div id="read-final-reordering-rule" />
读final域的重排序规则是，Java内存模型禁止处理器重排序初次读对象引用与初次读该对象包含的final域这两个操作。编译器会再读final域之前插入一个LoadLoad屏障。

* 初次读对象引用与初次读对象包含的final域，这两个操作之间存在间接依赖关系。由于编译器遵守间接依赖关系，因此编译器不会重排序这两个操作。
* 大多数处理器也会遵守间接依赖，也不会重排序这两个操作，但是有少数处理器允许对存在间接依赖关系的操作做重排序。

让我们回顾上面的例子，我们之前有一个假设，那就是线程B的读对象引用和读对象成员域之间没有重排序。让我们先回顾一下B线程执行的流程
* 初次读取引用变量singleton
* 初次读取引用变量指向的对象的普通域i
* 初次读取引用变量指向的对象的final域j

假设处理器进行了重排序，读成员域，包括普通域i和final域j被重排序到读对象引用之前。读普通域时，该域还没有被线程A写入，我们错误地读取了零值。读final域时，由于重排序规则将读对象的final域限定在读对象引用之后，此时final域已经被线程A正确地初始化过了，读取结果正确。

读final域的重排序规则可以确保，在读一个对象的final域之前，一定会先读包含这个final域的对象的引用，知道这个引用不为null，其final域一定被正确初始化了。

#### final域为引用类型 <div id="final-reference" />
让我们来看一下另一段示例代码，当我们的final域不再是基础数据类型而是引用类型时，会是什么效果
```java
public class FinalReferenceFieldExample {
    final int[] array;    //final域是引用类型
    static FinalReferenceFieldExample singleton;
    
    public FinalReferenceFieldExample() {    //构造函数
        array = new int[1];    // 1
        array[0] = 1;    // 2
    }
    
    public static void writeOne() {    //写线程A执行
        singleton = new FinalReferenceFieldExample();    // 3
    }
    
    public static void writeTwo() {    //写线程B执行
        singleton.array[0] = 2;    // 4
    }
    
    public static void read() {    // 读线程C执行
        if(singleton != null) {    // 5
            int value = singleton.array[0];    // 6
        }
    }
}
```

在上面这段代码中，final域是一个引用，对于引用类型，我们还有一个重排序规则：

在构造函数内对一个final引用的对象的成员域的写入与随后在构造函数外把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。

我们过一下整个执行流程，操作1是对final引用的写入，操作2是对这final引用所指对象的成员域的写入，这两个操作发生在构造函数内。操作3是吧被构造对象的引用赋值给某个引用变量。操作1和操作3是不能重排序的，操作2和操作3也不能被重排序。

但是我们再看操作4和操作6，虽然操作4和操作2同样都是对final引用所指向的对象的成员域进行写入，由于操作4不是在构造函数之内，所以上面的重排序规则不在有效，此时操作4和操作6之间存在数据竞争，我们就需要使用volatile或者锁来确保内存可见性。

写final域的重排序规则可以确保：在引用变量为任意线程可见之前，该引用变量指向的对象的final域已经在构造函数中被正确初始化过了。其实，要得到这个效果，还需要一个保证：在构造函数内部，不能让这个被构造对象的引用为其他线程所见，也就是对 在构造函数返回前，被构造对象的引用不能为其他线程所见，因为此 时的final域可能还没有被初始化。

## happens-before <div id="happens-before-detail" />
### Java内存模型的设计 <div id="java-memory-module-design" />
前面提到过Java内存模型是一个抽象的概念，我们探讨了顺序一致性模型以及如何在多处理器的情况下通过内存可见性控制来达到顺序一致性。Java内存模型设计者在设计它时，需要考虑两个关键因素。
* 程序员对内存模型的使用，程序员希望内存模型易于理解，易于编程。程序员希望基于一个强内存模型来编写代码。
* 编译器和处理器对内存模型的实现。编译器和处理器希望内存模型对它们的束缚越少越好，这样它们就可以做尽可能多的优化来提高性能。编译器和处理器希望实现一个弱内存模型。

这两个因素显然是相互矛盾的，所以我们需要找到一个平衡点，一方面需要为程序员提供足够强的内存可见性保证；另一方面，对编译器和处理器的限制要尽可能放松。

让我们回到圆面积计算的代码
```java
public class Solution {
    public void calculate() {
        double pi = 3.14; // A 
        double r = 1.0; // B 
        double area = pi * r * r; // C
    }
}
```

我们之前已经讲过这三个操作之间的happens-before关系
* A happens before B
* C happens before C
* A happens before C

这三个happens-before关系中，第二个和第三个是必要的，但是第一个并不是必要的，因为A，B两个操作的即使调换顺序也不会对结果产生任何影响。

因此，Java内存模型将happens-before要求禁止的重排序分为会改变程序执行结果和不会改变程序执行结果两类。对于会改变程序执行结果的重排序，显然这是需要禁止的。对于不会改变程序执行结果的重排序，Java内存模型不做要求。
* Java内存模型向程序员提供的happens-before规则能够满足程序员的需求，简单易懂且提供了足够强的内存可见性保证。
* Java内存模型对编译器和处理器的束缚尽可能的少。只要不改变程序执行结果，编译器和处理器想怎么优化都可以。如果一个锁只会被单个线程访问，那么这个锁就可以消除；如果一个volatile变量只会被一个线程访问，那么这个变量就会被当做普通变量来对待。这样既不会改变程序执行结果，也能增加效率。

### happens-before的定义 <div id="happens-before-defination" />
happens-before最早被用来定义分布式系统中事件之间的偏序关系。Java内存模型则使用happens-before关系向程序员提供跨线程的内存可见性保证，如果A线程的写操作a与B线程的读操作b之间存在happens-before关系，尽管a操作和b操作在不同线程中执行，Java内存模型仍然能够向程序员保证a操作将对b操作可见。

* 如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。
* 两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须按照happens-before关系指定的顺序来执行。如果重排序之后的执行结果和重排序之前的执行结果一直，

上面第一条规则是Java内存模型对程序员做出的承诺。从程序员的角度来说，如果A happens-before B，那么Java内存模型将向程序员保证，A操作的结果对B操作可见且A的执行顺序在B之前。

上面第二条是Java内存模型对编译器和处理器重排序的约束原则。Java内存模型遵循一个基本原则，只要不改变程序的执行结果，编译器和处理器怎么优都可以。Java内存模型之所以这么做，是因为程序员对于两个操作是否真的被重排序其实并不关心，他们关心的只是程序执行时的语义不能被改变。因此，happens-before关系本质上和as-if-serial语义是一回事。

* as-if-serial语义保证单线程内程序的执行结果不被改变，happens-before关系保证正确同步的多线程程序的执行结果不被改变。
* as-if-serial语义给编写单线程程序的程序员创造了一个幻境，单线程程序是按照程序的顺序来执行的；happens-before关系给编写正确同步的多线程程序的程序员创造了一个幻境：正确同步的多线程程序是按照happens-before指定的顺序来执行的。

as-if-serial和happens-before这么做的目的，都是为了在不改变程序执行结果的前提下，尽可能地提高程序的执行效率。

### happens-before规则 <div id="all-happens-before-rules" />
* 程序顺序规则 - 一个线程中的每一个操作，happens-before于该线程中的任意后续操作。
* 监视器锁规则 - 对一个锁的解锁，happens-before与随后对这个锁的加锁。
* volatile变量规则 - 对一个volatile域的写happens-before于任意后续对这个volatile域的读。
* 传递性 - 如果A happens-before B，且B happens-before C那么，A happens-before C。
* 线程start规则，如果A线程作为父线程启动线程B，那么A线程start操作happens-before与线程中的任意操作。
* 线程join规则，如果线程A操作ThreadB.join()并成功返回，那么线程B中任意操作happens-before与线程A中ThreadB.join()操作之后的任意操作。

## 双重检查锁 & 延迟初始化 <div id="lazy-initialization-and-double-check-lock" />
当我们实现单例模式时，我们会用到的一个经典的模式就是双重检查锁。我们通过对对象进行延迟初始化来降低初始化类和创建对象的开销。但是这其实是一个错误的做法，接下来我们会对其进行分析并改进。

让我们来看一段代码，我们使用非常简单的方式实现一个延迟初始化。
```java
public class UnsafeLazyInitialization {
    private static Instance instance;

    public static Instance getInstance() {
        if (instance == null) { // 1：A线程执行 
            instance = new Instance(); // 2：B线程执行 
        }
        
        return instance;
    }
}
```

这一段程序存在两个问题，第一，如果两个程序同时执行到第一个操作，此时由于instance尚未创建，所以两个线程看到的`instance == null`均为true，我们会创建两个对象；第二，如果A执行到第二个操作，然后B执行到第一个操作，此时B线程看到`instance != null`然后将instance返回，但是此时instance可能尚未被完全初始化。此处是因为整个对象创建的步骤其实分为三部，分配内存空间，对内存空间置零，将引用指向这一块儿内存地址；第二个和第三个操作是可能被重排序的。那么我们就虽然instance已经不为null，但是其普通域可能尚未被正确初始化。

我们可以对上面的程序做一个简单的处理，那就是使用synchronized关键字对代码进行同步。让我们看代码
```java
public class UnsafeLazyInitialization {
    private static Instance instance;

    public static synchronized Instance getInstance() {
        if (instance == null) { // 1：A线程执行 
            instance = new Instance(); // 2：B线程执行 
        }
        
        return instance;
    }
}
```
我们简单调整了代码，现在这段代码被正确同步了。但是我们想一下，第一次延迟初始化的确不会出问题了，但是之后我们每一次调用getInstance方法我们都需要进行加锁，这会造成巨大的性能开销。

正因为如此，人们想到了一个聪明的解决方案。双重检查锁定。人们希望在实现功能的同时通过双重检查锁定来避免使用同步锁所带来的开销。让我们看下代码
```java
public class DoubleCheckedLocking {
    private static Instance instance;

    public static Instance getInstance() {
        if (instance == null) { // 1：第一次检查
            synchronized(DoubleCheckedLocking.class) {    //加锁
                if(instance == null) {     // 第二次检查
                    instance = new Instance();    //初始化，问题的根源出在这里
                }
            }
        }
        
        return instance;
    }
}
```

我们看下代码，如果第一次检查instance不为null，那么就不需要执行下面的加锁和初始化操作。我们大幅降低了同步锁所带来的性能开销。看上去似乎两全其美。但是这其实是一个错误的优化。

在执行第一次检查时，虽然instance不为null，但是其属性域可能还没有完成初始化。问题主要出在对象初始化的代码`instance = new Instance();`，像上面说的，这一行代码实际上等价于下面三个操作
```java
public class DoubleCheckedLocking {
    private static Instance instance;

    public static Instance getInstance() {
        //...
        memory = allocate();    //1 为对象分配内存空间
        ctorInstance(memory);   //2 初始化对象
        instance = memory;      //3 将instance引用指向刚刚分配的内存地址
        //...
    }
}
```

操作2和操作3可能被重排序。此时代码变成下面的顺序

```java
public class DoubleCheckedLocking {
    private static Instance instance;

    public static Instance getInstance() {
        //...
        memory = allocate();    //1 为对象分配内存空间
        instance = memory;      //3 将instance引用指向刚刚分配的内存地址
        
        //注意此时，对象还没有被正确初始化，但是instance此时的确 != null
        
        ctorInstance(memory);   //2 初始化对象
        //...
    }
}
```

整个时间线看起来可能向下面的表格展示的一样
|时间|线程A|线程B|
|---|----—|-—---|
|t1|A:分配对象的内存空间||
|t2|A:设置instance指向刚刚分配的内存||
|t3||B:判断instance是否为空|
|t4||B:由于instance不为null，线程B将访问instance指向的对象|
|t5|A:初始化对象||
|t6|A:访问instance引用的对象||

知道了问题的根源了，那么我们就有两种解决方案
* 不允许操作2，3重排序。
* 允许操作2，3重排序，但不允许其他线程看到这个重排序。

### 基于volatile的解决方案 <div id="lazy-initialization-using-volatile" />
使用volatile禁用重排序，所以我们只需要做一点微小的修改就可以保证正确的结果了，那就是把instance调整为volatile变量。
```java
public class DoubleCheckedLocking {
    private static volatile Instance instance;

    public static Instance getInstance() {
        if (instance == null) { // 1：第一次检查
            synchronized(DoubleCheckedLocking.class) {    //加锁
                if(instance == null) {     // 第二次检查
                    instance = new Instance();    //初始化，问题的根源出在这里
                }
            }
        }
        
        return instance;
    }
}
```

### 基于类初始化的解决方案 <div id="lazy-initialization-using-class-initialization" />
JVM在类的初始化阶段，会获取一个锁，这个锁可以同步多个线程对同一个类的初始化。所以我们可以通过静态内部holder类来实现延迟初始化。
```java
public class DoubleCheckedLocking {
    private static class Holder {
        private static Instance instance = new Instance();
    }
    
    private static volatile Instance instance;

    public static Instance getInstance() {
        return Holder.instance;    //这里初始化Holder类，同时初始化instance
    }
}
```

初始化一个类，包括这个类的初始化和静态字段的初始化，在首次发生下列任意情况时，一个类或者接口类型T将被立即初始化。
* T是一个类，而且一个T类型的实例被创建。
* T是一个类，而且T中声明的静态方法被调用。
* T中声明的一个静态变量被赋值。
* T中声明的一个静态变量被使用，而且这个字段不是一个常量字段。
* T是一个顶级类，而且一个断言语句嵌套在T内部被执行。

## 总结 <div id="sumarry" />
### 处理器的内存模型 <div id="processor-memory-modal" />
顺序一致性内存模型是一个理论参考模型，Java内存模型和处理器内存模型在设计时通常会以顺序一致性内存模型为参照。在设计时，Java内存模型和处理器内存模型会对顺序一致性模型做一些放松，因为如果完全按照顺序一致性模型来实现处理器和Java内存模型，那么很多的处理器和编译器优化都需要被禁止，这对执行性能会有很大的影响。

所有处理器内存模型都允许写-读重排序，因为他们都是用了写缓冲区，写通比写回性能要好很多。写缓冲区可能导致写-读操作重排序，同事，我们可以看到这些处理器内存模型都允许更早读取到当前处理器的写，原因同样是因为写缓冲区。由于写缓冲区仅对当前处理器可见，这个特性导致当前处理器可以比其他处理器看到临时保存在写缓冲区中的写。

越是追求性能的处理器，内存模型设计就会越弱，因为这些处理器希望内存模型对它们的束缚越少越好，这样他们就可以做尽可能多的优化来提高性能。但是程序员希望一个较强的内存模型，所以Java内存模型设计时，Java编译器在生成字节码是，会再执行执行序列的适当位置插入内存屏障来限制处理器的重排序。

### 各种内存模型之间的关系 <div id="relationship-between-different-memory-model" />
Java内存模型是一个语言级的内存模型，处理器内存模型是一个硬件级的内存模型，顺序一致性模型是一个理论参考模型。

{{< image src="images/java-concurrent-programing/java-memory-model/relationships-of-different-memory-model.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

### Java内存模型的内存可见性保证 <div id="jmm-and-memory-visibility" />
* 单线程程序，单线程程序不会出现内存可见性问题，编译器，runtime，处理器会共同确保单线程程序的执行结果与该程序在顺序一致性模型中的执行结果相同。
* 正确同步的多线程程序。正确同步的多线程程序具有顺序一致性，即程序的执行结果与该程序在顺序一致性模型中的执行结果相同。这是Java内存模型关注的重点，Java内存模型通过限制编译器和处理器的重排序来为程序员提供内存可见性保证。
* 未同步、未正确同步的多线程程序，Java内存模型为它们提供了最小的安全性保障。线程执行是读取到的值，要么是之前某个线程写入的值，要么是默认值，也就是内存分配后初始化的零值。

