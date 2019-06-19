# JAVA 锁事

>  [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)

![java_lock](../pic/java_lock.png)

## 关系网络补充

* 无锁: 其实对应的是CAS
* 锁阻塞: JDK1.6之前 `synchronized `
* 共享锁: 只能读读之间 , 读写不可以
* 适应性自旋锁: 自适应意味着自旋的时间（次数）不再固定，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定。如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也是很有可能再次成功，进而它将允许自旋等待持续相对更长的时间。如果对于某个锁，自旋很少成功获得过，那在以后尝试获取这个锁时将可能省略掉自旋过程，直接阻塞线程，避免浪费处理器资源
* 偏向锁: 假设只有当前线程访问资源 ; 所以当前线程一直保留着锁直到其他线程尝试竞争偏向锁

## synchronized 的优化

* 1.6之前 synchronized 是使用重量级锁 , 其他线程等待锁释放的时候会进入线程阻塞
* 1.6之后 则引入了 偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化减少锁操作的开销

## synchronized 与 ReentrantLock 区别与联系

### 相同点:

重入锁 , 都是悲观锁, 都可实现非公平锁

### 不同点:

* 实现不同:

  * synchronized 

    java 关键字 . 锁信息存在于对象头部的 Mark Word 区域 .synchronized通过Monitor来实现线程同步，Monitor是依赖于底层的操作系统的Mutex Lock（互斥锁）来实现的线程同步

  * ReenTrantLock

    java 类 . 内部实现了`AbstractQueuedSynchronizer` ,并且可以实现公平锁 ,相比而言ReenTrantLock 更复杂一些 , 加锁和解锁都需要手动 , 异常也不会自动解锁.

## 锁膨胀

* 当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁
* 轻量级锁状态 : 当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

## 其他

* 公平锁 和 非公平锁 应该都是指 重量级锁的时候

## 参考

* [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)

  大部分知识点来源于此文章 , 强烈建议阅读

* [Synchronized 关键字使用、底层原理、JDK1.6 之后的底层优化](https://zhuanlan.zhihu.com/p/47692708)

* [Java synchronized原理总结](https://zhuanlan.zhihu.com/p/29866981)

  锁消除 , 锁粗化 部分摘抄自该文章

* [深度解析 Java 8：JDK1.8 AbstractQueuedSynchronizer 的实现分析（上）](https://www.infoq.cn/article/jdk1.8-abstractqueuedsynchronizer)

  结合ReentrantLock 分析 AQS , 另外可知后面排队的线程未必严格按照顺序执行 , 会结合线程的等待状态