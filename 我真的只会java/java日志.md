# java学习日志
> 2017-03-06 09:29 

* SimpleDateFormat参数 *(不推荐)*
	* 实例化SimpleDateFormat的时候一定要注意 ,**区分大小写**
	* y代表年 , 如yyyy
	* M代表月 , 如MM
	* d代表日期 , 如dd
	* H代表时间 , 如HH
	* h代表12小时制时间 , 如hh
	* m代表分钟 , 如mm
	* s代表秒 , 如ss
	* S代表毫秒 , 如SSS
	* 常用的方法如下
		```java
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//2017-02-08 09:26:54
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-M-d H:m:s");//2017-2-8 9:25:45
		```
	
* java8日期新特性
	* LocalDate
		```java
		// 取当前日期 : 
		LocalDate today = LocalDate.now(); // -> 2014-12-24
		// 根据年月日取日期 , 可以不补零 : 
		LocalDate crischristmas = LocalDate.of(2014, 12, 25);
		// 根据字符串取 , 严格按照ISO yyyy-MM-dd验证 , 需要补零
		LocalDate endOfFeb = LocalDate.parse("2014-02-28"); 
		// 取本月第1天 : 
		LocalDate firstDayOfThisMonth = today.with(TemporalAdjusters.firstDayOfMonth()); // 2014-12-01
		// 取本月第2天 : 
		LocalDate secondDayOfThisMonth = today.withDayOfMonth(2); // 2014-12-02
		// 取本月最后一天 , 再也不用计算是28,29,30还是31 : 
		LocalDate lastDayOfThisMonth = today.with(TemporalAdjusters.lastDayOfMonth()); // 2014-12-31
		// 取下一天 : 
		LocalDate firstDayOf2015 = lastDayOfThisMonth.plusDays(1); // 变成了2015-01-01
		// 取2015年1月第一个周一 , 这个计算用Calendar要死掉很多脑细胞 : 
		LocalDate firstMondayOf2015 = LocalDate.parse("2015-01-01").with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY));
		```
	* LocalTime
		```java
		//日期转换
        LocalTime now = LocalTime.of(0, 0, 0); // 00:00:00
        LocalDate today = LocalDate.now(); // -> 2014-12-24
        LocalDateTime dateTime1 = now.atDate(LocalDate.now());
        LocalDateTime dateTime2 = today.atTime(now);
        LocalDate localDate = dateTime2.toLocalDate();
        LocalTime localTime = dateTime1.toLocalTime();
		```
	* LocalDateTime
		```java
        LocalTime now = LocalTime.of(0, 0, 0); // 00:00:00
        LocalDate today = LocalDate.now(); // -> 2014-12-24
        LocalDateTime dateTime1 = now.atDate(LocalDate.now());
        LocalDateTime dateTime2 = today.atTime(now);
		LocalDateTime.parse("2007-12-03T10:15:30")
		//默认的是ISO_LOCAL_DATE_TIME, 因此还需要自己定义DateTimeFormatter
		DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
		```
	* Instant
	* ZonedDateTime
	* 新的日期格式化工具
		```java
		String goodFriday = "04 18 2014";
		try {
			//MM需要补零 ,M则是可以补零 , 也可以不补零
		    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM dd yyyy");
		    LocalDate holiday = LocalDate.parse(goodFriday, formatter);
		    String landing = holiday.format(formatter);
		    System.out.printf(goodFriday + holiday + landing);
		} catch (DateTimeParseException ex) {
		    System.out.printf("%s is not parsable!%n", goodFriday);
		    ex.printStackTrace();
		}
		```
	* 日期转换
		```java
		//Date转LocalDateTime
		Instant instant = Instant.ofEpochMilli((new Date()).getTime());
		LocalDateTime res = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
		//LocalDateTime转Date
	    Instant instant = LocalDateTime.now().atZone(ZoneId.systemDefault()).toInstant();
	    Date res = Date.from(instant);
		```
	* 参考URL
		* http://www.cnblogs.com/comeboo/p/5378922.html
		* http://blog.csdn.net/sf_cyl/article/details/51987088
		* http://www.cnblogs.com/feika/p/4448573.html
	* 支持
		* 6.0.5 版本的mysql
		* 3.4.2 版本的mybatis已经支持LocalDateTime 转换成数据库的DateTime(仍然需要额外的包)
	
* javaWeb项目获得当前目录 `getClass().getResource("").getFile().toString();`

* javaWeb项目获得项目根目录 `getClass().getResource("/").getFile().toString();`

* java8支持以`forEach`遍历
	```java
	map.forEach((key, value) -> System.out.println(key + " : " + value))
	```
	
* java8 Stream
	```java
	Stream.of(List<Integer>...);//得到的是Stream<List<Integer>>
	List<Integer>.stream();//得到的是Stream<Integer>
	//Stream<List<Integer>>需要进行 .flatMap(Collection::stream) 操作才能得到Stream<Integer>
	```
	
* jar 运行
  java -cp spider.jar com.smzdm.main.MainClass
  java -jar test.jar
  Main-Class: com.smzdm.main.MainClass

* jdk 7 文件流
	```java
	//读取全部
	byte[] data    = Files.readAllBytes(Paths.get("/home/biezhi/a.txt"));// 默认utf-8
	String content = new String(data);
	//按行读取
	List<String> lines = Files.readAllLines(Paths.get("/home/biezhi/a.txt"));
	//写入文件
	Files.write(Paths.get("/home/biezhi/b.txt"), "Hello JDK7!".getBytes());
	//尾部添加
	Files.write(Paths.get("/home/biezhi/b.txt"), "Hello JDK7!".getBytes(), StandardOpenOption.APPEND);
	```
	
* java ee
	* jsp内置对象 : request、respons、pageContext、Session、application、out(输出)、Config、page、Exception
	* jsp动作 : jsp:include,jsp:useBean:jsp:setProperty,jsp:getProperty:jsp:forward,jsp:plugin
	* Servlet生命周期 :web容器初始化时 , 调用init() 方法;运行时调用Service方法去处理请求;关闭web容器前调用destroy() 方法
	
* java序列化
	可以通过 `ObjectOutputStream` 将当前对象以二进制的形式保存到本地 , 其中serialVersionUID可以用来校验类版本
	
* Object基类方法
	* clone(protected native)
		实现对象的浅复制 , 只有实现了Cloneable接口才可以调用该方法 , 否则抛出CloneNotSupportedException异常
	* getClass(final native)
		获得运行时类型
	* toString
		默认是getClass().getName() + "@" + Integer.toHexString(hashCode())//hex是16进制
	* finalize(protected)
		该方法用于释放资源 , 因为无法确定该方法什么时候被调用 , 很少使用
	* equals
		Object类中默认equals是使用==判断 , 实际中可能需要重写 , 重写时一般也要重写hashCode方法
	* hashCode(native)
		该方法用于哈希查找 , 在一些具有哈希功能的Collection中用到;一般必须满足 :obj1.equals(obj2)==true=>obj1.hashCode()==obj2.hashCode(), 但是hashCode相等不一定就满足equals
	* wait(最终实现为 native)
		wait方法就是使当前线程等待该对象的锁 , 当前线程必须是该对象的拥有者 , 也就是具有该对象的锁 .wait() 方法一直等待 , 直到获得锁或者被中断 .wait(longtimeout) 设定一个超时间隔 , 如果在规定时间内没有获得锁就返回
		调用该方法后当前线程进入睡眠状态 , 直到以下事件发生
		(1) 其他线程调用了该对象的notify 或 notifyAll方法
		(2) 其他线程调用了interrupt中断该线程
		(3) 时间间隔到了
		此时该线程就可以被调度了 , 如果是被中断的话就抛出一个InterruptedException异常
	* notify(native)
		该方法唤醒在该对象上等待的某个线程
	* notifyAll(native)
		该方法唤醒在该对象上等待的所有线程
	
* Throwable介绍
	 Throwable 下有两个子类 , 分别是Exception和Error
	* Exception
		* unchecked exception
			也称为运行时异常 (RuntimeException), 比如常见的`NullPointerException`、`IndexOutOfBoundsException`, 不要求强制处理
		* checked exception
			Exception中除去RuntimeException, 其他的都可以被称为检查异常 , 常见的如`IOExeption`, 强制要求处理
	* Error
		如`OutOfMemoryError`. 虽然也可以被catch, 但是错误是无法处理 , 并且jdk官方不推荐进行捕获
	
* java正则
	
	```java
	public class TesReg {
	    @Test
	    public void testString() {
	        // 与js略不同 ,/[A-Z]/.test('检测是否包含大小写AAAbbb');
	        // 而java则需要把正则补全
	        System.out.println("检测是否包含大小写AAAbbb".matches(".*[A-Z].*"));
	    }
	
	    @Test
	    public void testGroup() {
	        String line = "This order was placed for QT3000! OK?";
	        Pattern pattern = Pattern.compile("(\\D*)(\\d+)(.*)");
	        Matcher matcher = pattern.matcher(line);
	        if (matcher.matches()) {
	            for (int i = 0; i <= matcher.groupCount(); i++) {
	                System.out.printf("Found %d group:" + matcher.group(i) + "\n", i);
	            }
	        } else {
	            System.out.println("NO MATCH");
	        }
	    }
	}
	```
	

## JAVA版本特性

### JAVA8

- nashorn js引擎

  ```java
  ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
  engine.eval("print('Hello World!');");
  ```

- Base64

  ```java
  Base64.Encoder encoder = Base64.getEncoder();
  String s = encoder.encodeToString("你好world".getBytes());
  Base64.Decoder decoder = Base64.getDecoder();
  String s2 = new String(decoder.decode(s));
  ```

* Comparator 升级

  ```java
  Comparator.comparing(UserModel::getClassName).thenComparing(UserModel::getId).reversed()
  ```

- 时间 API ,Stream ,Optional, 接口的默认方法,多重注解 函数式接口 `javac -parameters` 编译的时候不丢失方法入参名, `HashMap` 链表升级到红黑树,`String.join()` 

### JAVA7

- Files Paths 新的文件和路径 读取文件更加方便
- switch支持字符串(实际上仍然是 数字)
- catch 多个异常
- 泛型推断
- try-with-resource
- Objects 类, 方便对象的一些操作