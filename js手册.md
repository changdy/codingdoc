# Javascript学习日志
> Writen By CoolGhost,Started With 2016/10

* 访问JS属性的两种方式
 `person.lastName`   `person["lastName"]`
* 如果把值赋给尚未声明的变量，该变量将被自动作为全局变量声明
	* 此处可使用 window.carName
		```javascript
		function myFunction() {
			carName = "Volvo";
		}
		```
	* 此处可调用 carName 变量
		```javascript
		function myFunction() {
		carName = "Volvo";
		// 此处可调用 carName 变量
		}
		```
* js中的转义
`\'`	单引号`\"`	双引号`\\`	反斜杠`\n`	换行`\r`回车`\t`	tab(制表)`\b`	退格符`\f`换页符
* 类型判断
	```javascript
	function User(name , age){
	this.name=name;
	this.age=age;
	}
	var u=new User();
	console.log( 'typeof u  :'+typeof u );  
	//输出object  
	//显然,使用typeof判断复杂类型的对象,就失效了,但使用constructor就可以获取其真实类型
	console.log( 'u.constructor.name  :'+u.constructor.name );   //user
	```
* JS代码块的Break
	```javascript
	cars=["BMW","Volvo","Saab","Ford"];
	list:{
		document.write(cars[0] + "<br>"); 
		document.write(cars[1] + "<br>"); 
		document.write(cars[2] + "<br>"); 
		break list;
		document.write(cars[3] + "<br>"); 
		document.write(cars[4] + "<br>"); 
		document.write(cars[5] + "<br>"); 
	}
	```
	以上代码生成了名为list的代码块，并且break进行了跳出，同时continue 具有相同作用，不过只能应用于循环，而通过标签，break能够适用于任何js代码块
* null和Undefined 区别：
	```javascript
	typeof undefined             // undefined
	typeof null                  // object
	null === undefined           // false
	null == undefined            // true
	```
* JS对象类型：
	* 在 JavaScript 中有 5 种不同的数据类型： 
	string,number,boolean,object,function
	* 3 种对象类型：
	Object,Date,Array
	* 2 个不包含任何值的数据类型：
	null,undefined
	* 同时注意typeof方法中：
	NaN 的数据类型是 number
	数组(Array)的数据类型是 object
	日期(Date)的数据类型为 object
	null 的数据类型是 object
	未定义变量的数据类型为 undefined
* JS的try catch throw
	```javascript
	try{ 
		var x=document.getElementById("demo").value;
		if(x=="")    throw "值为空";
		if(isNaN(x))  throw "不是数字";
		if(x>10)     throw "太大";
		if(x<5)      throw "太小";
	}
	catch(err){
		var y=document.getElementById("mess");
		y.innerHTML="错误：" + err + "。";
	}
	```
* JS的调试方法包括使用`debugger`，不过使用该方法需要打开浏览器调试工具
* JS中如果String类型需要分行，要使用`\`
 	```javascript
	var x = "Hello \
	World!";
	```
* 程序块的作用域
在每个代码块中 JavaScript 不会创建一个新的作用域，一般各个代码块的作用域都是全局的。以下代码的的变量 i 返回 10，而不是 undefined：
	```javascript
	for (var i = 0; i < 10; i++) {
	// some code
	}
	return i;
	```
* JS自调用函数
以下是，最前最后加括号，还有一些类似的方法，但是这个可读性比较高
	```javascript
	(function() {
		alert(1);
	}());
	```
* JS没有强制要求参数数量和函数调用时的参数数量相同，即:
	```javascript
	x = findMax(1, 123, 500, 115, 44, 88);
	function findMax() {...} 
	```
并不会报错。同时JS提供了函数的内置对象arguments，根据这个可以得到相应的参数
* this的调用
	```javascript
	var myObject = {
		firstName:"John",
		lastName: "Doe",
		fullName: function () {
		return this.firstName + " " + this.lastName;
		}
	}
	myObject.fullName();         // 返回 "John Doe"
	```
* 使用构造函数调用函数
	```javascript
	// 构造函数:
	function myFunction(arg1, arg2) {
	    this.firstName = arg1;
	    this.lastName  = arg2;
	}
	// This creates a new object
	var x = new myFunction("John","Doe");
	x.firstName;         //John
	```
* JS的Browser对象
`location.hash`	设置或返回从井号 (#) 开始的 URL（锚）
`location.host`	设置或返回主机名和当前 URL 的端口号
`location.hostname`	设置或返回当前 URL 的主机名
`location.href`	设置或返回完整的 URL
`location.pathname`	设置或返回当前 URL 的路径部分
`location.port`	设置或返回当前 URL 的端口号
`location.protocol`	设置或返回当前 URL 的协议
`location.search`	设置或返回从问号 (?) 开始的 URL（查询部分）
`history.back()`  与在浏览器点击后退按钮相同
`history.forward()`  与在浏览器中点击按钮向前相同
* [JavaScript 计时事件](http://www.runoob.com/js/js-timing.html)
`setInterval("javascript function",milliseconds)` - 间隔指定的毫秒数不停地执行指定的代码。
`setTimeout("javascript function",milliseconds)` - 暂停指定的毫秒数后执行指定的代码
* jQuery选择器
	* 如果已经是jQuery对象了了，则往下寻找的时候需要使用`find()`方法，如$("#phase_mx").find("input[name^='mFlt']")
	* jQuery的模糊匹配包括`^=`，`$=`，`*=`分别对应开始、结束、包含关系。
* jQuery异步请求
有三种方式 `$.get`，`$.post`，`$.ajax`，其中前两者是后者的简单方式，如果需要修改一些参数则要使用$.ajax，
	```javascript
	$.ajax({
		url: "project/import",
		type: "POST",
		async: false,//同步或异步
		data: "test=test",//发送参数
		dataType: "json", //返回类型
		beforSend: function() {
		},
		success: function(msg) {
		},
		error: function() {
		}
	})
	```
* jQuery获取文档内容
jQuery中有text()和html()方法，和JS的innerTEXT()或innerHTML()类似，并且可以使用回调函数
	```javascript
	$(selector).text(function(index,oldcontent))
	```
* 元素选择器
利用jQuery选择器选取第几个元素时，不能直接用[]来选择，而是要通过eq来进行选择，如下：
	```javascript
	for (var i = 0; i < $("input[type='text']").length; i++) {
	    $("input[type='text'] :eq(" + i + ")").val(i);
	}
	```
	而在用dom节点选择的时候则可以利用[]来选择第几个元素，如：
	```javascript
	document.getElementsByName('name')[0].value='changdy'
	```
	JS和jQuery的不同一定要注意辨别
* JS类型转换
从input中得到的值默认是字符串类型的，如果期望是数字类型比较则有可能得到错误判断。这个时候需要用到类型转换，数字类型的可以使用`parseFloat()`和`parseInt()`方法。
另外，JS中也有强制类型转换，比如Boolean(value)、Number(value)、String(value)。具体可以参考：[cnblogs](http://www.cnblogs.com/446557021/archive/2011/10/13/2211043.html "cnblogs")
* jQuery each()方法
`each()`方法中的this为dom对象，而不是jQuery对象，转换方法如`$(this)`，如果需要查找子对象则可以使用`.find()`方法
* [jQuery 发送文件](http://www.jianshu.com/p/46e6e03a0d53)
当使用Spring MVC接收的时候，参数为`@Param("fileName") MultipartFile file`
* 遍历Object对象
	```javascript
	var obj = {a:1 , b:2 , c:3}
	for(var s in obj){
	  console.log(s);
	  console.log(obj[s]);
	}
	```
* [JS中生成和解析JSON](http://www.cnblogs.com/jiangyy/p/3531150.html)
* css和js的文件位置
css样式应该放在head中，而引入的js应该放入到body的最下方
* keys()和values()方法
前者返回传入对象所有属性名组成的数组，后者返回所有属性值组成的数组
* parent、parents、closest、parentsUntil
	* parent之查找一级
	* parents一直往上找
	* closest从当前元素开始找
	* parentsUntil 从父元素开始找，找到之后得到其子节点
