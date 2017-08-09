# html和css学习
>2017-03-20 10:03

## html标签
* `<pre>`
  可以用来显示空格，会保持原有文字格式
* `<a>`
  可以用来进行跳转到指定位置锚点,因此可以直接利用href="#"来跳转到顶部,以及'www.changdy.com#c4'这种形式跳转到指定位置
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
## css
### css样式优先级
* 根据权值
  每种CSS选择器都有其权值,其中内联 1000,id 100,class 100,tagname 1
* 权值一样后,则可根据最近样式法则去确定样式
* 继承的权值一般较低
### background-image
background-image 属性描述了元素的背景图像.默认情况下，背景图像进行平铺重复显示
### css显示
* Display(显示) 与 Visibility（可见性）
  display属性设置一个元素应如何显示，visibility属性指定一个元素应可见还是隐藏。
  visibility:hidden可以隐藏某个元素，但隐藏的元素仍需占用与未隐藏之前一样的空间。也就是说，该元素虽然被隐藏了，但仍然会影响布局。
* Positioning
  * Absolute 绝对定位的元素的位置相对于最近的已定位父元素
  * Relative 相对定位元素的定位是相对其正常位置
  * 默认位置是 static
### css选择器
* 后代选取器：div p
* 子元素选择器：div>p
* 相邻兄弟选择器：div+p (都是之后的)
* 普通相邻兄弟选择器：div~p (都是之后的)
* 需要注意css选择器不能够回溯
### 伪类
* :before、:after分别可以在元素前或这元素后添加内容
### css方位
* css: 上 右 下 左;
* css: 上 左右 下
* css: 上下 左右
### vertical-align
指定了垂直对齐的位置，有middle、bottom等、类似于text-align
### 杂项
* 1em的默认大小是16px
* table比较丑大部分是因为border-collapse属性
* line-height 属性值不加单位表示行距倍数，自动适应font-size的各种单位。
* input类型disabled话提交表单不会被发送到服务器
* box-sizing可以用来进行border的合并
* 一边固定长度,一边自适应,可以用flex来完成
* 给style标签增加scoped属性,则此属性只作用于当前父标签下
* iframe 增加seamless,边距为0
### flex布局
* 父设置
  * flex-direction
    主轴排列方向
  * flex-wrap
    是否换行(默认不换行)
  * flex-flow
    `flex-direction`+`flex-wrap`的缩写
  * justify-content
    定义子元素在主轴上的排列方式,默认是左对齐
  * align-items
    定义交叉轴上的排列方式,默认为`stretch`:如果子dom未设置高度或设为auto，将占满整个容器的高度。
  * align-content
    多个主轴的时候,对齐方式,单轴无效,默认:`stretch`
* 子设置
  * order

    排序,小的在上面

  * flex-grow

    子元素放大比例,默认为`0`,即不放大,如果所有子元素为1,则等分

  * flex-shrink

    缩小的比例,默认为`1`,如果设置为0,则不缩小

  * flex-basis

    设置宽度,可以是像素,或者比例

  * flex

    `flex-grow`, `flex-shrink` 和 `flex-basis`缩写,默认为`0 1 auto`

  * align-self

    覆盖父元素的`align-items`