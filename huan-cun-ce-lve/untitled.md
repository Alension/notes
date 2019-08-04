# LRU



LRU（Least recentlyused）:最近最少使用，淘汰最近不使用的数据  

![](http://oojclpmc4.bkt.clouddn.com/LRU.png)



1. 新数据插入到链表头部；
2. 每当缓存命中（即缓存数据被访问），则将数据移到链表头部；
3. 当链表满的时候，将链表尾部的数据丢弃。

java实现

{% hint style="info" %}
对于LRU策略，LinkedHashMap也提供了很方便的实现方式，只需将构造方法的accessOrder参数传true，链表就能按照访问时间进行排序
{% endhint %}

```text
package cache;import java.util.Collections;import java.util.LinkedHashMap;import java.util.Map;/** * @author xin.leng * @title LRUCache * @description * @date 2018/6/12 */public class LRUCache<K, V> extends LinkedHashMap<K, V> {    private final int MAX_CACHE_SIZE;    public LRUCache(int cacheSize) {        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);        MAX_CACHE_SIZE = cacheSize;    }    @Override    protected boolean removeEldestEntry(Map.Entry eldest) {        return size() > MAX_CACHE_SIZE;    }    public static void main(String[] args) {        final Map<Integer, Integer> cache = Collections.synchronizedMap(new LRUCache<>(3));        cache.put(1, 1);        cache.put(2, 2);        cache.put(3, 3);        cache.get(1);        cache.put(4, 4);        System.out.println(cache);    }}
```

{% code-tabs %}
{% code-tabs-item title="输出结果：" %}
```text
{3=3, 1=1, 4=4}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
对于FIFO和LRU我这里测试都是用的Collections.synchronizedMap\(\)来确保线程安全，这是一个比较简单的办法，但是效率并不好最好的。我们知道ConcurrentHashMap使用锁的分段来确保现在安全的同时而不用对每一个非 CAS操作进行加锁很好的提升了效率，但是我在jdk中并没有找到LinkedHashMap相应的同步类，如何实现：[https://blog.csdn.net/Nenny/article/details/8869527](https://blog.csdn.net/Nenny/article/details/8869527)[/](http://jm.taobao.org/2011/01/18/689/)
{% endhint %}



