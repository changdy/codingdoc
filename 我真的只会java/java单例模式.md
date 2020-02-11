# java单例模式

* 懒汉单例模式

  ```java
  public class Singleton {
  	private Singleton() {}
  	private static Singleton singleton = null;
  	public static Singleton getSingleton() {
  		if(singleton == null){
  			singleton = new Singleton();
  		}
  		return singleton;
  	}	
  }
  ```

* 饿汉单例模式

  ```java
  public class Singleton {
  	private Singleton() {}
  	private static final Singleton singleton = new Singleton();
  	public static Singleton getInstance() {
  		return singleton;
  	}
  }
  ```

* 线程安全的懒汉单例模式

  * 双重锁实现

      ```java
      public class Singleton {
        private Singleton() {}
        private volatile  static Singleton singleton = null;
        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
      }
      ```

      注意 单例对象建议使用 volatile 关键字修饰, 主要是为了避免指令重排序 ,参考 [单例陷阱——双重检查锁中的指令重排问题](https://juejin.im/post/5e41f86b5188254944669a75)
  
  * 内部类实现
  
    ```java
    public class Singleton {
    	private static class LazyHolder {
    		private static final Singleton INSTANCE = new Singleton();
    	}
    	private Singleton() {}
    	public static final Singleton getInstance() {
    		return LazyHolder.INSTANCE;
    	}
    }
    ```
  
  * 枚举类实现
  
    ```java
    public enum Singleton {
    
        INSTANCE;
    
        private EnumSingleton singleton;
    
        //JVM会保证此方法绝对只调用一次
        Singleton() {
            singleton = new EnumSingleton();
        }
    
        public EnumSingleton getInstance() {
            return singleton;
        }
    
        class EnumSingleton {
            private EnumSingleton() {
                System.out.println("===");
            }
        }
    }
    ```
  
    

## 参考

[[Java单例模式篇] 单例设计模式的9种实现](https://blog.csdn.net/SolarL/article/details/88720516)