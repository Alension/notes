# 缓存介绍

## 什么是缓存

* 缓存有很多种，从CPU缓存、磁盘缓存到浏览器缓存等，缓存有很多种，从 CPU缓存、磁盘缓存到浏览器缓存等，也就是将程序或系统经常要使用的对象存在内存或中介中，以便在使用时可以快速调用，也可以避免加载数据或者创建重复的实例，以达到减少系统开销，提高系统效率的目的。

## **为什么使用缓存**

* 我们一般都会把数据存放在关系型数据库中，不管数据库的性能有多么好，一个简单的查询也要消耗毫秒级的时间，这样我们常说的 QPS 就会被数据库的性能所限制，我们想要提高QPS，只能选择更快的存储设备。
* 在日常开发有这样的一种场景：某些数据的数据量不大、不经常变动，但访问却很频繁。受限于硬盘IO性能或者远程网络等原因，每次都直接获取会消耗大量的资源。可能会导致我们的响应变慢甚至造成系统压力过大，这在一些业务上是不能忍的，而缓存正是解决这类问题的神器。

### 

#### 



