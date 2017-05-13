# css样式学习
> 2017/5/10 20:28:41

## 层叠次序
* 浏览器缺省设置
* 外部样式表
* 内部样式表（位于 <head> 标签内部）
* 内联样式（在 HTML 元素内部）（最高）
## background-image
background-image 属性描述了元素的背景图像.默认情况下，背景图像进行平铺重复显示
## css位置
* Display(显示) 与 Visibility（可见性）
display属性设置一个元素应如何显示，visibility属性指定一个元素应可见还是隐藏。
visibility:hidden可以隐藏某个元素，但隐藏的元素仍需占用与未隐藏之前一样的空间。也就是说，该元素虽然被隐藏了，但仍然会影响布局。
* Positioning
* Absolute 绝对定位的元素的位置相对于最近的已定位父元素
* Relative 相对定位元素的定位是相对其正常位置
* 默认位置是 static
## css选择器
* 后代选取器：div p
* 子元素选择器：div>p
* 相邻兄弟选择器：div+p (都是之后的)
* 普通相邻兄弟选择器：div~p (都是之后的)
## 伪类
* :before、:after分别可以在元素前或这元素后添加内容
## css方位
* css: 上 右 下 左;
* css: 上 左右 下
* css: 上下 左右
## vertical-align
指定了垂直对齐的位置，有middle、bottom等、类似于text-align
## 杂项
* 1em的默认大小是16px
* table比较丑大部分是因为border-collapse属性
* line-height 属性值不加单位表示行距倍数，自动适应font-size的各种单位。
* input类型disabled话提交表单不会被发送到服务器