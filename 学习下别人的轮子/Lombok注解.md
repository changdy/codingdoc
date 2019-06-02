* @Data

  相当于`@get` `@set` `@equals` `@canEqual` `@hashCode` `@toString`的累加

* @Logj系列注解

  支持 @CommonsLog  @Log  @Log4j  @Log4j2  @Slf4j  @XSlf4j  

* @AllArgsConstructor @NoArgsConstructor @RequiredArgsConstructor

  全参,无参的构造方法,以及入参为@NonNull 注解的或者带有 final 修饰的成员变量的构造方法。

* @EqualsAndHashCode

  生成 equals、canEqual、hashCode 方法

* @NonNull

  为空的时候抛出空指针,可以在方法,类名,等上使用

* @Cleanup

  用于可以可以关闭的变量前

* @Value

  和@Data类似,但是没有get方法

* @SneakyThrows

  方法注解,讲方法中代码使用try-catch包裹起来,并可以使用注解的value往上抛

* @Synchronized

  锁,可以使用到类方法,或者实例方法上