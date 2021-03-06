# JAVA36讲

### 一、“一次编写，多次运行”的理解

一次编写， 多次运行。指的是Java语言跨平台的特性，这个特性依靠于虚拟机。

在不同的平台中，例如windows，linux只需要安装了jdk即java运行环境，就可以编译运行java文件，所以，**不是说java语言是跨平台的**，而是在不同的平台上都有**可以让java运行的环境**。所以，java才有了一次编译，多次运行的特性。

java虚拟机，给了java这个运行的环境，程序源代码到运行有3个阶段：编写——编译——运行。Java是在编译阶段体现出了跨平台的特性，java文件分为两次编译阶段：

- 第一次，把.java文件编译成.class文件字节码文件

- 第二次，由jvm进行编译，将字节码文件**编译和解析**成目标机器可识别的机器码

正因为第二次编译，所以java成为了跨平台运行的语言，前面说的编译和解析，说明jvm不仅是编译就好了：

- 编译：存在JIT编译器进行编译，将经常执行的代码直接编译成机器码，优化效率

- 解释：通过JVM内嵌的解释器解释，效率低比较慢
- AOT编译器：Java9提供的，直接将代码编译成机器码执行



所以：java程序详细的运行顺序为：

javac编译器将java代码编译成.class字节码文件。

由jvm对字节码文件编译解析成机器认识的机器码：

​	加载字节码文件，通过解释器进行逐步解释执行，这样比较慢，所以引进了JIT对高频率使用的代码进行直接编译成本地机器码文件，类似缓存技术，再次运行时就不需要解析这段高频代码了。



### 二、Exception与Error

1.异常类型

- Error:系统错误，虚拟机出错，我们处理不了，也不需要我们来处理。
- Exception，可以捕获的异常，且作出处理。也就是要么捕获异常并作出处理，要么继续抛
  出异常。
- RuntimeException，经常性出现的错误，可以捕获，并作出处理，可以不捕获，也可以不用抛出。ArrayIndexOutOfBoundsException像这种异常可以不捕获，为什么呢？在一个程序里，使用很多数组，如果使用一次捕获一次，则很累。

2.函数返回值有两种类型：值类型与对象引用。对于对象引用，要特别小心，如果在finally代码块中对函数返回的对象成员属性进行了修改，即使不在finally块中显式调用return语句，这个修改也会作用于返回值上。

3.当一个try后跟了很多个catch时，必须先捕获小的异常再捕获大的异常。

4.不要在finally里处理返回值，防止返回值的混淆。

5.上传下载不能抛异常。上传下载一定要关流。

6.异常不是错误。异常控制代码流程不利于代码简单易读。

7.勿在try代码块中调用return、break或continue语句。万一无法避免，一定要确保finally的存在不会改变函数的返回值。



### 三、Java中的各种对象引用

- 强引用：“存活”的对象不会被JVM回收

​	特点：我们平常典型编码Object obj = new Object()中的obj就是强引用。通过关键字**new创建的对象所关联的引用就是强引用**。 当JVM内存空间不足，JVM宁愿抛出OutOfMemoryError运行时错误（OOM），使程序异常终止，也不会靠随意回收具有强引用的“存活”对象来解决内存不足的问题。对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显式地将相应（强）引用赋值为 null，就是可以被垃圾收集的了，具体回收时机还是要看垃圾收集策略。

- 软引用：对象只有JVM内存不足时才清理

​	特点：软引用通过SoftReference类实现。 软引用的生命周期比强引用短一些。只有当 JVM认为内存不足时，才会去试图回收软引用指向的对象：即JVM 会确保在抛出 OutOfMemoryError 之前，清理软引用指向的对象。软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收器回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。后续，我们可以调用ReferenceQueue的poll()方法来检查是否有它所关心的对象被回收。如果队列为空，将返回一个null,否则该方法返回队列中前面的一个Reference对象。
​	应用场景：**软引用通常用来实现内存敏感的缓存**。如果还有空闲内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存。

- 弱引用：随时有可能被JVM清理

​	弱引用通过WeakReference类实现。 弱引用的生命周期比软引用短。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。由于垃圾回收器是一个优先级很低的线程，因此不一定会很快回收弱引用的对象。弱引用可以和一个引用队（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。

​	应用场景：弱应用同样可用于**内存敏感的缓存**。

- 虚引用：确保对象被回收后能做一些事情

​	特点：虚引用也叫幻象引用，通过PhantomReference类来实现。无法通过虚引用访问对象的任何属性或函数。幻象引用仅仅是提供了**一种确保对象被 finalize 以后，做某些事情的机制**。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。虚引用必须和引用队列（ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。

ReferenceQueue queue = new ReferenceQueue ();
PhantomReference pr = new PhantomReference (object, queue);

程序可以通过判断引用队列中是否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。如果程序发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取一些程序行动。应用场景：可用来跟踪对象被垃圾回收器回收的活动，当一个虚引用关联的对象被垃圾收集器回收之前会收到一条系统通知。



### 四、ArrayList数组遍历动态删除节点问题

问题：

​	使用增强for，list.remove(节点)；会报错

​	使用iteractor迭代器,it.remove()不会报错

原因：ArrayList的**fail-first**机制导致的

​	fail-first的本质就是防止collection在迭代器迭代操作中，进行remove操作。

​	foreach的本质是使用迭代器。但是会报错是因为remove掉那个节点后，迭代器的next()不知道该指向哪里了；而使用迭代器的变量it.remove()便不会发生fail-fast，因为是他的变量，而不是集合本身。

更深层的原因：

​	迭代器中next()方法会做判断，**expectedModCount == modCount**，是否相同，不同时抛出异常。这里的expectedModCount是迭代最初时赋初始值modCount，整个迭代过程是**不会改变的**，所以问题出现在modCount上，在list.remove()源码中可以看到，调用该方法，会使**modCount++**，这样modCount再与expectedModCount做比较时，就不相等了，便会抛出ConcurrentModificationException异常。



### 五、反射与动态代理

代理模式（通过代理静默地解决一些业务无关的问题，比如远程、安全、事务、日志、资源关闭……让应用开发者可以只关心他的业务）

- 静态代理：事先写好代理类，可以手工编写，也可以用工具生成。缺点是每个业务类都要对应一个代理类，非常不灵活。

- 动态代理：运行时自动生成代理对象。缺点是生成代理代理对象和调用代理方法都要额外花费时间。

- JDK动态代理：基于Java反射机制实现，必须要实现了接口的业务类才能用这种办法生成代理对象。新版本也开始结合ASM机制。

- cglib动态代理：基于ASM机制实现，通过生成业务类的子类作为代理类。

Java 反射机制的常见应用：动态代理（AOP、RPC）、提供第三方开发者扩展能力（Servlet容器，JDBC连接）、第三方组件创建对象（DI）……