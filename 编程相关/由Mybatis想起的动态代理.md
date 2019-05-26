# 由Mybatis想起的动态代理

其实一直有个疑问,mybatis中只有接口,没有具体的实现类,那我们自动注入的时候,到底注入的是什么东西呢?

## 先说下静态代理

```java
public interface Calculator {
	int add(int a, int b);
}
```

```java
public class CalculatorImpl implements Calculator {
	public int add(int a, int b) {
		int result = a + b;
		return result;
	}
}
```

```java
public class CalculatorProxy implements Calculator {
    private Calculator target;
    public CalculatorProxy(Calculator target) {
        this.target = target;
    }
    @Override
    public int add(int a, int b) {
        System.out.println("add方法开始...");
        int result = target.add(a, b);
        System.out.println("add方法结束...");
        return result;
    }
}
```

上面这个就是静态代理,可以看到需要再内部维护一个原对象的实例,并且对代码侵入性很高,所以一般情况下我们不会使用静态代理模式

## jdk自带的代理类

```java
public class ProxyTest {
    @Test
    public void test() {
        Calculator proxyInstance = (Calculator) Proxy.newProxyInstance(
                this.getClass().getClassLoader(),/*类加载器*/
                new Class<?>[]{Calculator.class},/*让代理对象和目标对象实现相同接口*/
                (proxy, method, args) -> {
                    System.out.println(method.getName() + "方法开始执行...");
                    System.out.println(method.getName() + "方法执行结束...");
                    return 2;
                });
        System.out.println(proxyInstance.add(1, 2));
        System.out.println(proxyInstance.toString());// 类型转换失败
    }
}
```

运行之后输出如下

```
add方法开始执行...
add方法执行结束...
2
```

在上述例子中,虽然没有使用Calculator 的子类,但是却通过`Proxy.newProxyInstance`创建了一个代理类.

该方法有三个入参,分别是 类加载器,可实现的接口,以及一个`InvocationHandler`(反射处理类)接口,不难推测出,实际上的运算是在`InvocationHandler`的实现类中完成的,该接口有一个三入参的方法,并且具有一个返回值,分别对应了被代理的类本身,方法签名,原方法入参以及方法返回值

当然在实际中,一般很少会直接用到原生的方法.大多会使用一些封装的工具 比如[AspectJ](https://mvnrepository.com/artifact/org.aspectj/aspectjrt)

## mybatis与动态代理

经过上面的讲解,我们已经大致了解了动态代理和静态代理,也学会了如何创建一个代理类.那么我们要抛出核心问题,mybatis的接口是如何被spring代理的?

其实很简单,mybatis的接口压根就不是被spring代理的,而是由mybatis自己代理的.对于普通的bean,其实是由spring自己的beanfactory创建的.但是mybatis则有自己的 beanfactory:`MapperFactoryBean` (这个其实就是抽象工厂模式的体现),返回则是`MapperProxy` 实例.所以当我们通过接口执行sql的时候其实是通过MapperProxy来执行sql的.

## mybatis如何与xml的sql建立关系?

- mybatis 把sql标签封装到`SqlSource`中,再把刚刚得到的`SqlSource`连同主键策略,超时时间以及命名空间id统一封装到`MappedStatement`中
- mybais通过自己的`MapperFactoryBean`针对mapper接口创建动态代理对象`MapperProxy` 并添加到容器中
- 当用户进行数据库操作的时候实际上会调用`MapperProxy.invoke`方法,该方法会通过`SqlSession` 以及全类名找到相应的`MappedStatement`
- SqlSession以及执行器

## 其他一些细节

* 类被动态代理之后,直接获取类上注解会爆空指针,可以使用spring 提供的`AnnotationUtils.findAnnotation` 
* mybatis不支持重载的原因:本质上讲是因为XML的命名规则只包含了namespace以及id,没有涉及入参,所以mybatis也就不支持重载
* 如果对一个类进行了aop,则自动注入之后的类是spring提供的代理类.如果没有进行aop功能,则获取的是原始类本身
* 接上条,其实`@Transational` `@Async` 等其实都是通过aop搞个代理类实现的.当你再ioc容器中获取带有这些注解的bean时,其实都已经被替换成了spring 的代理类

## 参考资料

* [面试官问我注解的使用有没有踩过坑](https://zhuanlan.zhihu.com/p/66712661)
* [浅谈JDK动态代理（上）](https://zhuanlan.zhihu.com/p/62534874)
* [阿里面试题：Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的？](https://zhuanlan.zhihu.com/p/61029087)