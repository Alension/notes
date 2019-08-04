# 更新策略

最常见的缓存更新策略是 Cache Aside Pattern :

* 失效：应用程序先从cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中。
* 命中：应用程序从 cache 中取数据，取到后返回。
* 更新：先把数据存到数据库中，成功后，再让缓存失效。  

了解更多跟新策略：[https://coolshell.cn/articles/17416.html](https://coolshell.cn/articles/17416.html)

