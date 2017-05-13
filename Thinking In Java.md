# Thinking In Java

> 读java编程思想的一些感悟和总结,记录一些常用的设计模式。

## 设计模式
* P177,183、243适配器设计模式
* P175 策略设计模式
* P122 单利
* P186 工厂模式
* P192、226 迭代器
* P207 模板方法
* P211 命令
## 总结
* 类不允许多继承，而接口可以多继承
* 接口中的域隐式的是static和final
* 内部类的一个优势是有外围类的所有元素的访问权限，因此内部类也应该在外部类中完成初始化（实现原理：内部类构造的时候，会得到外围类的引用）
## 知识点
* P222 几种集合框架的不通
* 集合框架中的for(:)是通过Iterable接口实现。

## 代码块
```java
//P253 异常信息获取
StringWriter trace = new StringWriter();
printStackTrace(new PrintWriter(trace));
trace.toString();
···