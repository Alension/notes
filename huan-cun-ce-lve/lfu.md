# LFU

LFU（Least frequently used）: 最近使用次数最少， 淘汰使用次数最少的数据  

![](http://oojclpmc4.bkt.clouddn.com/LFU.png)

1. 新加入数据插入到队列尾部（因为引用计数为1）；
2. 队列中的数据被访问后，引用计数增加；
3. 当需要淘汰数据时，将计数最小的数据块删除。

java实现

```java
package cache;import java.util.Comparator;import java.util.Map;import java.util.concurrent.ConcurrentHashMap;/** * @author xin.leng * @title LFUCache * @description * @date 2018/6/12 */public class LFUCache<K,V> extends ConcurrentHashMap<K,V>{    private Map<Object,Integer> map = new ConcurrentHashMap<>();    private final int MAX_CACHE_SIZE;    LFU2Cache(int cacheSize){        MAX_CACHE_SIZE = cacheSize;    }    @Override    public V get(final Object key) {         map.put(key,map.get(key)+1);        return super.get(key);    }    @Override    public V put(final K key, final V value) {        if (this.size() == MAX_CACHE_SIZE){            final Entry<Object, Integer> entry = map.entrySet()                                                .stream()                                                .min(Comparator                                                .comparing(Entry::getValue))                                                .orElse(null);            if (entry!=null){             map.remove(entry.getKey());             this.remove(entry.getKey());            }        }        map.put(key,1);        return super.put(key, value);    }    public static void main(String[] args) {        final LFUCache<Object, Object> cache = new LFUCache<>(3);        cache.put(1,1);        cache.put(2,2);        cache.put(3,3);        cache.get(1);        cache.get(1);        cache.get(3);        cache.put(4,4);        System.out.println(cache);    }}
```

{% code-tabs %}
{% code-tabs-item title="输出结果" %}
```text
{1=1, 3=3, 4=4}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

