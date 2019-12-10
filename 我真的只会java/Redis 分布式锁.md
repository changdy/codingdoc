# Redis 分布式锁

由于Redis 性能出众 , 结构简单 , 天然适合用来实现分布式锁 . 一般的策略是当某个key 不存在时, 设置该key对应的value,并返回成功 , 如果存在则返回false , 任务执行完成之后还有可能需要解锁 , 下面是几种常见的情况

## 单机情况

最简单的 可以使用 `setnx` (set if not exist)实现 , 但`setnx` 并不能原子性的设置过期时间 , 存在一定的解锁失败可能

此时可以使用`set` 搭配 `ex`  `nx` 实现

```Redis
set name p7+ ex 100 nx
```

spring boot 2.2+ 以上也提供了类似api

```java
new StringRedisTemplate().opsForValue().setIfPresent("name","value",20,TimeUnit.SECONDS);
// Boolean setIfPresent(K key, V value, long timeout, TimeUnit unit)
```

## 多机情况

但是单机情况下的操作并不适用于多机情景, 不过好在redis作者提出了一个更加高级的基于redis实现的分布式锁——RedLock.

大概意思是 客户端向集群发送锁请求 , 如果有一半以上的机器成功加锁则认为锁成功,否则失败.并且失败之后需要把加锁成功的节点解锁

对于java其实Redisson已经进行了封装,基本上也能开箱即用

```java
RedissonRedLock redLock = new RedissonRedLock(lock1, lock2, lock3);
boolean isLock = redLock.tryLock(500, 30000, TimeUnit.MILLISECONDS);
```

## 参考

* [Redis SET命令](https://redis.io/commands/set)

* [分布式锁-redLock And Redisson](https://blog.csdn.net/stone_yw/article/details/88062938)
* [Redisson实现Redis分布式锁的N种姿势](https://www.jianshu.com/p/f302aa345ca8)