# java学习日志
> 2017-03-06 09:29 

* SimpleDateFormat参数 *(不推荐)*
	* 实例化SimpleDateFormat的时候一定要注意，**区分大小写**
	* y代表年, 如yyyy
	* M代表月,如MM
	* d代表日期，如dd
	* H代表时间，如HH
	* h代表12小时制时间，如hh
	* m代表分钟，如mm
	* s代表秒，如ss
	* S代表毫秒，如SSS
	* 常用的方法如下
	```java
	SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//2017-02-08 09:26:54
	SimpleDateFormat sdf = new SimpleDateFormat("yyyy-M-d H:m:s");//2017-2-8 9:25:45
	```
* java8日期新特性
	* LocalDate
		```java
		// 取当前日期：
		LocalDate today = LocalDate.now(); // -> 2014-12-24
		// 根据年月日取日期，可以不补零：
		LocalDate crischristmas = LocalDate.of(2014, 12, 25);
		// 根据字符串取,严格按照ISO yyyy-MM-dd验证，需要补零
		LocalDate endOfFeb = LocalDate.parse("2014-02-28"); 
		// 取本月第1天：
		LocalDate firstDayOfThisMonth = today.with(TemporalAdjusters.firstDayOfMonth()); // 2014-12-01
		// 取本月第2天：
		LocalDate secondDayOfThisMonth = today.withDayOfMonth(2); // 2014-12-02
		// 取本月最后一天，再也不用计算是28，29，30还是31：
		LocalDate lastDayOfThisMonth = today.with(TemporalAdjusters.lastDayOfMonth()); // 2014-12-31
		// 取下一天：
		LocalDate firstDayOf2015 = lastDayOfThisMonth.plusDays(1); // 变成了2015-01-01
		// 取2015年1月第一个周一，这个计算用Calendar要死掉很多脑细胞：
		LocalDate firstMondayOf2015 = LocalDate.parse("2015-01-01").with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY));
		```
	* LocalTime
		```java
		LocalTime zero = LocalTime.of(0, 0, 0); // 00:00:00
		LocalTime mid = LocalTime.parse("12:00:00"); // 12:00:00
		LocalTime now = LocalTime.now().withNano(0)); // 11:09:09
		LocalTime now = LocalTime.now(); // 11:09:09.240
		```
	* LocalDateTime
		```java
		
		```
	* Instant
	* ZonedDateTime
	* 新的日期格式化工具
		```java
		String goodFriday = "04 18 2014";
		try {
			//MM需要补零，M则是可以补零，也可以不补零
		    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM dd yyyy");
		    LocalDate holiday = LocalDate.parse(goodFriday, formatter);
		    String landing = holiday.format(formatter);
		    System.out.printf(goodFriday + holiday + landing);
		} catch (DateTimeParseException ex) {
		    System.out.printf("%s is not parsable!%n", goodFriday);
		    ex.printStackTrace();
		}
		```
	* 参考URL
	* http://www.cnblogs.com/comeboo/p/5378922.html
* javaWeb项目获得当前目录 `getClass().getResource("").getFile().toString();`
* javaWeb项目获得项目根目录 `getClass().getResource("/").getFile().toString();`
* 