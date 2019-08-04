# redis



### redis的优势

* 异常快 - Redis非常快，每秒可执行大约110000次的设置\(SET\)操作，每秒大约可执行81000次的读取/获取\(GET\)操作。
* 支持丰富的数据类型-Redis支持开发人员常用的大多数数据类型，例如列表，集合，排序集和散列等等。这使得Redis很容易被用来解决各种问题，因为我们知道哪些问题可以更好使用地哪些数据类型来处理解决。
* 操作具有原子性-所有Redis操作都是原子操作，这确保如果两个客户端并发访问，Redis服务器能接收更新的值。
* 多实用工具 - Redis是一个多实用工具，可用于多种用例，如：缓存，消息队列\(Redis本地支持发布/订阅\)，应用程序中的任何短期数据，例如，web应用程序中的会话，网页命中计数等。

### 实例：缓存hybris枚举数据

```java
final Type typeClass = new TypeToken<List<EnumData>>(){}.getType();final List<EnumData> data = hepRedisQueryCacheService.getEnumFromRedis(type, typeClass);if (data != null) {    return ResultUtil.success(data,data.size());}LOG.debug("enumToData zhFlag true");resultDTO = enumToDataService.enumToData(type, Locale.CHINESE);hepRedisQueryCacheService.cacheEnum2Redis(type, resultDTO.getData());
```

{% hint style="info" %}
 这是一段很简单的前台查询枚举redis缓存更新策略的代码，先从redis里面取数据，如果能找到数据就直接返回，不能找到就缓存到redis中，接下来就是。对于缓存失效，redis可以很方便的在缓存数据的时候设置缓存时间，另外对于hybris枚举数据，可以想到更改items文件或者直接在hmc等会修改到枚举表的操作都会照成redis缓存和实际数据不一致，在hybris中，可以实现监听器来完成缓存数据的失效
{% endhint %}

```java
package com.hand.ec.listeners.redis;import com.hand.ec.redis.services.HepRedisQueryCacheService;import com.hand.ec.redisson.util.HepRedisKeyUtil;import de.hybris.platform.core.HybrisEnumValue;import de.hybris.platform.servicelayer.model.ModelService;import de.hybris.platform.tx.AfterSaveEvent;import de.hybris.platform.tx.AfterSaveListener;import org.apache.log4j.Logger;import org.springframework.beans.factory.annotation.Autowired;import org.springframework.context.ApplicationListener;import org.springframework.context.event.ContextRefreshedEvent;import org.springframework.stereotype.Component;import org.springframework.util.ObjectUtils;import java.util.Collection;/** * @author xin.leng * @title HepRedisCacheListener * @description * @date 2018/5/18 */@Component(value = "hepRedisCacheListener")public class HepRedisCacheListener implements AfterSaveListener , ApplicationListener<ContextRefreshedEvent> {    private Logger LOG = Logger.getLogger(HepRedisCacheListener.class);    @Autowired    private ModelService modelService;    @Autowired    private HepRedisQueryCacheService hepRedisQueryCacheService;    @Override    public void afterSave(final Collection<AfterSaveEvent> collection) {        if (ObjectUtils.isEmpty(collection)){            return;        }        collection.forEach(event->{            Object o = null;            try {                o = modelService.get(event.getPk());            } catch (Exception e) {                //ignore            }            if (o!=null && o instanceof HybrisEnumValue){                HybrisEnumValue hybrisEnumValue = (HybrisEnumValue)o;                hepRedisQueryCacheService.delEnumFromRedis( hybrisEnumValue.getType());            }        });    }    @Override    public void onApplicationEvent(final ContextRefreshedEvent contextRefreshedEvent) {        LOG.debug(">>>>>>>>>>>>>contextRefreshedEvent>>>>>>>>>>>>>");        hepRedisQueryCacheService.delFromRedisByKey(HepRedisKeyUtil.CACHE_ENUM_QUERY_KEY);    }}
```

{% hint style="info" %}
对于在items文件更改枚举，可以实现ApplicationListener在spring启动时清空redis里的所有枚举数据，此外，如果在hmc或代码里面修改了枚举，这时就需要监听afterSave事件了,注意这里不是使用hybirs里面的model拦截器来实现，因为如果有两个并发操作，一个是更新操作，另一个是查询操作，更新操作删除缓存后，查询操作没有命中缓存，先把老数据读出来后放到缓存中，然后更新操作更新了数据库。于是，在缓存中的数据还是老的数据，导致缓存中的数据是脏的，这也是上面的缓存更新策略中的更新操作为什么先更新数据库再让缓存失效。
{% endhint %}

{% hint style="warning" %}
上面的代码有一个问题，有很多查询接口要缓存的话，从redis查询、判断、缓存到redis这些代码每个接口里面都需要写到复用性太差了，到这里我们容易想到的是用spring的aop来实现这些代码的复用，其实spring给我们提供了更加方便的方法去实现这一目的，使用spring cache可以轻松实现。关于spring cache + redis这里就不详细描述了， 可以看这篇博文 [https://www.cnblogs.com/chenkeyu/p/8028781.html](https://www.cnblogs.com/chenkeyu/p/8028781.html)
{% endhint %}

{% hint style="warning" %}
有一点需要注意的在我的个人经验看来对于太过复杂的查询比如有很多个表关联的不适合用缓存，对于一个查询这么多个表做监听的话会影响效率的，还有就是一开始我们提到的缓存的数据一般是数据量不大、不经常变动，但访问却很频繁，如若不是就不建议用缓存了。
{% endhint %}

