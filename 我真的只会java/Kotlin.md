# Kotlin学习

## 属性

* 空对象转非空 采用了`Elvis `风格 

  ```kotlin
  // 强转
  var nonull=nullAble!!
  // 遇空转
  var nonull=nullAble ?:""
  // 抛异常
  val nonull = nullAble ?: throw IllegalStateException("Email is missing!")
  // if not null 执行代码
  nullAble?.let {
      // 代码会执行到此处, 假如nullAble不为null
  }
  // nullAble为null或transformValue 结果为null都将返回 defaultValueIfValueIsNull
  nullAble?.let { transformValue(it) } ?: defaultValueIfValueIsNull
  // 如果 `person` 或者 `person.department` 其中之一为空，都不会调用该函数：
  person?.department?.head = managersPool.getManager()
  // 同时需要注意运算优先级
  i = str?.length?:0 + i // 其实等价于 str?.length?: (0 + i)
  ```

* 类型转换

  ```kotlin
  // kotlin支持通过关键字`is`自动转换
  fun getStringLength(obj: Any): Int? {
      if (obj !is String) return null
      // `obj` 在这一分支自动转换为 `String`
      return obj.length
  }
  ```

* 延迟属性

  ```kotlin
  // lazy之后的代码块 只有再第一次调用的时候执行
  val lazyValue: String by lazy {
      println("computed!")     // 第一次调用输出，第二次调用不执行
      "Hello"
  }
  ```

* with 用法

  ```kotlin
  class Turtle {
      fun penDown()
      fun penUp()
      fun turn(degrees: Double)
      fun forward(pixels: Double)
  }
  
  val myTurtle = Turtle()
  with(myTurtle) { // 画一个 100 像素的正方形
      penDown()
      for(i in 1..4) {
          forward(100.0)
          turn(90.0)
      }
      penUp()
  }
  ```

* 可观察属性 Observable

  ```kotlin
  class User {
      var name: String by Delegates.observable("22") { _, old, new ->
          println("$old -> $new")
      }
  }
  
  fun main() {
      User()
              .also { it.name = "name" }
              .also { println(it.name) }
  }
  ```

* 避免关键字

  ```kotlin
  // Utils.is() // 编译不通过, 因为is 是kotlin的关键字
  Utils.`is`()
  ```

  

## kotlin类

### 嵌套类

嵌套类 就是在一个类里面声明了另外一个类

### 内部类

和嵌套类大体相似, 但是内部类 隐式的有一个对外部类的对象的引用 

### 匿名对象

```kotlin
fun foo() {
    val adHoc = object {
        var x: Int = 0
        var y: Int = 0
    }
    print(adHoc.x + adHoc.y)
    // adHoc 只能在作用域内使用
}
```

### 构造方法

```kotlin
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    init {println("init method")}
}
// init 以及赋值操作是根据顺序来
class Person(val firstName: String, val lastName: String) 
// kotlin可以这样声明构造函数
class Constructors {
    init {println("Init block")}
    constructor(i: Int) {println("Constructor")}
}
// 先打印 `Init block` 因为 `init` 其实算默认构造方法的一部分
```

### Kotlin Class类文件

```kotlin
class Person {companion object {}}
fun main(args: Array<String>) {
    // kotlin 的class
    val kClass: KClass<Person> = Person::class
    // 实例的类相似,只不过加了 out, 下同 不在累述
    val instanceKClass: KClass<out Person> = Person()::class
    // java 的 class
    val javaClass: Class<Person> = Person::class.java
    // 这边其实是伴生类的
    val companionClass=Person.javaClass
}
```

## List

> 提到的numbers 都是  (1..10).toList() ,大部分方法由迭代器提供

### 初始化方法

```kotlin
// 使用区间 
val toList = (1..2).toList() + 0 - 2
// 使用序列
println(generateSequence(1) { it + 2 }.take(5).toList())
// 指定构造器
ArrayList(listOf(1, 2, 3, 4, 5))
```

### 带有index 的迭代

```kotlin
// 带有index的 迭代 同时解构
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```
### flatMap & flatten

```kotlin
data class StringContainer(val values: List<String>)
val containers = listOf(
    StringContainer(listOf("one", "two", "three")),
    StringContainer(listOf("seven", "eight"))
)
println(containers.flatMap { it.values })

val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
println(numberSets.flatten())
```

### 映射成Map

```kotlin
val list = listOf("one", "two", "three", "four")
list.associateWith { it.length }
list.associateBy { it.first().toUpperCase() }
list.associateBy(keySelector = { it.first().toUpperCase() }
        , valueTransform = { it.length })
```

### filter过滤

```kotlin
// 过滤并累加
var temp = mutableListOf<Int>()
numbers.filterTo(temp){it>5}
// 根据类型过滤
val allObject = listOf(null, 1, "two", 3.0, "four")
allObject.filterIsInstance<String>().forEach { println(it.toUpperCase()) }
```

### 字符串累加

```kotlin
// 字符串拼接,其实调用的是joinTo方法=>numbers.joinTo(StringBuffer(""))
println(numbers.joinToString(separator = " | ", prefix = "start: ")
// transform key
println(numbers.joinToString { "Element: $it"})
```

### 分组 & 分区

```kotlin
// 入参类似于 Iterable.associateBy
numbers.groupBy({ it.toString().first() }, { it })
// groupingBy 讲list 转换成 Grouping
numbers.groupingBy { it.toString().first() }.eachCount()
// aggregate方法最全 {1=1:3-4-5, 2=2:6-7-8, 3=3:9}
numbers.groupingBy { it / 3 }.aggregate {
    key, accumulator: StringBuilder?, element, first ->
    if (first) // first element
        StringBuilder().append(key).append(":").append(element)
    else
        accumulator!!.append("-").append(element)
}
// 分区 执行结构
val (match, rest) = numbers.partition { it > 3 }
```

### slice ,take ,drop

```kotlin
// slice 可以用index取元素
println(numbers.slice(1..3))
println(numbers.slice(setOf(3, 5, 0)))
// take为取前N个元素,类似stream的limit drop相反
println(numbers.take(3))
// takeWhile 并不一定遍历整个集合,会自动截至到第一个不符合的元素 ,注意和filter的不同
val tempList = "one two three four five six".split(" ")
tempList.takeWhile { !it.startsWith('f') }// one, two, three
// dropWhile 与takeWhile 相反
tempList.dropWhile { it.length == 3 }//three, four, five, six
```

### Chunked & Windowed & zipWithNext

Chunked  是 每N个元素组成一个新集合 ,可以用来限制单个集合的大小

```kotlin
// chunked 实际上是借助 windowed(size, size, partialWindows = true) 实现
println(numbers.chunked(3))
println(numbers.chunked(3) { it.sum() })  // 分组之后还可以再次聚合
```

Windowed 

```kotlin
// size 分组大小,step 每次步进,partialWindows 是否保留不饱满分区
numbers.windowed(size=3, step = 2, partialWindows = true)
```

zipWithNext 元素和下一个进行聚合

```kotlin
val strNumbers = listOf("one", "two", "three", "four", "five")
println(strNumbers.zipWithNext())
println(strNumbers.zipWithNext() { s1, s2 -> s1.length > s2.length})
```

### 排序

```kotlin
data class TempData(var score: Int, var name: String)

val data = listOf(TempData(1, "C"), TempData(2, "B"), TempData(1, "A"))
val javaComparator = Comparator.comparing(TempData::score).thenComparing(TempData::name)
println(data.sortedWith(javaComparator))
println(data.minWith(javaComparator)) // TempData(score=1, name=A)
println(data.minBy { it.score }) // TempData(score=1, name=C)
// 这里反而不如java 优雅了
val kotlinComparator = Comparator { i: TempData, j: TempData -> i.score.compareTo(j.score) }
        .thenComparator { i: TempData, j: TempData -> i.name.compareTo(j.name) }
```

### reduce 与fold 差异

reduce 限定了返回值必须是集合泛型本身,并且初始值为第一个元素. 而fold 并不限定返回类型,第一个值可以需要手动指定

## 数组

Kotlin 中的数组是一个拥有泛型的类，创建函数也是泛型函数，和集合数据类型一样 写法如下：

```kotlin
🏝️
val strs: Array<String> = arrayOf("a", "b", "c")
            👆              👆
```

## 方法

### with

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}
val myTurtle = Turtle()
with(myTurtle) { // 画一个 100 像素的正方形
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

### 单表达式函数

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    else -> throw IllegalArgumentException("Invalid color param value")
}
fun transform(color: String): Int {
    return when (color) {
        "Red" -> 0
        else -> throw IllegalArgumentException("Invalid color")
    }
}
```

### 表达式

```kotlin
// try 类型
val result = try {
    count()
} catch (e: ArithmeticException) {
    throw IllegalStateException(e)
}
// when 表达式
val v = when (color) {
    "Red" -> 0
    else -> throw IllegalArgumentException("Invalid color")
}
// if 另外注意kotlin没有三目运算
val result = if (param == 1) {
    "one"
} else if (param == 2) {
    "two"
} else {
    "three"
}
```

### Lambda 表达式

```kotlin
fun main() {
    // 其实相比较java的 函数接口貌似也没简单很多,不过的确灵活了很多
    val setFun: TestEntity.(String) -> Unit = { r = it }
	var setFun2 = fun TestEntity.(other: String) { r = other }
    val testEntity = TestEntity("1", "2", "4")
    setValue("R") { testEntity.r = it }// 拖尾 lambda 表达式
    setValue("s", { x -> testEntity.s = x })
    // 下面的 function 其实不用声明类型
    val function: (TestEntity) -> String = { x: TestEntity -> x.r }
    println(getValue(testEntity, function))
    println(getValue(testEntity) { it.r })
    println(getValue(testEntity, fun(t: TestEntity) = t.r))
}
fun setValue(value: String, function: (String) -> Unit) {
    function.invoke(value)
}
fun getValue(entity: TestEntity, function: (TestEntity) -> String): String {
    return function.invoke(entity)
}
```

## 其他杂项

### 标签与跳转

kotlin中可以设置标签供break以及Continue 

* 显示设置标签

  ```kotlin
  loop@ for (i in 1..100) {
      println(i)
      for (j in 1..100) {
          if (j == 2)
          // break  此时的break仍然会打印所有的i值
          break@loop // 此时只会打印 第一个i值
      }
  }
  ```

*  隐式标签 

  ```kotlin
  fun foo() {
      listOf(1, 2, 3, 4, 5).forEach {
          if (it == 3) return@forEach // 局部返回到该 lambda 表达式的调用者，即 forEach 循环
          print(it)
      }
      print(" done with implicit label")
  }
  ```

### 操作符重载

```kotlin
a > b	// 等效于 a.compareTo(b) > 0 
a in b // b.contains(a)
a(i, j)	// a.invoke(i, j)
```

### 数组初始化

```kotlin
Array(5) { i -> (i * i) }.also { print(Arrays.toString(it)) }
```

### Map初始化

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
println(map["key"])
println(map.get("key"))
val filteredMap = map.filter { (key, value) -> key.endsWith("1") && value > 10}
```

### 作用域函数

| Function | Object reference | Return value   | Is extension function                        |
| :------- | :--------------- | :------------- | :------------------------------------------- |
| `let`    | `it`             | Lambda result  | Yes                                          |
| `run`    | `this`           | Lambda result  | Yes                                          |
| `run`    | -                | Lambda result  | No: called without the context object        |
| `with`   | `this`           | Lambda result  | No: takes the context object as an argument. |
| `apply`  | `this`           | Context object | Yes                                          |
| `also`   | `it`             | Context object | Yes                                          |

### 额外注解

* JvmOverloads

  Kotlin提供了 @JvmOverloads注解，使用这个注解后，会让Kotlin编译器按照从左向右的顺序依次为每一个可选参数生成重载 , 可以在java代码中也方便使用

## 心得  以及引用

* java中默认方法以及属性类都可以被子类覆盖,但是kotlin中只有额外声明才能被子类修改
* kotlin迭代器中 没有peek ,可以任何时候使用 `also` 或者 `forEach`
* [[译]掌握Kotlin中的标准库函数: run、with、let、also和apply](https://zhuanlan.zhihu.com/p/37085876)
* [如何在 Java 和 Kotlin 之间进行互操作](https://juejin.im/post/5de6474a518825122b0f652b)
* [正确使用Kotlin注解，兼容Java代码](https://juejin.im/post/5dcc246f6fb9a02b112aeb5f#heading-2)
* [理解Kotlin语言独有的位置注解，让注解控制更精准](https://juejin.im/post/5dce9cb55188251845221090)
* [【码上开学】Kotlin 里那些「不是那么写的」](https://juejin.im/post/5d5c80555188256522360a3d)
* [【码上开学】Kotlin 里那些「更方便的」](https://juejin.im/post/5d5fccb8f265da03925a4f90)
* [kotlin 中::class 、class.java、javaClass、javaClass.kotlin区别](https://blog.csdn.net/a568478312/article/details/80718028)
