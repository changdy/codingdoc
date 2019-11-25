# Javascript学习日志
> Writen By CoolGhost,Started With 2016/10

## JavaScript类型相关

### JS对象类型

* 在 JavaScript 中有 5 种不同的数据类型 :  

  string,number,boolean,object,function

* 3 种对象类型 : 
  Object,Date,Array

* 2 个不包含任何值的数据类型 : 
  null,undefined

* 同时注意typeof方法中 : 
NaN 的数据类型是 number
数组 (Array) 的数据类型是 object
日期 (Date) 的数据类型为 object
null 的数据类型是 object
未定义变量的数据类型为 undefined

### 类型判断

```javascript
function User(name , age){
  this.name=name;
  this.age=age;
}
let u=new User();
console.log( 'typeof u  :'+typeof u );  
//输出object  
//显然 , 使用typeof判断复杂类型的对象 , 就失效了 , 但使用constructor就可以获取其真实类型
console.log( 'u.constructor.name  :'+u.constructor.name );   //user
```

### null和Undefined 区别

```javascript
typeof undefined             // undefined
typeof null                  // object
null == undefined            // true
```

## JavaScript方法特性

### JS自调用函数
```javascript
(function() {
	alert(1);
}());
```

### JS方法参数

JS没有强制要求参数数量和函数调用时的参数数量相同

```javascript
findMax(1, 123, 500, 115, 44, 88);
function findMax() {
    console.log(arguments)//可以通过arguments得到实际入参
} 
```
### 方法的this

```javascript
let myObject = {
	firstName:"John",
	lastName: "Doe",
	fullName: function () {
		return this.firstName + " " + this.lastName;
	}
}
myObject.fullName(); // 返回 "John Doe"
```

### 使用构造函数调用函数

```javascript
// 构造函数 :
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}
// This creates a new object
let x = new myFunction("John","Doe");
x.firstName;         //John
```

### bind,call,apply 区别
三者都可以用来改变this的指向 , 不同的是 call和apply会立刻调用函数 , 而bind会返回一个新的函数 (估计vue是用这种方法来实现的),call和apply主要体现在传参上;另外需要注意这三个对箭头函数均无效!

```javascript
func.call(this, arg1, arg2); //正常传参即可
func.apply(this, [arg1, arg2]) //需要将被传入的参数 封装成数组对象 tips:可以用arguments对象实现类似转发效果
```

## JavaScript 原生方法

### 对象遍历

* [for、forEach 、for in、each循环详解](https://blog.csdn.net/w390058785/article/details/78444088)
* [【ES6】for of用法](https://blog.csdn.net/w390058785/article/details/80522383)
* [ES6：遍历对象的键的所有方法](https://segmentfault.com/a/1190000014459289)
* [ES6属性遍历方法](https://www.cnblogs.com/zhujiasheng/p/8409979.html)
* [ES6之6种遍历对象属性的方法](https://www.jianshu.com/p/9fd5ce854006)

### substring与substr

- 如果只传一个参数 , 没有差别 , 都是从该位置截取到字符串尾部
- 如果传递两个参数 , 则substr(begain,length) 第二个参数是截取字符串的长度 ,substring

### js正则分组替换

```javascript
//"5=a,6=b,7=c"换成"a=5,b=6,c=7"
"5=a,6=b,7=c".replace(/(\d+)=(\w)/g,"$2=$1");
// 像vscode 也是这个语法
```

## JavaScript 对象

### Browser对象
`location.hash`	设置或返回从井号 (#) 开始的 URL(锚)
`location.host`	设置或返回主机名和当前 URL 的端口号
`location.hostname`	设置或返回当前 URL 的主机名
`location.href`	设置或返回完整的 URL
`location.pathname`	设置或返回当前 URL 的路径部分
`location.port`	设置或返回当前 URL 的端口号
`location.protocol`	设置或返回当前 URL 的协议
`location.search`	设置或返回从问号 (?) 开始的 URL(查询部分)
`history.back()` ,`history.forward()`  与在浏览器中点击按钮向前/向后相同

### [URL对象](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/URL)

```JS
new URL("https://test.com/test?param=1#id");
```
```
href: https://test.com/test?param=1&p2=4#id
origin: https://test.com
protocol: https:
host: test.com
hostname: test.com
pathname: /test
search: ?param=1&p2=4
searchParams: param=1&p2=4
hash: #id
```

### [URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)

```js
var paramsString = "?q=URLUtils.searchParams&topic=api"
var searchParams = new URLSearchParams(paramsString);

for (let p of searchParams) {
  console.log(p);
}
searchParams.has("topic") === true; // true
searchParams.get("topic") === "api"; // true
searchParams.getAll("topic"); // ["api"]
searchParams.get("foo") === ""; // true
searchParams.append("topic", "webdev");
searchParams.toString(); // "q=URLUtils.searchParams&topic=api&topic=webdev"
searchParams.set("topic", "More webdev");
searchParams.toString(); // "q=URLUtils.searchParams&topic=More+webdev"
searchParams.delete("topic");
searchParams.toString(); // "q=URLUtils.searchParams"
// 显示键/值对
for(var pair of searchParams.entries()) {
   console.log(pair[0]+ ', '+ pair[1]); 
}
```

## jQuery

### jQuery选择器

* 如果已经是jQuery对象了了 , 则往下寻找的时候需要使用`find()`方法 , 如$("#phase_mx").find("input[name^='mFlt']")
* jQuery的模糊匹配包括`^=`,`$=`,`*=`分别对应开始、结束、包含关系 .

* 元素选择器
利用jQuery选择器选取第几个元素时 , 不能直接用[]来选择 , 而是要通过eq来进行选择 , 如下 : 
	
	```javascript
	for (let i = 0; i < $("input[type='text']").length; i++) {
	    console.log($("input[type='text']").eq(i));
	}
	```
	而在用dom节点选择的时候则可以利用[]来选择第几个元素 , 如 : 
	```javascript
	document.getElementsByName('name')[0].value='changdy'
	```
	JS和jQuery的不同一定要注意辨别
	

### jQuery异步请求
有三种方式 `$.get`,`$.post`,`$.ajax`, 其中前两者是后者的简单方式 , 如果需要修改一些参数则要使用$.ajax, 返回的是 xhr对象 , 可以使用 `$.ajaxSetup`进行全局设置.另外 jQuery之后是返回`deferred`对象,详见:[jQuery的deferred对象详解](http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html)

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


### jQuery each() 方法
`each()`方法中的this为dom对象 , 而不是jQuery对象 , 转换方法如`$(this)`, 如果需要查找子对象则可以使用`.find()`方法

### [jQuery 发送文件](http://www.jianshu.com/p/46e6e03a0d53)
当使用Spring MVC接收的时候 , 参数为`@Param("fileName") MultipartFile file`

### parent、parents、closest、parentsUntil对比

* parent之查找一级
* parents一直往上找
* closest从当前元素开始找
* parentsUntil 从父元素开始找 , 找到之后得到其子节点

### jQuery on和trigger方法
trigger可以用来触发js事件,因此有时候可以借助trigger来进行消息的传递

```js
$("p").on("custom.someNamespace1", function (event) {
    console.log(event);//event.type=custom,event.namespace=someNamespace1
});
$("p").click(function (event) {
    $(this).trigger("custom.someNamespace1");
});
$("button").click(function(){
    $("p").off("custom.someNamespace");//让节点不在监听"custom.someNamespace"事件
});
```
### [$.prop() 方法](https://www.runoob.com/jquery/html-prop.html)

* 可以用来dom的状态,同时和attr有一定区别:[prop() 和 attr() 之间的不同](https://www.runoob.com/try/try.php?filename=tryjquery_html_prop_attr) ,[关于复选框选中状态的判断](https://www.cnblogs.com/circulatttt/p/5193262.html)

* 关于`data-*`的获取以及修改 :[jquery中data() 和js中dataset属性的区别](https://www.cnblogs.com/yxi-liu/p/8023988.html) ,[js,jQuery获取html5的data-*属性](https://segmentfault.com/a/1190000005770912), 本质上来说是因为 jQuery进行了一个缓存

## js规范阅读

* parseInt应该指定进制
parseInt(inputValue, 10);
* 变量提升
变量声明会提升至作用域顶部 , 但赋值不会 .
* 判断数组是否非空直接使用if(arr.length)

## 其他杂项

### 程序块的作用域
在每个代码块中 JavaScript 不会创建一个新的作用域 , 一般各个代码块的作用域都是全局的 . 以下代码的的变量 i 返回 10, 而不是 undefined : 

```javascript
for (var i = 0; i < 10; i++) {
// some code
}
console.log(i); //10 ,但是如果用let声明i,就会报错
```

### [JavaScript 模块现状](https://zhuanlan.zhihu.com/p/26567790)

### js标签的 defer 和async 属性

* 默认情况下 执行到script标签的时候 dom会停止解析 , 并且开始下载脚本 , 脚本下载完毕后开始立即执行 , 所以之前为了保证执行时dom完整都是把 脚本放到body的最下面
* defer 的时候 , 浏览器不会停止解析dom, 并且开始下载脚本 . 等到dom树解析完毕 , 才会按照顺序依次执行脚本
* async 同样不会停止dom渲染 , 但是下载完毕后立刻执行 , 可以用来做 数据统计相关

### URL注意事项
* 避免URL出现大小写, 在不同的服务器上 大小写是否敏感可能不同, 因此使用连字符是更好的方式
* 注意转义问题, 比如`+ / ? % # &` 以及中文