# Spring 笔记

* 请求流程
  - 客户端（浏览器）发送请求，被DispatcherServlet接受
  - DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。
  - 解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理(适配器模式的体现)
  - HandlerAdapter 会根据 Handler 来调用真正的处理器开处理请求，并处理相应的业务逻辑。
  - 处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是个逻辑上的 View。
  - DispatcherServlet  调用ViewResolver ,并根据逻辑找到view
  - DispaterServlet 把返回的 Model 传给 View（视图渲染）,并进行渲染
  - 把 View 返回给请求者（浏览器）

* Spring IOC 与 AOP

  >ioc的思想最核心的地方在于，资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。第一，资源集中管理，实现资源的可配置和易管理。第二，降低了使用资源双方的依赖程度，也就是我们说的耦合度。     -- 引自知乎用户 [Eric](https://www.zhihu.com/question/23277575/answer/24259844)

  AOP的实现依赖于IOC, IOC则是为了更好的提供AOP .两者相辅相成 . 实际上好多注解都是通过AOP动态代理实现 比如 `@Transaction` `@Async`,包括MyBatis 也是把Mapper 动态代理之后注册到 IOC容器中.
  
  更详细的 可以参考之前写的 [由Mybatis想起的动态代理](由Mybatis想起的动态代理.md)

* Transactional 注解

  默认情况下 Transactional 只回滚 `RuntimeException` 和`Error` 而不会回滚 检查异常