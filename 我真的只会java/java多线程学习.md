# java多线程学习

> 2017/08/19
## 多线程实现方法

* 继承Thread类

  其实Thread类也是实现了 Runnable接口

* 实现Runnable接口

  需要注意即便实现Runnable接口也需要通过 `new Thread(runnable).satrt()`来启动,通过run()方法启动,线程变为同步

* 也可使用Callable实现带有返回值

  ```java
  public class MyCallable implements Callable<String> {
      @Override
      public String call() throws Exception {
          Date dateTmp1 = new Date();
          Thread.sleep(2000);
          Date dateTmp2 = new Date();
          long time = dateTmp2.getTime() - dateTmp1.getTime();
          return "任务返回运行结果,当前任务时间" + time + "毫秒";
      }
  }
  ```

  ```java
  public class CallableTest {
      public static void main(String[] args) throws Exception {
          System.out.println("----程序开始运行----");
          int taskSize = 5;
          // 创建一个线程池
          ExecutorService pool = Executors.newFixedThreadPool(taskSize);
          // 创建多个有返回值的任务
          List<Future> list = new ArrayList<>();
          for (int i = 0; i < taskSize; i++) {
              Callable<String> c = new MyCallable();
              // 执行任务并获取Future对象
              Future f = pool.submit(c);
              list.add(f);
          }
          // 关闭线程池
          pool.shutdown();
          // 获取所有并发任务的运行结果
          Date date1 = new Date();
          for (Future f : list) {
              // 从Future对象上获取任务的返回值，并输出到控制台
              f.get();
          }
        	Date date2 = new Date();
        	System.out.println("程序运行时间" + (date2.getTime() - date1.getTime()));
      }
  }
  ```

## Thread类方法

* currentThread 静态

  返回对当前正在执行的线程对象的引用

* sleep 静态

  当前线程暂停一定时间,转为阻塞状态,不考虑线程优先级

* yield 静态

  让出cpu权限给更同级或更高优先级,线程回归就绪状态

* join

  执行该方法后,主线程会等待子线程完成

* interrupt

  并不会真正的终止掉该线程,而是通知并标志,具体内容可以参考头部链接

* stop resume

  因为这些方法线程不安全,所以被列为过时

## ThreadLocal

- Thread类中有一个ThreadLocalMap成员变量类(一个定义在ThreadLocal类中的内部类),它是一个Map,key是ThreadLocal实例对象
- 当为ThreadLocal类的对象set值时,首先获得当前线程的ThreadLocalMap,然后以ThreadLocal类的对象为key,设定value.get值时则类似.
- ThreadLocal变量的活动范围为线程,是该线程"专有的,独自霸占"的,对该变量的所有操作均由该线程完成！也就是说,ThreadLocal 不是用来解决共享对象的多线程访问的竞争问题的,因为ThreadLocal.set() 到线程中的对象是该线程自己使用的对象,其他线程是不需要访问的,也访问不到的.当线程终止后,这些值会作为垃圾回收.
- 由ThreadLocal的工作原理决定了：每个线程独自拥有一个变量,并非是共享的
- ThreadLocal的key为弱引用,内存清理的时候可能会造成key为null,但是value仍然存在的情况,可能由此引发内存泄露,当不需要ThreadLocal对象时需要调用下 `remove`方法

## 面试考点

* sleep和wait 的区别?
  sleep()是Thread的静态方法,调用此方法会让当前线程暂停执行指定的时间,将CPU让给其他线程,但是对象的锁依然保持,因此休眠时间结束后会自动恢复.wait()是Object类的方法,调用对象的wait()方法导致当前线程放弃对象的锁(线程暂停执行),进入对象的等待池(wait pool),只有调用对象的notify()方法(或notifyAll()方法)时才能唤醒等待池中的线程进入等锁池(lock pool),如果线程重新获得对象的锁就可以进入就绪状态

* 如何在两个线程间共享数据?

  可以通过共享对象来实现并借助wait和notify的方法实现

* 为什么wait, notify 和 notifyAll这些方法不在thread类里面?

  JAVA提供的锁是对象级的而不是线程级的,每个对象都有锁.这样Java的每一个类都有用于线程间通信的基本方法;如果wait()方法定义在Thread类中,线程正在等待的是哪个锁就不明显
* java 生产消费者解决方案

  * 采用synchronized锁以及wait notify方法实现
  * 采用Lock锁以及await和signal方法是实现
  * 采用BlockingQueue实现
  * 参考 : [用Java实现生产者消费者的几种方法](https://zhuanlan.zhihu.com/p/20300609)

## wait和notify用法

```java
class ThreadA extends Thread {
    public void run() {
        synchronized (this) {
            System.out.println(Thread.currentThread().getName() + " call notify()");
            // 唤醒当前的wait线程
            this.notify();
            System.out.println("执行 notify(),时间: " + LocalTime.now());
            try {
                sleep(1000);
            } catch (InterruptedException e) {}
        }
    }
}
public class WaitTest {
    public static void main(String[] args) {
        ThreadA t1 = new ThreadA();
        synchronized (t1) {
            try {
                t1.start();
                Thread.sleep(1000);
                System.out.println("main");
                t1.wait();  //  不是使t1线程等待，而是当前执行wait的线程等待
                System.out.println("continue:" + LocalTime.now());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

如上最后的打印结果是

```
main
Thread-0 call notify()
执行 notify(),时间: 10:29:16.400
continue:10:29:17.400
```

## 关键字

* synchronized

  主要是锁机制

  ```java
  public class SyncThread implements Runnable {
      private static Integer count;

      public SyncThread() {
          count = 0;
      }
  	// 注意加synchronized static和不加的区别
    	// public void method() {
      public synchronized static void method() {
          for (int i = 0; i < 5; i++) {
  			System.out.println(Thread.currentThread().getName() + ":" + (count++));
          }
      }

      @Override
      public void run() {
          method();
      }

      public static void main(String[] args) {
          SyncThread syncThread1 = new SyncThread();
          SyncThread syncThread2 = new SyncThread();
          Thread thread1 = new Thread(syncThread1, "SyncThread1");
          Thread thread2 = new Thread(syncThread2, "SyncThread2");
          thread1.start();
          thread2.start();
      }
  }
  ```

  ```java
  //下面两种的写法功能一样
  public synchronized void method(){
     // todo
  }
  public void method(){
     synchronized(this) {
        // todo
     }
  }
  ```

* volatile
  * 使用volatile关键字会强制将修改的值立即写入主存
  * 修改关键字时,会导致其他线程的该关键字缓存无效(反映到硬件层的话,就是CPU的L1或者L2缓存中对应的缓存行无效),因此其他线程会从主存读取新值
  * 观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现,加入volatile关键字时,会多出一个lock前缀指令----深入理解Java虚拟机

*  ThreadLocal

   每个线程保留一份该类的拷贝,互相之间并不影响


## 并发三个问题

* 原子性

  要么全部执行,要么全部不执行

* 可见性

  一个线程修改值其他线程能够得知

* 有序性

  确保代码执行的顺序正确

## 内存模型概念相关

计算机在执行程序时,每条指令都是在CPU中执行的,而执行指令过程中,势必涉及到数据的读取和写入.由于程序运行过程中的临时数据是存放在主存(物理内存)当中的,这时就存在一个问题,由于CPU执行速度很快,而从内存读取数据和向内存写入数据的过程跟CPU执行指令的速度比起来要慢的多,因此如果任何时候对数据的操作都要通过和内存的交互来进行,会大大降低指令执行的速度.因此在CPU里面就有了高速缓存

![cache_thread](../pic/thread_cache.jpg)

主要问题:

数据存放在主存(物理内存)中,而指令运行在CPU中,为避免IO时间过长,因此CPU中有高速缓存,而在并发的情况下指令可能运行在不同的CPU中,因此同一个对象在各个高速缓存和物理内存中的值可能不一样

## 参考资料

* [Java多线程干货系列](http://www.jianshu.com/p/8a04b5ec786c)
* [Java里一个线程调用了Thread.interrupt()到底意味着什么？](https://www.zhihu.com/question/41048032)
* [Java并发编程：volatile关键字解析](http://www.cnblogs.com/dolphin0520/p/3920373.html)