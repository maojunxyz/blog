---
title: Redis大Key怎么处理
date: 2023/11/02 20:18
updated: 2023/11/02 20:18
categories:
- [技术, Redis]
tags:
- Redis
---

## 什么是大Key
BigKey称为大Key，通常以Key对应Value的存储大小，或者Key对应Value的数量来进行综合判断。对于大Key也没有严格的定义区分，针对String与非String结构，给出如下定义：

- String：String类型的 Key 对应的 Value 超过 10KB
- 非String结构（Hash，Set，ZSet，List）：Value的数量达到10000个，或者Vaule的总大小为100KB
- 集群中Key的总数超过1亿



## 产生原因

1. 数据结构设置不合理，例如集合中元素唯一时，应该使用Set替换List；
2. 针对业务缺少预估性，没有预见Value动态增长；
3. Key没有设置过期时间，把缓存当成垃圾桶，一直再往里面扔，但是从不处理



## 存在危害

- 数据倾斜

redis数据倾斜分为**数据访问倾斜和数据量倾**斜，会导致该Key所在的数据分片节点CPU使用率、带宽使用率升高，从而影响该分片上所有Key的处理。

**数据访问倾斜**：某节点中key的QPS高于其他节点中的Key

**数据量倾斜**：某节点中key的大小高于其他节点中的Key

- 网络传输延迟

当有大key时，单次操作时间延长，导致网络阻塞。

- 慢查询

严重影响 QPS 、TP99 等指标，对大Key进行的慢操作会导致后续的命令被阻塞，从而导致一系列慢查询。

- CPU压力

内存中数据量过大，就会造成CPU负载过高。单个分片cpu占用率过高，其他分片无法拥有cpu资源，从而被影响。

- 持久化备份

大 key 对持久化也有些影响。fork 操作会拷贝父进程的页表项，如果过大，会占用更多页表，主线程阻塞拷贝需要一定的时间。



## 检测大Key

- redis-cli --bigkeys

  执行redis-cli的--bigkeys选项

- redis-rdb-tools

​	https://github.com/sripathikrishnan/redis-rdb-tools



## 应对策略

- 设置过期时间，尽量过期时间分散，防止大Key同一时间过期
- 把大Key分割成小key来处理，比如存储为String类型的JSON，可以删除不使用的Filed；

例如对象为{"userName":"张三","ciyt":"北京"}，如果只需要用到userName属性，那就定义新对象，只具有userName属性{"userName":"张三"}，精简缓存中数据。

- 搭建redist集群，把key分配到不同的hash slot槽所在的分片上
- 采用压缩算法，利用时间换空间，进行序列化与反序列化。同时也存在缓存数据识别性低的缺点
- 在业务上进行干预，设置阈值。比如用户购物车的商品数量，或者领券的数量，不能无限的增大；



## 删除大Key

### DEL命令

- redis < 4.0 版本时，在主线程中同步删除，删除大Key会阻塞主线程。
- 4.0 版本 < redis< 6.0 版本：引入lazy-free，手动开启lazy-free时，有4个选项可以控制，分别对应不同场景下，是否开启异步释放内存机制：
  - lazyfree-lazy-expire：key在过期删除时尝试异步释放内存
  - lazyfree-lazy-eviction：内存达到maxmemory并设置了淘汰策略时尝试异步释放内存
  - lazyfree-lazy-server-del：执行RENAME/MOVE等命令或需要覆盖一个key时，删除旧key尝试异步释放内存
  - replica-lazy-flush：主从全量同步，从库清空数据库时异步释放内存

开启lazy-free后，Redis在释放一个key的内存时，首先会评估代价，如果释放内存的代价很小，那么就直接在主线程中操作了，没必要放到异步线程中执行

redis >= 6.0 版本：引入lazyfree-lazy-user-del，只要开启了，del直接可以异步删除key，不会阻塞主线程。



### SCAN命令

SCAN命令可以帮助在不阻塞主线程的情况下逐步遍历大量的键，以及避免对数据库的阻塞。

### UNLINK命令

Redis 4.0 提供了 lazy delete (unlink命令) 



## 总结

- 当Hash/Set底层采用哈希表存储（非ziplist/int编码存储）时，并且元素数量超过64个
- 当ZSet底层采用跳表存储（非ziplist编码存储）时，并且元素数量超过64个
- 当List链表节点数量超过64个（注意，不是元素数量，而是链表节点的数量，List的实现是在每个节点包含了若干个元素的数据，这些元素采用ziplist存储）
- refcount == 1 就是在没有引用这个Key时

只有以上这些情况，在删除key释放内存时，才会真正放到异步线程中执行，其他情况一律还是在主线程操作。也就是说String（不管内存占用多大）、List（少量元素）、Set（int编码存储）、Hash/ZSet（ziplist编码存储）这些情况下的key在释放内存时，依旧在主线程中操作。



**参考链接**

- [浅析Redis大Key](https://mp.weixin.qq.com/s/L9ozOPivbM1SjhoTfjHgaw)