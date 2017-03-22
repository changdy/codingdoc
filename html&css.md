# html和css学习
>2017-03-20 10:03

## 标签
* `<pre>`
可以用来显示空格，会保持原有文字格式
* `<a>`
可以用来进行跳转到指定位置
	```html
	<a href="#C4">查看章节 4</a>
	<h2><a id="C4">章节 4</a></h2>
	```
* `<head>`
	在 `<head>`元素中你可以插入脚本（scripts）, 样式文件（CSS），及各种meta信息: `<title>` `<style>` `<meta>` `<link>` `<script>` `<noscript>` `<base>`
	* `<base>`
		定义页面中所有链接默认的链接目标地址。
		```html
		<base href="http://www.runoob.com/images/" target="_blank">
		```
	* `<meta>`
		标签提供了元数据.元数据也不显示在页面上，但会被浏览器解析。META元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。元数据可以使用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他Web服务。
		```html
		<meta name="description" content="免费在线教程">
		<meta name="keywords" content="HTML,CSS,XML,JavaScript">
		<meta name="author" content="runoob">
		<meta charset="UTF-8">
		```
	* `<noscript>`
	如果当前浏览器禁止js运行则会显示 `<noscript>`里面的信息。
* `<div>` `<span>`
	div是块元素(block-level),span为内联元素(inline)。两个都没有特殊意义。块元素一般会新行开始、二内联元素一般不会以新行开始