# FIFO



FIFO（First InFirstout）：先见先出，淘汰最先近来的数据，新进来的页面最迟淘汰，完全符合队列 ****

![](http://oojclpmc4.bkt.clouddn.com/FIFO.png)

1. 新访问的数据插入FIFO队列尾部，数据在FIFO队列中顺序移动；
2. 淘汰FIFO队列头部的数据； 

**java实现:**

{% hint style="info" %}
这里我们使用LinkedHashMap，只需要重写removeEldestEntry方法可以很方便的实现FIFO策略
{% endhint %}



可以很方便的实现FIFO策略

```java
package cache;import java.util.*;/** * @author xin.leng * @title FIFOCache * @description * @date 2018/6/5 */public class FIFOCache<K,V> extends LinkedHashMap<K,V>{    private final int MAX_CACHE_SIZE;    public FIFOCache(int cacheSize) {        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, false);        MAX_CACHE_SIZE = cacheSize;    }    @Override    protected boolean removeEldestEntry(Map.Entry eldest) {        return size() > MAX_CACHE_SIZE;    }    public static void main(String[] args) {        final Map<Integer, Integer> cache = Collections.synchronizedMap(new FIFOCache<>(2));        cache.put(1,1);        cache.put(2,2);        cache.put(3,3);        System.out.println(cache);    }}
```

{% code-tabs %}
{% code-tabs-item title="输出结果" %}
```bash
{2=2, 3=3}
```
{% endcode-tabs-item %}
{% endcode-tabs %}



