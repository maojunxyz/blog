---
title: JVM类加载器
date: 2021/02/02 11:07:27
updated: 2021/02/02 11:07:27
categories:
- [技术, JVM]
tags:
- JVM
- Java
---



## 类的生命周期

1. 加载（Loading）：找 Class 文件
2. 验证（Verification）：验证格式、依赖
3. 准备（Preparation）：静态字段、方法表
4. 解析（Resolution）：符号解析为引用
5. 初始化（Initialization）：构造器、静态变量赋值、静态代码块
6. 使用（Using）
7. 卸载（Unloading）

![类的生命周期](./assets/image-20240421110852538.png)

## 类的加载时机

1. 当虚拟机启动时，初始化用户指定的主类，就是启动执行的 main 方法所在的类；
2. 当遇到用以新建目标类实例的 new 指令时，初始化 new 指令的目标类，就是 new 一个类的时候要初始化；
3. 当遇到调用静态方法的指令时，初始化该静态方法所在的类；
4. 当遇到访问静态字段的指令时，初始化该静态字段所在的类；
5. 子类的初始化会触发父类的初始化；
6. 如果一个接口定义了 default 方法，那么直接实现或者间接实现该接口的类的初始化，会触发该接口的初始化；
7. 使用反射 API 对某个类进行反射调用时，初始化这个类，其实跟前面一样，反射调用要
8. 么是已经有实例了，要么是静态方法，都需要初始化；
9. 当初次调用 MethodHandle 实例时，初始化该 MethodHandle 指向的方法所在的类。



## 不会初始化（可能会加载）

1. 通过子类引用父类的静态字段，只会触发父类的初始化，而不会触发子类的初始化。
2. 定义对象数组，不会触发该类的初始化。
3. 常量在编译期间会存入调用类的常量池中，本质上并没有直接引用定义常量的类，不会触发定义常量所在的类。
4. 通过类名获取 Class 对象，不会触发类的初始化，Hello.class 不会让 Hello 类初始化。
5. 通过 Class.forName 加载指定类时，如果指定参数 initialize 为 false 时，也不会触发类初始化，其实这个参数是告诉虚拟机，是否要对类进行初始化。(Class.forName”jvm.Hello”)默认会加载 Hello 类。
6. 通过 ClassLoader 默认的 loadClass 方法，也不会触发初始化动作（加载了，但是不初始化）。





## 三类加载器

1. 启动类加载器（BootstrapClassLoader）
2. 扩展类加载器（ExtClassLoader）
3. 应用类加载器（AppClassLoader）

![类加载器](./assets/image-20240421111920010.png)

### 加载器特点

1. 双亲委托
2. 负责依赖
3. 缓存加载

![依赖关系](./assets/image-20240421111944649.png)



## 当前 ClassLoader 加载了哪些 Jar



```java
package jvm;

import java.lang.reflect.Field;
import java.net.URL;
import java.net.URLClassLoader;
import java.util.ArrayList;

public class JvmClassLoaderPrintPath {

    public static void main(String[] args) {

        // 启动类加载器
        URL[] urls = sun.misc.Launcher.getBootstrapClassPath().getURLs();
        System.out.println("启动类加载器");
        for (URL url : urls) {
            System.out.println(" ===> " + url.toExternalForm());
        }

        printClassloader("扩展类加载器", JvmClassLoaderPrintPath.class.getClassLoader().getParent());
        printClassloader("应用类加载器", JvmClassLoaderPrintPath.class.getClassLoader());

    }

    private static void printClassloader(String name, ClassLoader classLoader) {
        System.out.println();
        if (null != classLoader) {
            System.out.println(name + " Classloader -> " + classLoader);
            printURLForClassloader(classLoader);
        } else {
            System.out.println(name + " Classloader -> null");
        }
    }

    private static void printURLForClassloader(ClassLoader classLoader) {
        Object ucp = insightField(classLoader, "ucp");
        Object path = insightField(ucp, "path");
        ArrayList paths = (ArrayList) path;
        assert paths != null;
        for (Object p : paths) {
            System.out.println(" ===> " + p.toString());
        }
    }

    private static Object insightField(Object obj, String fName) {
        Field f;
        try {
            if (obj instanceof URLClassLoader) {
                f = URLClassLoader.class.getDeclaredField(fName);
            } else {
                f = obj.getClass().getDeclaredField(fName);
            }
            f.setAccessible(true);
            return f.get(obj);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```



## 自定义 ClassLoader

```java
package jvm;

public class Hello {
    static {
        System.out.println("Hello Class Initialized!");
    }
}
```

编译成字节码class文件，然后获取class文件的base64编码。

![base64编码](./assets/image-20240421124423794.png)

将base64编码放入自定义类加载器中初始化。

```java
package jvm;

import java.util.Base64;

public class HelloClassLoader extends ClassLoader {

    public static void main(String[] args) throws Exception {

        new HelloClassLoader().findClass("jvm.Hello").newInstance();
    }

    @Override
    protected Class<?> findClass(String name) {
        String helloBase64 = "yv66vgAAADQAHwoABgARCQASABMIABQKABUAFgcAFwcAGAEABjxpbml0PgEAAygpVgEABENvZGUBAA9MaW5lTnVtYmVyVGFibGUBABJMb2NhbFZhcmlhYmxlVGFibGUBAAR0aGlzAQALTGp2bS9IZWxsbzsBAAg8Y2xpbml0PgEAClNvdXJjZUZpbGUBAApIZWxsby5qYXZhDAAHAAgHABkMABoAGwEAGEhlbGxvIENsYXNzIEluaXRpYWxpemVkIQcAHAwAHQAeAQAJanZtL0hlbGxvAQAQamF2YS9sYW5nL09iamVjdAEAEGphdmEvbGFuZy9TeXN0ZW0BAANvdXQBABVMamF2YS9pby9QcmludFN0cmVhbTsBABNqYXZhL2lvL1ByaW50U3RyZWFtAQAHcHJpbnRsbgEAFShMamF2YS9sYW5nL1N0cmluZzspVgAhAAUABgAAAAAAAgABAAcACAABAAkAAAAvAAEAAQAAAAUqtwABsQAAAAIACgAAAAYAAQAAAAMACwAAAAwAAQAAAAUADAANAAAACAAOAAgAAQAJAAAAJQACAAAAAAAJsgACEgO2AASxAAAAAQAKAAAACgACAAAABQAIAAYAAQAPAAAAAgAQ";
        byte[] bytes = decode(helloBase64);
        return defineClass(name,bytes,0,bytes.length);
    }

    public byte[] decode(String base64) {
        return Base64.getDecoder().decode(base64);
    }
}
```



## 添加引用类的几种方式



1. 放到 JDK 的 lib/ext 下，或者 -Djava.ext.dirs
2. java-cp/classpath 或者 class 文件放到当前路径
3. 自定义 ClassLoader 加载
4. 拿到当前执行类的 ClassLoader，反射调用 addUrl 方法添加 Jar 或路径（JDK9 无效）



在JDK8下，当前执行类的 ClassLoader，反射调用 addUrl 方法添加 Jar 或路径示例：

把之前Hello.java编译后的字节码文件放入到目录：c:/Users/maojun/lib/jvm/Hello.class。

```java
public class JvmAppClassLoaderAddURL {
    public static void main(String[] args) {

        String appPath = "file:/c:/Users/maojun/lib/";
        URLClassLoader urlClassLoader = (URLClassLoader) JvmAppClassLoaderAddURL.class.getClassLoader();
        try {
            Method addURL = URLClassLoader.class.getDeclaredMethod("addURL", URL.class);
            addURL.setAccessible(true);
            URL url = new URL(appPath);
            addURL.invoke(urlClassLoader, url);
            // 效果跟Class.forName("jvm.Hello").newInstance()一样
            Class.forName("jvm.Hello");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

在 JDK 9 及以上版本中，由于模块系统的引入，类加载器的实现发生了改变。AppClassLoader的实现不再是URLClassLoader的直接子类，而是由jdk.internal.loader.ClassLoaders$AppClassLoader实现

在JDK9之后的版本修改为下面的代码：

```java
public class JvmAppClassLoaderAddURL {
    public static void main(String[] args) {

        String appPath = "file:/c:/Users/maojun/lib/";
        ClassLoader classLoader = JvmAppClassLoaderAddURL.class.getClassLoader();
        try {
            URL[] url = new URL[]{new URL(appPath)};
            Class.forName("jvm.Hello",true,new URLClassLoader(url));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



（完）