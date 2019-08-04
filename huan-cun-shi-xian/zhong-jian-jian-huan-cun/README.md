# 中间件缓存

## 为什么使用中间件缓存

* 集群环境下，每台服务器的cache是不同步的，这样会出问题的，虽然可以使用RMI等使服务器间通信刷新缓存，但是还是不如中间件来的直接。
* 例如使用ConcurrentMap实际上还是是使用JVM的内存来缓存对象的，虽然使用方便，但是会造成大量的内存消耗。

{% hint style="info" %}
常用的缓存中间件有redis和Memcached，这里介绍一下redis缓存
{% endhint %}

{% page-ref page="redis.md" %}



>

|  |  |
| :--- | :--- |
|  |  |

