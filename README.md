# todolist

> 重新开始给自己树flag

## 长期

* 以下仓库知识点系统的学习与记录

  - [ ] [CS-Notes](https://github.com/CyC2018/CS-Notes)
  - [ ] [JCSprout](https://github.com/crossoverJie/JCSprout)
  - [ ] [JavaGuide](https://github.com/Snailclimb/JavaGuide)
  - [ ] [后端架构师技术图谱](https://github.com/xingshaocheng/architect-awesome)
  - [ ] [3y原创技术文章导航](https://github.com/ZhongFuCheng3y/3y)
  - [ ] [Java工程师成神之路](https://github.com/hollischuang/toBeTopJavaer)
  - [ ] [互联网 Java 工程师进阶知识完全扫盲](https://github.com/doocs/advanced-java)
  - [ ] [ZXBlog](https://github.com/ZXZxin/ZXBlog)
  
* 英语学习

* 仿照UC浏览器开发一个chrome浏览器历史记录插件

  暂时搁浅 , 好久不用CSS已经不会写布局了

* 用Electorn写一个手机和pc端同步工具

## 疑难杂症

- [ ] 为什么mysql之前都不支持倒序排序 , 按理来说B+树下面转成双线链表应该很简单啊
- [ ] 规则引擎 :一般项目会用到吗 , 有啥实际的应用
- [ ] hash是怎么实现字符串查找的?: [为什么 360 面试官说 Trie 树没用？](https://www.zhihu.com/question/27168319)
- [ ] 为什么最后需要arp协议? 不能一直 进行ip查找吗?

## 2019-06-02

- [ ] 前段时间学习的知识点整理
- [ ] 很久之前的零碎知识点也做个统计
- [ ] N皇后问题

## 2019-06-08

- [x] mysql 资料以及索引优化整理
- [ ] 构建自己的网络知识

## 2019-06-13

- [x] js的 `JSON.parse`方法如何防止精度丢失

  可以把数字序列化成字符串,或者使用第三方库 比如 [json-bigint](https://github.com/sidorares/json-bigint)

  另外前端如果不进行运算的话 , 最好使用字符串替代长整型. 如果一定要进行运算 只能借助第三方库,比如: [bignumber.js](https://github.com/MikeMcl/bignumber.js) , [decimal.js](https://github.com/MikeMcl/decimal.js/) [mathjs](https://github.com/josdejong/mathjs/) 

- [x] 前端直接生成并下载文件的方法

  [照片批量下载](https://github.com/changdy/demo/blob/master/script/%E7%85%A7%E7%89%87%E6%89%B9%E9%87%8F%E4%B8%8B%E8%BD%BD.md)

- [x] [各种锁的情况](我真的只会java/JAVA锁事.md)

- [ ] Spring 整理一下

- [x] js 如何简单深拷贝

  确实没啥简单实现深拷贝的方法 , (对js的鄙视+1).  一般来说只能借助第三方库实现: 比如 lodash的`_.cloneDeep()` 和 `jQuery.extend` , 也可以自己实现 , 但要想好怎么处理非基本类型 , 比如

  `Map` `Set` 以及 `Moment`  .另外 `{...obj}` ` Object.assgin({},obj)` 都是浅拷贝 ,纯基本数据类型的确用 `JSON.parse(JSON.stringify())`最简单

- [x] [从输入URL到页面加载的过程](https://github.com/changdy/codingdoc/blob/master/%E5%86%99%E5%86%99%E5%89%8D%E7%AB%AF%E6%9C%80%E7%82%AB%E9%85%B7%E4%BA%86/%E4%BB%8E%E8%BE%93%E5%85%A5URL%E5%88%B0%E9%A1%B5%E9%9D%A2%E5%8A%A0%E8%BD%BD%E7%9A%84%E8%BF%87%E7%A8%8B.md)


## 2019-06-15

- [x] [bower](https://bower.io/) 了解

  应该是和 npm yarn之类的工具了,现在估计 已经不需要用了

  >  We don't recommend using Bower for new projects. Please consider Yarn and Webpack or Parcel.

## 2019-06-16

- [ ] 多进程 多线程 是如何与cpu的 核心和线程数量对应的