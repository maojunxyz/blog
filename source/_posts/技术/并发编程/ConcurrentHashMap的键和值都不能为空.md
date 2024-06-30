---
title: ConcurrentHashMap的键和值都不能为空
date: 2023/11/03 16:22
updated: 2023/11/03 16:22
categories:
- [技术, 并发编程]
tags:
- Java
- ConcurrentHashMap
---

## ConcurrentHashMap的键和值都不能为空

查看源码，在 put 方法里面，如果 key 或者 value 为空，则抛出空指针异常。

```java
    final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        ...
    }
```

这是为了避免在多线程环境下出现歧义问题。



### key不能为null的原因

线程 t1 调用 containsKey 方法判断 key 是否存在，假设当前这个 key 不存在，本来应该返回 false。

但是在 T1 线程返回之前，正好有一个 T2 线程插入了这个 key，但是 value 为 null。

这就导致原本 T1 线程返回的结果有可能是 true，有可能是 false，取决于 T1 和 T2 线程的执行顺序。



![image-20231103162609717](./assets/image-20231103162609717.png)



### value不能为null的原因

到底是插入的 null值，还是集合中不存在才返回的 null值。这就产生了歧义问题。



### 总结

 ConcurrentHashMap 又是一个线程安全的集合，所以自然就不允许 key 或者 value 为 null。

而 HashMap 中是允许存 null 的，因为它不需要考虑到线程安全性问题。



ConcurrentHashMap 的作者 Doug Lea对于 ConcurrentHashMap 不允许插入 null 值的回答。

```text
The main reason that nulls aren't allowed in ConcurrentMaps

(ConcurrentHashMaps, ConcurrentSkipListMaps) is that ambiguities that may
be just barely tolerable in non-concurrent maps can't be accommodated. The
main one is that if map.get(key) returns null, you can't detect whether the key
explicitly maps to null vs the key isn't mapped.In a non-concurrent map, you
can check this via map.contains(key),but in a concurrent one, the map might
have changed between calls. 

Further digressing: I personally think that allowingnulls in Maps (also Sets) is an
open invitation for programsto contain errors that remain undetected untilthey
break at just the wrong time. (Whether to allow nulls evenin non-concurrent
Maps/Sets is one of the few design issues surroundingCollections that Josh
Bloch and I have long disagreed about.)

It is very difficult to check for null keys and valuesin my entire application . Would it be easier to declare somewherestatic final Object NULL = new
Object();and replace all use of nulls in uses of maps with NULL?
																	-Dougv
```



（本文完）