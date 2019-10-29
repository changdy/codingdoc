# ThreadLocal及相关知识

> ThreadLocal也算是面试的一个关键点吧, 自己也对这个比较感兴趣,其牵扯的知识面也比较多,因此写下此文章留作备份

## 相关类

* Thread

  线程类 ,通过静态方法 `Thread.currentThread()`可以获取当前的线程实例,每个Thread有一个`ThreadLocalMap`引用

* ThreadLocalMap

  真正保留映射关系的类,为`ThreadLocal`的内部类, Map的key为弱引用(`WeakReference`)之后的`ThreadLocal` ,其value为`ThreadLocal` 保存的值, 所有的方法

* ThreadLocal

  大部分方法其实由`ThreadLocalMap`实现,但对外只暴漏 一些简单的方法 体现了面想对象的思想

可以先把`ThreadLocalMap`看作是一个普通的`HashMap` 理解三者的关系:

每个线程可以持有一个 `HashMap`实例 , `ThreadLocal`把自身当作key传入到对应的`HashMap`中即可得到对应的value

## 实现细节

### 什么是弱引用,有普通的引用有什么差别?

引用类型的出现是为了完善 java的垃圾回收机制,相当于手动的标记一个类垃圾回收的策略

* 强引用

  举例:`Object instance=new Object()`

  使用最普遍的引用 ,GC的时候并不会回收仍然被强引用的对象 即便OOM

* 软引用

  举例:`SoftReference<Object> reference = new SoftReference<>(new Object());`

  即便我们保留着 `reference ` , GC的时候如果发现内存不足 , 仍然会回收被软引用持有的对象( 注意断句 , 回收的并不是 `SoftReference` , 而是其中持有的对象 , 即上文中对应的是 `Object`对象, 弱引用,幻想引用类似) . 可以用来充当缓存

* 弱引用

  举例:`WeakReference<Object> reference = new WeakReference<>(new Object());`

  GC的时候无论内存是否充裕都会进行回收,可以用来充当缓存

  ```java
  Object instance = new Object();
  WeakReference<Object> weakRef = new WeakReference<>(instance);
  System.gc();
  System.out.println("因为当前有强引用存在,所以此时能返回被引用的对象:" + weakRef.get());
  instance = null;// 解除强引用
  System.gc();
  System.out.println("此时返回null:" + weakRef.get());
  ```

* 幻想引用

  举例:`PhantomReference<Object> reference = new PhantomReference<>(new Object());`

  任何时候都有可能被回收,回收时间不确定

| 引用类型 | 回收条件   | 用途         | 销毁时机       |
| -------- | ---------- | ------------ | -------------- |
| 强引用   | 永不回收   | 获取引用对象 | JVM停止运行    |
| 软引用   | 内存不足时 | 缓存         | 内存不足时销毁 |
| 软引用   | GC时       | 缓存         | GC后销毁       |
| 虚引用   | 随时       | 暂未发现     | 随时销毁       |

另外需要注意 `PhantomReference` ,`SoftReference`,`WeakReference` 都是`Reference`的子类 , 其中有几行特殊的代码

```java
private T referent;         /* Treated specially by GC */
/* -- Constructors -- */
Reference(T referent, ReferenceQueue<? super T> queue) {
    this.referent = referent;
    this.queue = (queue == null) ? ReferenceQueue.NULL : queue;
}
// 这一点困扰了我很久, 当时在想最后难道不都是强引用吗? 但实际上这个强引用被垃圾回收机制特殊处理了,我看了很多关于引用相关的文章也没提到这一点
```

另外和引用相关的还有引用队列 , 可以通过引用队列判断实例是否已经被销毁

### 为什么ThreadLocalMap 要使用弱引用的设计

我们先设想使用`HashMap`代替`ThreadLocalMap ` ,那么在线程的整个生命周期中都会保存`HashMap`的强引用 , 自然其保存的`key` 和`value`都不会被垃圾回收 , 因此如果想借助`HashMap `写出安全的代码 必须采取类似方式

```java
HashMap<String, String> map = new HashMap<>();
// map属于线程,因此一旦创建,只有先销毁线程,才能对map进行GC
public void test() {
    map.put("first", "firstValue");
    map.put("second", "second");
    map.remove("first");// 需要手动remove
    //map.remove("second");// 忘记remove了,糟糕了, 线程存货时GC永远都不会回收了
}
```

在多线程的情况下 有可能remove的时机更不明确 , 并且一般都使用线程池更进一步降低了容错 , 出于这种考虑`ThreadLocalMap `的key使用了弱引用

### ThreadLocalMap内部结构是怎样的

与`HashMap`不同 `ThreadLocalMap`采用的是数组+开放地址法存储数据 , 当数组内部达到阈值(默认是数组大小的2/3)时则进行扩容 . 相比较而言不太适合存储大量数据 ,当然一般情况也不需要过多的使用`ThreadLocal`

### 说好呢弱引用呢,为什么还有可能带来OOM

在系统进行GC时 , 如果`ThreadLocal`的引用仅存在于`ThreadLocalMap`中 , 则会对其进行回收 , 此时对应的`WeakReference`执行`get`方法后也将会得到空对象 , 但是问题在于`ThreadLocalMap`中的`value`仍然是强引用 , 不会被垃圾回收 ,  因此可能造成内存泄漏 所以最好在不使用`ThreadLocal`时调用一下`remove`方法

不过代码本身也做了一些容错 ,在一个`ThreadLocal`实例执行 `get` `set` `remove` 方法时均有可能回收其他`ThreadLocal`实例引起来的内存泄露 , 但这种容错并不稳定 , 尽量还是手动执行`remove`

### 使用场景

对于web开发最常见的就是通过`ThreadLocal` 获取当前请求的一些信息 , 比如 请求的用户信息. 我之前开发的一个项目 就是基于`ThreadLocal`  实现了静态获取 当前登陆状态

### 一些感悟

`ThreadLocal`也算是java面试里面经常被问到的类 和`HashMap` 相似 , 暴露的方法并不多 但其实内部设计都比较巧妙, 比如 在阅读 [阿里架构师浅析ThreadLocal源码——黄金分割数的使用](https://segmentfault.com/a/1190000019658136) 时竟然还碰到了黄金分隔 . 自己了解这些之后也算是 能做到 **知其然 知其所以然** 也学习了一些其他的概念 , 比如弱引用之类的 , 之前一直比较迷糊 , 现在结合`ThreadLocalMap` 也算是有一些新的 , 尤其是在看到 `Reference`源码 `private T referent;  /* Treated specially by GC */` 这一行的时候 颇有一种 豁然开朗的愉悦

### 参考

* [阿里架构师浅析ThreadLocal源码——黄金分割数的使用](https://segmentfault.com/a/1190000019658136)

  强烈建议阅读 , 讲解了很多 内部实现 , 以及如何处理 hash冲突

* [深度揭秘ThreadLocal](https://zhuanlan.zhihu.com/p/34494674)

  比较入门 , 但其实 这里描述的并不准确

  > 不过不用担心，ThreadLocal提供了这个问题的解决方案。
  >
  > 每次操作set、get、remove操作时，ThreadLocal都会将key为null的Entry删除，从而避免内存泄漏。
  >
  
  在不扩容时 并不会遍历数组里的所有元素 , 为了效率只会检测hash碰撞部分是否内存泄漏 . 扩容时虽然会遍历所有的元素, 但数组的长度会再次扩大, hash碰撞的概率会降低 , 内存泄漏也更难被发现 , 并且`Map` 中的数组只会扩容不会缩容 , 因此各位同学还是养成手动`remove`的习惯

* 杨晓峰-.java核心技术36讲-- 强引用、软引用、弱引用、幻象引用有什么区别？

  介绍了几种引用 太文绉绉了 

* [Java四种引用简介](https://segmentfault.com/a/1190000019297979)