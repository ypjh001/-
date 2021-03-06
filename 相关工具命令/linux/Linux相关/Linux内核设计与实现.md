# 概述

## 目的

对内核各个核心子系统有个整体把握，它们提供什么样的服务，为什么要提供这样的服务，如何实现的。

调试技术，编码风格，注意事项。

本人侧重实践，有需要的话 看完此书后再看《Linux Device Drivers》

译者的学习网站 www.kerneltravel.net

我应该不会全看，只看调重点章节详细阅读。我的主力语言是Java，对内核有个大致的了解，理解部分内容就好了。

## 问题

不只是内核开发，其他开发也是，体系越来越庞大。出现了局部断层。

## 一些疑问&参考回答

### OS和内核的区别

- OS > 内核，kernel是OS的最基本部分，两者是包含关系，kernel是OS的核心
- 内核用于管理系统资源，例如提供对软件层面的抽象（例如对进程、文件系统、同步、内存、网络协议等对象的操作和权限控制），和对硬件访问的抽象（例如磁盘，显示，网络接口卡（NIC））；操作系统，在内核的基础上有延伸，包括了提供基础服务的系统组件。
- 一个内核不是一套完整的操作系统，现在默认linux为linux内核，这是由于历史源因造成的，实际上一般来讲，一个Linux发行版本出来包括Linux内核之外，还包含大量的软件（套件），比如软件开发工具，数据库，Web服务器（例如Apache），X Window，桌面环境（比如GNOME和KDE），办公套件（比如OpenOffice、org）等等。

----

# Linux内核简介

## Unix历史

Unix 系统调用少！只开放必要的调用！几乎把系统中的所有东西都当做文件对待，处理方式统一（秒啊！）。

## Linux历史

开源，它人可修改完善，是它成功的一大要素。（想想Java和C#，开源是Java胜出的一大因素？）

和Unix一样，保证了应用程序编程接口的一致。（没有直接使用Unix源码哦！）

## Linux系统的基础

- 内核
- C库
- 工具集和系统的基本工具

本书说Linux主要是值内核，特殊指代的话会进行说明。

## 单内核与微内核

| 内核   | 原理                                                         | 优势                                                         | 劣势                             |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------- |
| 单内核 | 整个内核都在一个大内核地址空间上运行。                       | 1.简单<br />2.高效。内核间的调用没什么开销                   | 一个功能的崩溃会导致系统无法使用 |
| 微内核 | 内核按功能被划分成各个独立的过程。每个过程独立的运行在自己的地址空间上。 | 1.安全，各个服务直接独立运行，互不影响<br />2.方便拓展，耦合性低 | 服务之间的通信耗时               |

Linux的内核虽然是基于单内核的，但是经过这么多年的发展，也具备微内核的一些特征。（体现了Linux实用至上的原则）

**主要有以下特征：**

1. 支持动态加载内核模块
2. 支持对称多处理（SMP）
3. 内核可以抢占（preemptive），允许内核运行的任务有优先执行的能力
4. 不区分线程和进程，所有的进程都一样----只不过是其中的一些共享资源而已。（这让我想起了设计模式，多数设计模式本质就是多态，提高代码复用性和可拓展性，不必分的太明确，不过分的明确，命名上可以讲究一下，见名知意。）

**对称多处理的解释**

**对称多处理**（英语：Symmetric multiprocessing，缩写为 SMP），也译为**均衡多处理**、**对称性多重处理**、**对称多处理机**[[1\]](https://zh.wikipedia.org/wiki/对称多处理#cite_note-1)，是一种[多处理器](https://zh.wikipedia.org/wiki/多處理器)的电脑硬件架构，在对称多处理架构下，每个处理器的地位都是平等的，对资源的使用权限相同。现代多数的[多处理器](https://zh.wikipedia.org/wiki/多處理器)系统，都采用对称多处理架构，也被称为对称多处理系统（Symmetric multiprocessing system）。在这个系统中，拥有超过一个以上的处理器，这些处理器都连接到同一个共享的主存上，并由单一操作系统来控制。在[多核心处理器](https://zh.wikipedia.org/wiki/多核心處理器)的例子中，对称多处理架构，将每一个核心都当成是独立的处理器。

在对称多处理系统上，在操作系统的支持下，无论行程是处于用户空间，或是核心空间，都可以分配到任何一个处理器上运行。因此，进程可以在不同的处理器间移动，达到负载平衡，使系统的效率提升。

## **内核版本号**

内核的版本号主要有四个数组成。比如版本号：2.6.26.1 其中，

2 - 主版本号

6 - 从版本号或副版本号

26 - 修订版本号

1 - 稳定版本号

副版本号表示这个版本是稳定版（**偶数**）还是开发版（**奇数**），上面例子中的版本号是稳定版。

稳定的版本可用于企业级环境。

修订版本号的升级包括BUG修正，新的驱动以及新的特性的追加。

稳定版本号主要是一些关键性BUG的修改。

# 内核开发

获取内核源码，编译。建议用虚拟机搞，毕竟怕把系统搞崩溃了。Ubuntu系统的部分内核代码位于 `sys` 目录下。

## 下载内核代码

```shell
apt-cache search linux-source
apt-get install linux-source-5.4.0
tar -jxv -f linux-source-5.4.0.tar.bz2 -C /home/
```

## 编译内核

没实践过，就不实践了，有空再说哈哈哈。

### **获取内核源码**

内核是开源的，所有获取源码特别方便，参照以下的网址，可以通过git或者直接下载压缩好的源码包。

[http://www.kernel.org](http://www.kernel.org/)

### **内核源码的结构**

| **目录**      | **说明**                            |
| ------------- | ----------------------------------- |
| arch          | 特定体系结构的代码                  |
| block         | 块设备I/O层                         |
| crypo         | 加密API                             |
| Documentation | 内核源码文档                        |
| drivers       | 设备驱动程序                        |
| firmware      | 使用某些驱动程序而需要的设备固件    |
| fs            | VFS和各种文件系统                   |
| include       | 内核头文件                          |
| init          | 内核引导和初始化                    |
| ipc           | 进程间通信代码                      |
| kernel        | 像调度程序这样的核心子系统          |
| lib           | 同样内核函数                        |
| mm            | 内存管理子系统和VM                  |
| net           | 网络子系统                          |
| samples       | 示例，示范代码                      |
| scripts       | 编译内核所用的脚本                  |
| security      | Linux 安全模块                      |
| sound         | 语音子系统                          |
| usr           | 早期用户空间代码（所谓的initramfs） |
| tools         | 在Linux开发中有用的工具             |
| virt          | 虚拟化基础结构                      |

## 内核开发的特点

### 特点概述

常用准则被颠覆。

- 内核编程不能访问 C 库和标准的 C 头文件
- 内核编程时必须使用 GNU C
- 内核编程缺乏内存保护极值 
- 难以执行浮点数运算（？？？）
- 内核给每个进程只有一个很小的定长堆栈
- 内核支持异步中断、抢占和SMP，必须时刻注意同步和并发
- 要考虑可移植性

### 无libc库或标准头文件

对内核来说，C库甚至是C库的一个子集，都太大效率太低了。

不过大部分常用的C函数，内核中都有对应的实现

### 内核编译推荐

使用GNU C，推荐用gcc 4.4或以后的版本来编译内核

因为使用GNU C，所有内核中常使用GNU C中的一些扩展：

### 内核代码中C的扩展

#### 内联函数

内联函数在编译时会在它被调用的地方展开，减少了函数调用和返回的开销（OOP语言这快非常频繁），性能较好。但是，频繁的使用内联函数也会使代码变长，从而在运行时占用更多的内存。

所以内联函数使用时最好要满足以下几点：函数较小，会被反复调用，对程序的时间要求比较严格。

内联函数示例：static **inline** void sample();

内联函数在使用前要定义好，以便展开。

推荐优先使用内联函数，而非宏定义。

#### 内联汇编

内联汇编用于偏近底层或对执行时间严格要求的地方。示例如下：

```c
unsigned int low, high;
asm volatile("rdtsc" : "=a" (low), "=d" (high));
/* low 和 high 分别包含64位时间戳的低32位和高32位 时间要求严格的用汇编写*/
```

#### 分支声明

如果能事先判断一个if语句时经常为真还是经常为假，那么可以用unlikely和likely来优化这段判断的代码。（<span style="color:red">`JVM`在这块的优化有不少坑，都是来源于指令重排序</span>）

```c
/* 如果error在绝大多数情况下为0(假) */
if (unlikely(error)) {
    /* ... */
}
/* 如果success在绝大多数情况下不为0(真) */
if (likely(success)) {
    /* ... */
}
```

#### 没有内存保护

内核是最低层的程序，如果内核访问非法内存，整个系统都会挂掉！！所以内核开发的风险比用户程序开发的风险要大。

内核中的内存是不分页的，每用一个字节的内存，物理内存就少一个字节。所以内核中使用内存一定要谨慎。（**我又和OS混了。OS里不是说内存是分页或分段的吗**。）

#### 不使用浮点数

内核不能完美的支持浮点操作，使用浮点数时，需要人工保存和恢复浮点寄存器及其他一些繁琐的操作。

#### 内核栈容积小且固定

内核栈的大小由编译内核时决定的，对于不用的体系结构，内核栈的大小虽然不一样，但都是固定的。

查看内核栈大小的方法：

```shell
ulimit -a | grep "stack size"
```

#### 同步和并发

Linux是多用户的操作系统，所以必须处理好同步和并发操作，防止因竞争而出现死锁。

#### 可移植性

Linux内核可用于不同的体系结构，支持多种硬件。所以开发时要时刻注意可移植性，尽量使用体系结构无关的代码。

# 进程管理

进程是所有操作系统的核心概念，同样在linux上也不例外。

- [x] 进程和线程
- [x] 进程的生命周期
- [x] 进程的创建
- [x] 进程的终止

## 进程和线程

## 进程的生命周期

## 进程的创建

## 进程的终止

# 进程调度

- 什么是调度
- 调度实现原理
- Linux上调度实现的方法
- 调度相关的系统调用

## 调度

现在的操作系统都是多任务的，为了能让更多的任务能同时在系统上更好的运行，需要一个管理程序来管理计算机上同时运行的各个任务（也就是进程）。

这个管理程序就是调度程序，它的功能说起来很简单：

- 决定哪些进程运行，哪些进程等待
- 决定每个进程运行多长时间

此外，为了获得更好的用户体验，运行中的进程还可以立即被其他更紧急的进程打断。

总之，调度是一个平衡的过程。一方面，它要保证各个运行的进程能够最大限度的使用CPU(即尽量少的切换进程，进程切换过多，CPU的时间会浪费在切换上)；另一方面，保证各个进程能公平的使用CPU(即防止一个进程长时间独占CPU的情况)。

## 实现原理

前面说过，调度功能就是决定哪个进程运行以及进程运行多长时间。

决定哪个进程运行以及运行多长时间都和进程的优先级有关。为了确定一个进程到底能持续运行多长时间，调度中还引入了时间片的概念。

### 关于进程的优先级

<span style="color:green">进程的优先级有2种度量方法，一种是nice值，一种是实时优先级。</span>

nice 值的范围是 -20～+19，值越大优先级越低，也就是说 nice 值为 -20 的进程优先级最大。

实时优先级的范围是 0～99，与 nice 值的定义相反，实时优先级是值越大优先级越高。

实时进程都是一些对响应时间要求比较高的进程，因此系统中有实时优先级高的进程处于运行队列的话，它们会抢占一般的进程的运行时间。

进程的2种优先级会让人不好理解，到底哪个优先级更优先？一个进程同时有2种优先级怎么办？

其实linux的内核早就有了解决办法。

对于第一个问题，到底哪个优先级更优先？

答案是实时优先级高于nice值，在内核中，实时优先级的范围是 0～MAX_RT_PRIO-1 MAX_RT_PRIO的定义参见 include/linux/sched.h

```shell
1611 #define MAX_USER_RT_PRIO        100
1612 #define MAX_RT_PRIO             MAX_USER_RT_PRIO
```

nice值在内核中的范围是 MAX_RT_PRIO～MAX_RT_PRIO+40 即 MAX_RT_PRIO～MAX_PRIO

```shell
1614 #define MAX_PRIO                (MAX_RT_PRIO + 40)
```

第二个问题，一个进程同时有2种优先级怎么办？

答案很简单，就是一个进程不可能有2个优先级。一个进程有了实时优先级就没有Nice值，有了Nice值就没有实时优先级。

我们可以通过以下命令查看进程的实时优先级和Nice值：(其中RTPRIO是实时优先级，NI是Nice值)

```shell
$ ps -eo state,uid,pid,ppid,rtprio,ni,time,comm
S   UID   PID  PPID RTPRIO  NI     TIME COMMAND
S     0     1     0      -   0 00:00:00 systemd
S     0     2     0      -   0 00:00:00 kthreadd
S     0     3     2      -   0 00:00:00 ksoftirqd/0
S     0     6     2     99   - 00:00:00 migration/0
S     0     7     2     99   - 00:00:00 watchdog/0
S     0     8     2     99   - 00:00:00 migration/1
S     0    10     2      -   0 00:00:00 ksoftirqd/1
S     0    12     2     99   - 00:00:00 watchdog/1
S     0    13     2     99   - 00:00:00 migration/2
S     0    15     2      -   0 00:00:00 ksoftirqd/2
S     0    16     2     99   - 00:00:00 watchdog/2
S     0    17     2     99   - 00:00:00 migration/3
S     0    19     2      -   0 00:00:00 ksoftirqd/3
S     0    20     2     99   - 00:00:00 watchdog/3
S     0    21     2      - -20 00:00:00 cpuset
S     0    22     2      - -20 00:00:00 khelper
```

### 关于时间片

有了优先级，可以决定谁先运行了。但是对于调度程序来说，并不是运行一次就结束了，还必须知道间隔多久进行下次调度。

于是就有了时间片的概念。时间片是一个数值，表示一个进程被抢占前能持续运行的时间。

也可以认为是进程在下次调度发生前运行的时间(除非进程主动放弃CPU，或者有实时进程来抢占CPU)。

时间片的大小设置并不简单，设大了，系统响应变慢(调度周期长)；设小了，进程频繁切换带来的处理器消耗。默认的时间片一般是10ms

### 调度实现原理

基于优先级和时间片的调度实现；下面举个直观的例子来说明：

假设系统中只有3个进程ProcessA(NI=+10)，ProcessB(NI=0)，ProcessC(NI=-10)，NI表示进程的nice值，时间片=10ms

- 调度前，把进程优先级按一定的权重映射成时间片(这里假设优先级高一级相当于多5msCPU时间)。假设ProcessA分配了一个时间片10ms，那么ProcessB的优先级比ProcessA高10(nice值越小优先级越高)，ProcessB应该分配10*5+10=60ms，以此类推，ProcessC分配20*5+10=110ms
- 开始调度时，优先调度分配CPU时间多的进程。由于ProcessA(10ms),ProcessB(60ms),ProcessC(110ms)。显然先调度ProcessC
- 10ms(一个时间片)后，再次调度时，ProcessA(10ms),ProcessB(60ms),ProcessC(100ms)。ProcessC刚运行了10ms，所以变成100ms。此时仍然先调度ProcessC
- 再调度4次后(4个时间片)，ProcessA(10ms),ProcessB(60ms),ProcessC(60ms)。此时ProcessB和ProcessC的CPU时间一样，这时得看ProcessB和ProcessC谁在CPU运行队列的前面，假设ProcessB在前面，则调度ProcessB
- 10ms(一个时间片)后，ProcessA(10ms),ProcessB(50ms),ProcessC(60ms)。再次调度ProcessC
- ProcessB和ProcessC交替运行，直至ProcessA(10ms),ProcessB(10ms),ProcessC(10ms)。这时得看ProcessA，ProcessB，ProcessC谁在CPU运行队列的前面就先调度谁。这里假设调度ProcessA
- 10ms(一个时间片)后，ProcessA(时间片用完后退出),ProcessB(10ms),ProcessC(10ms)。
- 再过2个时间片，ProcessB和ProcessC也运行完退出。

这个例子很简单，主要是为了说明调度的原理，实际的调度算法虽然不会这么简单，但是基本的实现原理也是类似的：

1）确定每个进程能占用多少CPU时间(这里确定CPU时间的算法有很多，根据不同的需求会不一样)

2）占用CPU时间多的先运行

3）运行完后，扣除运行进程的CPU时间，再回到 1）

#  系统调用

```shell
# ubuntu下查看有那些系统调用
cat /usr/src/linux-headers-5.4.0-42/arch/alpha/include/asm/unistd.h
```

讲了下系统调用的规则，有点，怎么看有那些系统调用，如何自定义系统调用。

# 内核数据结构

## 主要内容

`ubuntu`下的文件位置

- 链表----在`/usr/src/linux-headers-5.4.0-42/include/linux/list.h`
- 队列----在``
- 映射
- 红黑树













































































