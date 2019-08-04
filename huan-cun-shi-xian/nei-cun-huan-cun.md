# 内存缓存



### HashMap/ConcurrentHashMap

在Java中使用本地缓存最简单的方式就是使 HashMap者ConcurrentHashMap，对于只读场景，两者都可以使用，对于缓存更新的场景，可以使用ConcurrentHashMap 来保证数据的一致性，在前面讲到过期策略的时候已经介绍过如何使用，这里不多说了。



### EhCache

是比较老的java开源框架了，在hybris中就使用了ecCache，这里就没有深入研究了。

