---
title: JVM字节码基础
date: 2021/01/07 20:06:24
updated: 2024/04/20 21:00:55
categories:
- [技术, JVM]
tags:
- JVM
- Java
---



## 编程语言类型

- 面向过程、面向对象、面向函数
- 静态类型、动态类型
- 编译执行、解释执行
- 有虚拟机、无虚拟机
- 有 GC、无 GC

> Java 是一种面向对象、静态类型、编译执行，有 VM/GC 和运行时、跨平台的高级语言。

![编程语言的进化](./assets/image-20240420200734456.png)





![源代码跨平台](./assets/image-20240420200854257.png)

![二进制跨平台](./assets/image-20240420200909577.png)



## 字节码、类加载器、虚拟机关系

![字节码、类加载器、虚拟机关系](./assets/image-20240420201015026.png)



## Java字节码

Java bytecode 由单字节（byte）的指令组成，理论上最多支持 256 个操作码（opcode）。

实际上 Java 只使用了200左右的操作码， 还有一些操作码则保留给调试操作。



根据指令的性质，主要分为四个大类：

1. 栈操作指令，包括与局部变量交互的指令
2. 程序流程控制指令
3. 对象操作指令，包括方法调用指令
4. 算术运算以及类型转换指令

### 生成字节码

```java
package jvm;

public class HelloByteCode {
    public static void main(String[] args) {
        HelloByteCode obj = new HelloByteCode();
    }
}
```

编译：javac jvm/HelloByteCode.java

查看字节码：javap -c jvm.HelloByteCode

```java
Compiled from "HelloByteCode.java"
public class jvm.HelloByteCode {
  public jvm.HelloByteCode();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class jvm/HelloByteCode
       3: dup
       4: invokespecial #3                  // Method "<init>":()V
       7: astore_1
       8: return
}
```

查看更多细节字节码：javap -c -verbose demo.jvm0104.HelloByteCode

```java
Classfile /C:/Users/maojun/IdeaProjects/jvm/HelloByteCode.class
  Last modified 2024-4-20; size 292 bytes
  MD5 checksum bb865898ca7ce1961a447afc5e44b29d
  Compiled from "HelloByteCode.java"
public class jvm.HelloByteCode
  minor version: 0
  major version: 61
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Class              #8             // jvm/HelloByteCode
   #8 = Utf8               jvm/HelloByteCode
   #9 = Methodref          #7.#3          // jvm/HelloByteCode."<init>":()V
  #10 = Utf8               Code
  #11 = Utf8               LineNumberTable
  #12 = Utf8               main
  #13 = Utf8               ([Ljava/lang/String;)V
  #14 = Utf8               SourceFile
  #15 = Utf8               HelloByteCode.java
{
  public jvm.HelloByteCode();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=2, args_size=1
         0: new           #7                  // class jvm/HelloByteCode
         3: dup
         4: invokespecial #9                  // Method "<init>":()V
         7: astore_1
         8: return
      LineNumberTable:
        line 5: 0
        line 6: 8
}
SourceFile: "HelloByteCode.java"
```

如果需要查看字节码Debug信息，可以在编译字节码的使用增加`-g`参数来编译字节码。

javac -g  jvm/HelloByteCode.java

然后再查看更多细节字节码：javap -c -verbose demo.jvm0104.HelloByteCode

```java
Classfile /C:/Users/maojun/IdeaProjects/jvm/HelloByteCode.class
  Last modified 2024-4-20; size 423 bytes
  MD5 checksum f6d53454ea5d1af439c610137591b74e
  Compiled from "HelloByteCode.java"
public class jvm.HelloByteCode
  minor version: 0
  major version: 61
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Class              #8             // jvm/HelloByteCode
   #8 = Utf8               jvm/HelloByteCode
   #9 = Methodref          #7.#3          // jvm/HelloByteCode."<init>":()V
  #10 = Utf8               Code
  #11 = Utf8               LineNumberTable
  #12 = Utf8               LocalVariableTable
  #13 = Utf8               this
  #14 = Utf8               Ljvm/HelloByteCode;
  #15 = Utf8               main
  #16 = Utf8               ([Ljava/lang/String;)V
  #17 = Utf8               args
  #18 = Utf8               [Ljava/lang/String;
  #19 = Utf8               obj
  #20 = Utf8               SourceFile
  #21 = Utf8               HelloByteCode.java
{
  public jvm.HelloByteCode();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Ljvm/HelloByteCode;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=2, args_size=1
         0: new           #7                  // class jvm/HelloByteCode
         3: dup
         4: invokespecial #9                  // Method "<init>":()V
         7: astore_1
         8: return
      LineNumberTable:
        line 5: 0
        line 6: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  args   [Ljava/lang/String;
            8       1     1   obj   Ljvm/HelloByteCode;
}
SourceFile: "HelloByteCode.java"
```

 这时就会多了本地方案表信息：LocalVariableTable，里面包含了槽位和名称信息。

很多使用直接使用字节码反编译查看源码很多方法的形参都是y1,y2就是由于编译字节码的时候信息被擦除了。

### 字节码的运行时结构

JVM 是一台基于栈的计算机器。
每个线程都有一个独属于自己的线程栈（JVM Stack），用于存储栈帧（Frame）。
每一次方法调用、JVM 都会自动创建一个栈帧。
栈帧由操作数栈、 局部变量数组以及一个 Class 引用组成。
Class 引用指向当前方法在运行时常量池中对应的 Class。

![字节码运行时结构](./assets/image-20240420210205570.png)



### 从助记符到二进制

```java
       0: new           #2                  // class jvm/HelloByteCode
       3: dup
       4: invokespecial #3                  // Method "<init>":()V
       7: astore_1
       8: return
```

0,3,4,7,8代表了字节偏移量,8-0相当于总共占用了8个字节，其中：

- new占用了3-0=3个字节。
- dup占用了4-3=1个字节。
- invokespecial #3 占用了7-4=3个字节。
- astore_1占用了8-7=1个字节。
- return占用了0个字节。

将上面的操作指令放入到字节码数组中。



![对应16进制](./assets/image-20240420210330242.png)

new、dup等这些字节码指令集分别对应16进制的0xbb和0x59等。

将其翻译成16进制后，则可以通过16进制编辑器打开字节码文件，找到其在字节码文件中的位置。

![16进制字节码](./assets/image-20240420212248791.png)





### 算数操作与类型转换

![四则运算符指令](./assets/image-20240420230244735.png)

![类型转化指令](./assets/image-20240420230305755.png)

### 方法调用的指令

- Invokestatic：顾名思义，这个指令用于调用某个类的静态方法，这是方法调用指令中最快的一个。
- Invokespecial ：用来调用构造函数，但也可以用于调用同一个类中的 private 方法, 以及可见的超类方法。
- invokevirtual ：如果是具体类型的目标对象，invokevirtual 用于调用公共、受保护和package 级的私有方法。
- invokeinterface ：当通过接口引用来调用方法时，将会编译为 invokeinterface 指令。
- invokedynamic ： JDK7 新增加的指令，是实现“动态类型语言”（Dynamically Typed 
- Language）支持而进行的升级改进，同时也是 JDK8 以后支持 lambda 表达式的实现基础。



### 字节码执行过程

```java
package jvm;

public class Demo {
    public static void foo() {
        int a = 1;
        int b = 2;
        int c = (a + b) * 5;
    }
}
```
执行`javap -c Demo.class`查看字节码。
```java
Compiled from "Demo.java"
public class jvm.Demo {
  public jvm.Demo();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void foo();
    Code:
       0: iconst_1
       1: istore_0
       2: iconst_2
       3: istore_1
       4: iload_0
       5: iload_1
       6: iadd
       7: iconst_5
       8: imul
       9: istore_2
      10: return
}
```

对应JVM执行字节码过程 ：

1. 将int类型的数字1推送至栈顶

![将数字1推送至栈顶](./assets/image-20240420222241881.png)

2. 将int数字存放到局部变量区0的位置。

![将int存放到局部变量区0](./assets/image-20240420222258745.png)

3. 将int类型的数字2推送至栈顶

![将int类型的数字2推送至栈顶](./assets/image-20240420222338490.png)

4. 将数字2存放到局部变量区的位置1

![将数字2存放到局部变量区的位置1](./assets/image-20240420222353781.png)

5. 加载局部变量区0的int数字到栈顶

![加载局部变量区0位置的int数字](./assets/image-20240420222407364.png)

6. 加载局部变量区1的int数字到栈顶

![image-20240420222418235](./assets/image-20240420222418235.png)

7. 计算2个int整数的和

![计算2个int的求和](./assets/image-20240420222426761.png)

8. 将int类型的数字5推送至栈顶

![将int类型的数字5推送至栈顶](./assets/image-20240420222435820.png)

9. 计算2个int整数的乘积

![计算2个int整数的乘积](./assets/image-20240420222445389.png)

10. 将数字2存放到局部变量区的位置2

![将数字2存放到局部变量区的位置2](./assets/image-20240420222454121.png)

11. 返回值，弹栈。

![返回值](./assets/image-20240420222503273.png)

（完）
