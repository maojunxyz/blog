---
title: 重学JavaSE
date: 2015/10/17 13:20:07
updated: 2015/10/17 13:20:07
categories:
- [技术, Java]
tags:
- Java
---



## 面向对象

### 封装

类的属性私有，提供get和set对外访问，因为属性的赋值或者获取逻辑只能由类本身决定。而不能由外部胡乱修改。

orm框架操作数据库，不需要关心链接是如何建立的、sql是如何执行的，只需要引入mybatis，调方法即可。

### 继承

继承基类的方法，并做出自己的改变和/或扩展，子类共性的方法或者属性直接使用父类的，而不需要自己再定义，只需扩展自己个性化的。

### 多态

基于对象所属类的不同，外部对同一个方法的调用，实际执行的逻辑不同。

父类的引用指向子类对象;

无法调用子类特有的功能；



## JDK JRE JVM

### JDK

Java Develpment Kit java 开发工具

### JRE

Java Runtime Environment java运行时环境

### JVM

java Virtual Machine java 虚拟机



## hashCode与equals



- 如果两个对象相等，则hashcode一定也是相同的。反过来不一定。
- 两个对象相等,对两个对象分别调用equals方法都返回true
- 因此，equals方法被覆盖过，则hashCode方法也必须被覆盖
- hashCode()的默认行为是对堆上的对象产生独特值。如果没有重写hashCode()，则该class的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）





## final

- 修饰类：表示类不可被继承
- 修饰方法：表示方法不可被子类覆盖，但是可以重载
- 修饰变量：表示变量一旦被赋值就不可以更改它的值。





### 局部内部类和匿名内部类只能访问局部final变量

```java
public class Test {
public static void main(String[] args) {
}
//局部final变量a,b
public void test(final int b) {//jdk8在这里做了优化, 不用写,语法糖，但实际上也是有
的，也不能修改
final int a = 10;
//匿名内部类
new Thread(){
public void run() {
System.out.println(a);
System.out.println(b);
};
}.start();
}
}
class OutClass {
private int age = 12;
public void outPrint(final int x) {
class InClass {
public void InPrint() {
System.out.println(x);
System.out.println(age);
    }
}
new InClass().InPrint();
}
}
```



## 接口和抽象类的区别

- 抽象类可以存在普通成员函数，而接口中只能存在public abstract 方法。
- 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的。
- 抽象类只能继承一个，接口可以实现多个。



抽象类是对类本质的抽象，表达的是 is a 的关系，比如： BMW is a Car 。抽象类包含并实现子类的通
用特性，将子类存在差异化的特性进行抽象，交由子类去实现。

接口是对行为的抽象，表达的是 like a 的关系。比如： Bird like a Aircraft （像飞行器一样可以
飞），但其本质上 is a Bird 。接口的核心是定义行为，即实现类可以做什么，至于实现类主体是谁、
是如何实现的，接口并不关心

使用场景：当你关注一个事物的本质的时候，用抽象类；当你关注一个操作的时候，用接口。





## ArrayList和LinkedList区别

### ArrayList

基于动态数组，连续内存存储，适合下标访问（随机访问），扩容机制：因为数组长度固
定，超出长度存数据时需要新建数组，然后将老数组的数据拷贝到新数组，如果不是尾部插入数据还会
涉及到元素的移动（往后复制一份，插入新元素），使用尾插法并指定初始容量可以极大提升性能、甚
至超过linkedList（需要创建大量的node对象）

### LinkedList

基于链表，可以存储在分散的内存中，适合做数据插入及删除操作，不适合查询：需要逐
一遍历

遍历LinkedList必须使用iterator不能使用for循环，因为每次for循环体内通过get(i)取得某一元素时都需
要对list重新进行遍历，性能消耗极大。
另外不要试图使用indexOf等返回元素索引，并利用其进行遍历，使用indexlOf对list进行了遍历，当结
果为空时会遍历整个列表。



### HashMap和HashTable有什么区别

- HashMap方法没有synchronized修饰，线程非安全，HashTable线程安全；
- HashMap允许key和value为null，而HashTable不允许

### 底层实现

数组+链表
jdk8开始链表高度到8、数组长度超过64，链表转变为红黑树，元素以内部类Node节点存在

- 计算key的hash值，二次hash然后对数组长度取模，对应到数组下标
- 如果没有产生hash冲突(下标位置没有元素)，则直接创建Node存入数组，如果产生hash冲突，先进行equal比较，相同则取代该元素，不同，则判断链表高度插入链表，链表高度达到8，并且数组长度到64则转变为红黑树，长度低于6则将红黑树转回链表
- key为null，存在下标0的位置

### ConcurrentHashMap

### JDK7

- 数据结构：ReentrantLock+Segment+HashEntry，一个Segment中包含一个HashEntry数组，每个HashEntry又是一个链表结构
- 元素查询：二次hash，第一次Hash定位到Segment，第二次Hash定位到元素所在的链表的头部
- 锁：Segment分段锁 Segment继承了ReentrantLock，锁定操作的Segment，其他的Segment不受影响，并发度为segment个数，可以通过构造函数指定，数组扩容不会影响其他的segment
- get方法无需加锁，volatile保证

### JDK8

- 数据结构：synchronized+CAS+Node+红黑树，Node的val和next都用volatile修饰，保证可见性查找，替换，赋值操作都使用CAS
- 锁：锁链表的head节点，不影响其他元素的读写，锁粒度更细，效率更高，扩容时，阻塞所有的读写操作、并发扩容
- 读操作无锁
  - Node的val和next使用volatile修饰，读写线程对该变量互相可见
  - 数组用volatile修饰，保证扩容时被读线程感知



## Java中的异常体系

Java中的所有异常都来自顶级父类Throwable。

- Throwable
  - Exception
  - Error

Error是程序无法处理的错误，一旦出现这个错误，则程序将被迫停止运行。

Exception不会导致程序停止，又分为两个部分RunTimeException运行时异常和CheckedException检查异常。

- RunTimeException常常发生在程序运行过程中，会导致程序当前线程执行失败。
- CheckedException常常发生在程序编译过程中，会导致程序编译不通过。



## Java类加载器

JDK自带有三个类加载器：

1. bootstrap ClassLoader
2. ExtClassLoader
3. AppClassLoader

- BootStrapClassLoader是ExtClassLoader的父类加载器，默认负责加载%JAVA_HOME%lib下的jar包和class文件。
- ExtClassLoader是AppClassLoader的父类加载器，负责加载%JAVA_HOME%/lib/ext文件夹下的jar包和class类。
- AppClassLoader是自定义类加载器的父类，负责加载classpath下的类文件。系统类加载器，线程上下文加载器

可以继承ClassLoader实现自定义类加载器。



## 双亲委派模型





![双亲委派模型](./assets/image-20240418233117182.png)

- 为了安全性，避免用户自己编写的类动态替换 Java的一些核心类，比如 String。
- 同时也避免了类的重复加载，因为 JVM中区分不同类，不仅仅是根据类名，相同的 class文件被不同的 ClassLoader加载就是不同的两个类



## GC判断对象可以回收

- 引用计数法：每个对象有一个引用计数属性，新增一个引用时计数加1，引用释放时计数减1，计数为0时可以回收，
- 可达性分析法：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GCRoots 没有任何引用链相连时，则证明此对象是不可用的，那么虚拟机就判断是可回收对象。

> 注意：引用计数法，可能会出现A 引用了 B，B 又引用了 A，循环引用。这时候就算他们都不再使用了，但因为相互引用 计数器=1 永远无法被回收。

GC Roots的对象有：

- 虚拟机栈(栈帧中的本地变量表）中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI(即一般说的Native方法)引用的对象

可达性算法中的不可达对象并不是立即死亡的，对象拥有一次自我拯救的机会。对象被系统宣告死亡至
少要经历两次标记过程：第一次是经过可达性分析发现没有与GC Roots相连接的引用链，第二次是在由
虚拟机自动建立的Finalizer队列中判断是否需要执行finalize()方法。

当对象变成(GC Roots)不可达时，GC会判断该对象是否覆盖了finalize方法，若未覆盖，则直接将其回
收。否则，若对象未执行过finalize方法，将其放入F-Queue队列，由一低优先级线程执行该队列中对象
的finalize方法。执行finalize方法完毕后，GC会再次判断该对象是否可达，若不可达，则进行回收，否
则，对象“复活”

每个对象只能触发一次finalize()方法

由于finalize()方法运行代价高昂，不确定性大，无法保证各个对象的调用顺序，不推荐大家使用，建议
遗忘它。



## 线程的生命周期和状态

线程通常有五种状态

1. 新建状态（New）：新创建了一个线程对象。
2. 就绪状态（Runnable）：线程对象创建后，其他线程调用了该对象的start方法。该状态的线程位于可运行线程池中，变得可运行，等待获取CPU的使用权。
3. 运行状态（Running）：就绪状态的线程获取了CPU，执行程序代码。
4. 阻塞状态（Blocked）：阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。
5. 死亡状态（Dead）：线程执行完了或者因异常退出了run方法，该线程结束生命周期。

阻塞的情况分为三种

1. 等待阻塞：运行的线程执行wait方法，该线程会释放占用的所有资源，JVM会把该线程放入“等待池”中。进入这个状态后，是不能自动唤醒的，必须依靠其他线程调用notify或notifyAll方法才能被唤醒，wait是object类的方法。
2. 同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入“锁池”中。
3. 其他阻塞：运行的线程执行sleep或join方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep状态超时、join等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。sleep是Thread类的方法。



## sleep()、wait()、join()、yield()的区别

### 锁池

所有需要竞争同步锁的线程都会放在锁池当中，比如当前对象的锁已经被其中一个线程得到，则其他线
程需要在这个锁池进行等待，当前面的线程释放同步锁后锁池中的线程去竞争同步锁，当某个线程得到
后会进入就绪队列进行等待cpu资源分配。

### 等待池

当我们调用wait（）方法后，线程会放到等待池当中，等待池的线程是不会去竞争同步锁。只有调用了
notify（）或notifyAll()后等待池的线程才会开始去竞争锁，notify（）是随机从等待池选出一个线程放
到锁池，而notifyAll()是将等待池的所有线程放到锁池当中

- sleep 是 Thread 类的静态本地方法，wait 则是 Object 类的本地方法。
- sleep方法不会释放lock，但是wait会释放，而且会加入到等待队列中。
- sleep方法不依赖于同步器synchronized，但是wait需要依赖synchronized关键字。
- sleep不需要被唤醒（休眠之后推出阻塞），但是wait需要（不指定时间需要被别人中断）。
- sleep 一般用于当前线程休眠，或者轮循暂停操作，wait 则多用于多线程之间的通信。
- sleep 会让出 CPU 执行时间且强制上下文切换，而 wait 则不一定，wait 后可能还是有机会重新竞争到锁继续执行的。

>sleep就是把cpu的执行资格和执行权释放出去，不再运行此线程，当定时时间结束再取回cpu资源，参与cpu的调度，获取到cpu资源后就可以继续运行了。而如果sleep时该线程有锁，那么sleep不会释放这个锁，而是把锁带着进入了冻结状态，也就是说其他需要这个锁的线程根本不可能获取到这个锁。也就是说无法执行程序。如果在睡眠期间其他线程调用了这个线程的interrupt方法，那么这个线程也会抛出interruptexception异常返回，这点和wait是一样的。



- yield（）执行后线程直接进入就绪状态，马上释放了cpu的执行权，但是依然保留了cpu的执行资格，所以有可能cpu下次进行线程调度还会让这个线程获取到执行权继续执行
- join（）执行后线程进入阻塞状态，例如在线程B中调用线程A的join（），那线程B会进入到阻塞队列，直到线程A结束或中断线程

```java
public static void main(String[] args) throws InterruptedException {
Thread t1 = new Thread(new Runnable() {
@Override
public void run() {
try {
Thread.sleep(3000);
} catch (InterruptedException e) {
e.printStackTrace();
}
System.out.println("22222222");
}
});
t1.start();
t1.join();
// 这行代码必须要等t1全部执行完毕，才会执行
System.out.println("1111");
}
22222222
1111
```



## 线程安全的理解

不是线程安全、应该是内存安全，堆是共享内存，可以被所有线程访问

> 当多个线程访问一个对象时，如果不用进行额外的同步控制或其他的协调操作，调用这个对象的行为都可以获得正确的结果，我们就说这个对象是线程安全的



堆是进程和线程共有的空间，分全局堆和局部堆。全局堆就是所有没有分配的空间，局部堆就是用户分
配的空间。堆在操作系统对进程初始化的时候分配，运行过程中也可以向系统要额外的堆，但是用完了
要还给操作系统，要不然就是内存泄漏。



> 在Java中，堆是Java虚拟机所管理的内存中最大的一块，是所有线程共享的一块内存区域，在虚
>
> 拟机启动时创建。堆所存在的内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及
>
> 数组都在这里分配内存



栈是每个线程独有的，保存其运行状态和局部自动变量的。栈在线程开始的时候初始化，每个线程的栈
互相独立，因此，栈是线程安全的。操作系统在切换线程的时候会自动切换栈。栈空间不需要在高级语
言里面显式的分配和释放。



目前主流操作系统都是多任务的，即多个进程同时运行。为了保证安全，每个进程只能访问分配给自己
的内存空间，而不能访问别的进程的，这是由操作系统保障的。
在每个进程的内存空间中都会有一块特殊的公共区域，通常称为堆（内存）。进程内的所有线程都可以
访问到该区域，这就是造成问题的潜在原因。





## Thread、Runable的区别

Thread和Runnable的实质是继承关系，没有可比性。无论使用Runnable还是Thread，都会new
Thread，然后执行run方法。用法上，如果有复杂的线程操作需求，那就选择继承Thread，如果只是简
单的执行一个任务，那就实现runnable。



```java
//会卖出多一倍的票
public class Test {
public static void main(String[] args) {
// TODO Auto-generated method stub
new MyThread().start();
new MyThread().start();
}
static class MyThread extends Thread{
private int ticket = 5;
public void run(){
while(true){
System.out.println("Thread ticket = " + ticket--);
if(ticket < 0){
break;
}
}
}
}
}
```
> MyThread创建了两个实例，自然会卖出两倍，属于用法错误

```java
//正常卖出
public class Test2 {
public static void main(String[] args) {
// TODO Auto-generated method stub
MyThread2 mt=new MyThread2();
new Thread(mt).start();
new Thread(mt).start();
}
static class MyThread2 implements Runnable{
private int ticket = 5;
public void run(){
while(true){
System.out.println("Runnable ticket = " + ticket--);
if(ticket < 0){
break;
}
}
}
}
}
```



## 守护线程的理解

守护线程：为所有非守护线程提供服务的线程；任何一个守护线程都是整个JVM中所有非守护线程的保
姆；
守护线程类似于整个进程的一个默默无闻的小喽喽；它的生死无关重要，它却依赖整个进程而运行；哪
天其他线程结束了，没有要执行的了，程序就结束了，理都没理守护线程，就把它中断了；

> 注意： 由于守护线程的终止是自身无法控制的，因此千万不要把IO、File等重要操作逻辑分配给它；因
> 为它不靠谱；

守护线程的作用是什么？
举例， GC垃圾回收线程：就是一个经典的守护线程，当我们的程序中不再有任何运行的Thread,程序就
不会再产生垃圾，垃圾回收器也就无事可做，所以当垃圾回收线程是JVM上仅剩的线程时，垃圾回收线
程会自动离开。它始终在低级别的状态中运行，用于实时监控和管理系统中的可回收资源。

应用场景：

来为其它线程提供服务支持的情况；或者在任何情况下，程序结束时，这个线
程必须正常且立刻关闭，就可以作为守护线程来使用；反之，如果一个正在执行某个操作的线程必须要
正确地关闭掉否则就会出现不好的后果的话，那么这个线程就不能是守护线程，而是用户线程。通常都
是些关键的事务，比方说，数据库录入或者更新，这些操作都是不能中断的。
thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个
IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
在Daemon线程中产生的新线程也是Daemon的。
守护线程不能用于去访问固有资源，比如读写操作或者计算逻辑。因为它会在任何时候甚至在一个操作
的中间发生中断。
Java自带的多线程框架，比如ExecutorService，会将守护线程转换为用户线程，所以如果要使用后台线
程就不能用Java的线程池。



## ThreadLocal的原理和使用场景

每一个 Thread 对象均含有一个 ThreadLocalMap 类型的成员变量 threadLocals ，它存储本线程中所
有ThreadLocal对象及其对应的值
ThreadLocalMap 由一个个 Entry 对象构成
Entry 继承自 WeakReference<ThreadLocal<?>> ，一个 Entry 由 ThreadLocal 对象和 Object 构
成。由此可见， Entry 的key是ThreadLocal对象，并且是一个弱引用。当没指向key的强引用后，该
key就会被垃圾收集器回收
当执行set方法时，ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap对
象。再以当前ThreadLocal对象为key，将值存储进ThreadLocalMap对象中。
get方法执行过程类似。ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap
对象。再以当前ThreadLocal对象为key，获取对应的value。
由于每一条线程均含有各自私有的ThreadLocalMap容器，这些容器相互独立互不影响，因此不会存在
线程安全性问题，从而也无需使用同步机制来保证多条线程访问容器的互斥性。



使用场景：

- 在进行对象跨层传递的时候，使用ThreadLocal可以避免多次传递，打破层次间的约束。
- 线程间数据隔离
- 进行事务操作，用于存储线程事务信息。
- 数据库连接，Session会话管理。

> Spring框架在事务开始时会给当前线程绑定一个Jdbc Connection,在整个事务过程都是使用该线程绑定的
>
> connection来执行数据库操作，实现了事务的隔离性。Spring框架里面就是用的ThreadLocal来实现这种
>
> 隔离



![threadLocal](./assets/image-20240418234331801.png)



## ThreadLocal内存泄露原因，如何避免

内存泄露为程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露
堆积后果很严重，无论多少内存,迟早会被占光，
不再会被使用的对象或者变量占用的内存不能被回收，就是内存泄露。
强引用：使用最普遍的引用(new)，一个对象具有强引用，不会被垃圾回收器回收。当内存空间不足，
Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不回收这种对象。
Spring框架在事务开始时会给当前线程绑定一个Jdbc Connection,在整个事务过程都是使用该线程绑定的
connection来执行数据库操作，实现了事务的隔离性。Spring框架里面就是用的ThreadLocal来实现这种
隔离
如果想取消强引用和某个对象之间的关联，可以显式地将引用赋值为null，这样可以使JVM在合适的时
间就会回收该对象。
弱引用：JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。在java中，用
java.lang.ref.WeakReference类来表示。可以在缓存中使用弱引用。
ThreadLocal的实现原理，每一个Thread维护一个ThreadLocalMap，key为使用弱引用的ThreadLocal
实例，value为线程变量的副本。

hreadLocalMap使用ThreadLocal的弱引用作为key，如果一个ThreadLocal不存在外部**强引用**时，

Key(ThreadLocal)势必会被GC回收，这样就会导致ThreadLocalMap中key为null， 而value还存在着强

引用，只有thead线程退出以后,value的强引用链条才会断掉，但如果当前线程再迟迟不结束的话，这

些key为null的Entry的value就会一直存在一条强引用链（红色链条）

key 使用强引用

当hreadLocalMap的key为强引用回收ThreadLocal时，因为ThreadLocalMap还持有ThreadLocal的强

引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。

key 使用弱引用

当ThreadLocalMap的key为弱引用回收ThreadLocal时，由于ThreadLocalMap持有ThreadLocal的弱

引用，即使没有手动删除，ThreadLocal也会被回收。当key为null，在下一次ThreadLocalMap调用

set(),get()，remove()方法的时候会被清除value值。

因此，ThreadLocal内存泄漏的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有

手动删除对应key就会导致内存泄漏，而不是因为弱引用。

ThreadLocal正确的使用方法

每次使用完ThreadLocal都调用它的remove()方法清除数据

将ThreadLocal变量定义成private static，这样就一直存在ThreadLocal的强引用，也就能保证任

何时候都能通过ThreadLocal的弱引用访问到Entry的value值，进而清除掉 。



## 并发、并行、串行的区别

- 串行在时间上不可能发生重叠，前一个任务没搞定，下一个任务就只能等着
- 并行在时间上是重叠的，两个任务在**同一时刻互不干扰**的同时执行。
- 并发允许两个任务彼此干扰。统一时间点、只有一个任务运行，交替执行



### 并发的三大特性

### 原子性

原子性是指在一个操作中cpu不可以在中途暂停然后再调度，即不被中断操作，要不全部执行完成，要
不都不执行。就好比转账，从账户A向账户B转1000元，那么必然包括2个操作：从账户A减去1000元，
往账户B加上1000元。2个操作必须全部完成。



```java
private long count = 0;
public void calc() {
count++;
}
```

1. 将 count 从主存读到工作内存中的副本中
2. +1的运算
3. 将结果写入工作内存
4. 将工作内存的值刷回主存(什么时候刷入由操作系统决定，是不确定的

那程序中原子性指的是最小的操作单元，比如自增操作，它本身其实并不是原子性操作，分了3步的，
包括读取变量的原始值、进行加1操作、写入工作内存。所以在多线程中，有可能一个线程还没自增
完，可能才执行到第二部，另一个线程就已经读取了值，导致结果错误。那如果我们能保证自增操作是
一个原子性的操作，那么就能保证其他线程读取到的一定是自增后的数据

关键字：synchronized



### 可见性

当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
若两个线程在不同的cpu，那么线程1改变了i的值还没刷新到主存，线程2又使用了i，那么这个i值肯定
还是之前的，线程1对变量的修改线程没看到这就是可见性问题。

```java
//线程1
boolean stop = false;
while(!stop){
doSomething();
}
//线程2
stop = true;
```

如果线程2改变了stop的值，线程1一定会停止吗？不一定。当线程2更改了stop变量的值之后，但是还
没来得及写入主存当中，线程2转去做其他事情了，那么线程1由于不知道线程2对stop变量的更改，因
此还会一直循环下去。

关键字：volatile、synchronized、final



### 有序性

虚拟机在进行代码编译时，对于那些改变顺序之后不会对最终结果造成影响的代码，虚拟机不一定会按
照我们写的代码的顺序来执行，有可能将他们重排序。实际上，对于有些代码进行重排序之后，虽然对
变量的值没有造成影响，但有可能会出现线程安全问题。

```java
int a = 0;
bool flag = false;
public void write() {
a = 2; //1
flag = true; //2
}
public void multiply() {
if (flag) { //3
int ret = a * a;//4
}
}
```

write方法里的1和2做了重排序，线程1先对flag赋值为true，随后执行到线程2，ret直接计算出结果，
再到线程1，这时候a才赋值为2,很明显迟了一步
关键字：volatile、synchronized
volatile本身就包含了禁止指令重排序的语义，而synchronized关键字是由“一个变量在同一时刻只允许
一条线程对其进行lock操作”这条规则明确的。
synchronized关键字同时满足以上三种特性，但是volatile关键字不满足原子性。
在某些情况下，volatile的同步机制的性能确实要优于锁(使用synchronized关键字或
java.util.concurrent包里面的锁)，因为volatile的总开销要比锁低。
我们判断使用volatile还是加锁的唯一依据就是volatile的语义能否满足使用的场景(原子性)



## volatile

 保证被volatile修饰的共享变量对所有线程总是可见的，也就是当一个线程修改了一个被volatile修

饰共享变量的值，新值总是可以被其他线程立即得知。

```java
//线程1
boolean stop = false;
while(!stop){
doSomething();
}
//线程2
stop = true;
```

如果线程2改变了stop的值，线程1一定会停止吗？不一定。当线程2更改了stop变量的值之后，但

是还没来得及写入主存当中，线程2转去做其他事情了，那么线程1由于不知道线程2对stop变量的

更改，因此还会一直循环下去。



 禁止指令重排序优化。

```java
int a = 0;
bool flag = false;
public void write() {
a = 2; //1
flag = true; //2
}
public void multiply() {
if (flag) { //3
int ret = a * a;//4
}
}
```

write方法里的1和2做了重排序，线程1先对flag赋值为true，随后执行到线程2，ret直接计算出结果，

再到线程1，这时候a才赋值为2,很明显迟了一步。

但是用volatile修饰之后就变得不一样了

第一：使用volatile关键字会强制将修改的值立即写入主存；

第二：使用volatile关键字的话，当线程2进行修改时，会导致线程1的工作内存中缓存变量stop的缓存

行无效（反映到硬件层的话，就是CPU的L1或者L2缓存中对应的缓存行无效）；

第三：由于线程1的工作内存中缓存变量stop的缓存行无效，所以线程1再次读取变量stop的值时会去主

存读取。

inc++; 其实是两个步骤，先加加，然后再赋值。不是原子性操作，所以volatile不能保证线程安全。



## 为什么用线程池

- 降低资源消耗；提高线程利用率，降低创建和销毁线程的消耗。
- 提高响应速度；任务来了，直接有线程可用可执行，而不是先创建线程，再执行。
- 提高线程的可管理性；线程是稀缺资源，使用线程池可以统一分配调优监控。



## 线程池的参数

- corePoolSize 代表核心线程数，也就是正常情况下创建工作的线程数，这些线程创建后并不会消除，而是一种常驻线程
- maxinumPoolSize 代表的是最大线程数，它与核心线程数相对应，表示最大允许被创建的线程数，比如当前任务较多，将核心线程数都用完了，还无法满足需求时，此时就会创建新的线程，但是线程池内线程总数不会超过最大线程数
- keepAliveTime 、 unit 表示超出核心线程数之外的线程的空闲存活时间，也就是核心线程不会消除，但是超出核心线程数的部分线程如果空闲一定的时间则会被消除,我们可以通过setKeepAliveTime 来设置空闲时间
- workQueue 用来存放待执行的任务，假设我们现在核心线程都已被使用，还有任务进来则全部放入队列，直到整个队列被放满但任务还再持续进入则会开始创建新的线程
- ThreadFactory 实际上是一个线程工厂，用来生产线程执行任务。我们可以选择使用默认的创建工厂，产生的线程都在同一个组内，拥有相同的优先级，且都不是守护线程。当然我们也可以选择自定义线程工厂，一般我们会根据业务来制定不同的线程工厂
- Handler 任务拒绝策略，有两种情况，第一种是当我们调用 shutdown 等方法关闭线程池后，这时候即使线程池内部还有没执行完的任务正在执行，但是由于线程池已经关闭，我们再继续想线程池提交任务就会遭到拒绝。另一种情况就是当达到最大线程数，线程池已经没有能力继续处理新提交的任务时，这是也就拒绝

## 线程池处理流程

![线程池处理流程](./assets/image-20240418234957710.png)



## 线程池中阻塞队列的作用

一般的队列只能保证作为一个有限长度的缓冲区，如果超出了缓冲长度，就无法保留当前的任务
了，阻塞队列通过阻塞可以保留住当前想要继续入队的任务。
阻塞队列可以保证任务队列中没有任务时阻塞获取任务的线程，使得线程进入wait状态，释放cpu资
源。
阻塞队列自带阻塞和唤醒的功能，不需要额外处理，无任务执行时,线程池利用阻塞队列的take方法挂
起，从而维持核心线程的存活、不至于一直占用cpu资源



## 为什么是先添加列队而不是先创建最大线程

在创建新线程的时候，是要获取全局锁的，这个时候其它的就得阻塞，影响了整体效率。
就好比一个企业里面有10个（core）正式工的名额，最多招10个正式工，要是任务超过正式工人数
（task > core）的情况下，工厂领导（线程池）不是首先扩招工人，还是这10人，但是任务可以稍微积
压一下，即先放到队列去（代价低）。10个正式工慢慢干，迟早会干完的，要是任务还在继续增加，超
过正式工的加班忍耐极限了（队列满了），就的招外包帮忙了（注意是临时工）要是正式工加上外包还
是不能完成任务，那新来的任务就会被领导拒绝了（线程池的拒绝策略）。





## 线程池中线程复用原理

线程池将线程和任务进行解耦，线程是线程，任务是任务，摆脱了之前通过 Thread 创建线程时的
一个线程必须对应一个任务的限制。

在线程池中，同一个线程可以从阻塞队列中不断获取新任务来执行，其核心原理在于线程池对
Thread 进行了封装，并不是每次执行任务都会调用 Thread.start() 来创建新线程，而是让每个线程去
执行一个“循环任务”，在这个“循环任务”中不停检查是否有任务需要被执行，如果有则直接执行，也就
是调用任务中的 run 方法，将 run 方法当成一个普通的方法执行，通过这种方式只使用固定的线程就
将所有任务的 run 方法串联起来。