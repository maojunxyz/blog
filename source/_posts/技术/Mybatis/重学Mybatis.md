---
title: 重学Mybatis
date: 2016/03/03 14:32:37
updated: 2016/03/03 14:32:37
categories:
- [技术, Mybatis]
tags:
- Mybatis
---



## Mybatis的优缺点

### 优点

- 基于 SQL 语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL 写在XML 里，解除 sql 与程序代码的耦合，便于统一管理；提供 XML 标签， 支持编写动态 SQL 语句， 并可重用。
- 与 JDBC 相比，减少了 50%以上的代码量，消除了 JDBC 大量冗余的代码，不需要手动开关连接；
-  很好的与各种数据库兼容（ 因为 MyBatis 使用 JDBC 来连接数据库，所以只要JDBC 支持的数据库MyBatis 都支持）。
-  能够与 Spring 很好的集成；
-  提供映射标签， 支持对象与数据库的 ORM 字段关系映射； 提供对象关系映射标签， 支持对象关系组件维护。

### 缺点

- SQL 语句的编写工作量较大， 尤其当字段多、关联表多时， 对开发人员编写SQL 语句的功底有一定要求。
- SQL 语句依赖于数据库， 导致数据库移植性差， 不能随意更换数据库。



## #{}和${}的区别是什么？

-  #{}是预编译处理、是占位符

-  ${}是字符串替换、是拼接符。

Mybatis 在处理#{}时，会将 sql 中的#{}替换为?号，调用 PreparedStatement 来赋值；

Mybatis 在处理${}时， 就是把${}替换成变量的值，调用 Statement 来赋值；

#{} 的变量替换是在DBMS 中、变量替换后，#{} 对应的变量自动加上单引号

${} 的变量替换是在 DBMS 外、变量替换后，${} 对应的变量不会加上单引号

使用#{}可以有效的防止 SQL 注入， 提高系统安全性。





## Mybatis 的插件运行原理，如何编写一个插件

Mybatis 只支持针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这4 种接口的插件， Mybatis 使用 JDK 的动态代理， 为需要拦截的接口生成代理对象以实现接口方法拦截功能， 每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的invoke() 方法， 拦截那些你指定需要拦截的方法。



编写插件： 实现 Mybatis 的 Interceptor 接口并复写 intercept()方法， 然后在给插件编写注解， 指定
要拦截哪一个接口的哪些方法即可， 在配置文件中配置编写的插件。

```java
@Intercepts({@Signature(type = StatementHandler.class, method = "query", args =
{Statement.class, ResultHandler.class}),
@Signature(type = StatementHandler.class, method = "update", args =
{Statement.class}),
@Signature(type = StatementHandler.class, method = "batch", args = {
Statement.class })})
@Component

invocation.proceed() //执行具体的业务逻辑
```

