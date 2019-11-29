# 查看MySql状态

## information_schema 常用表

* SCHEMATA
提供了当前mysql实例中所有数据库的信息 . 是`show databases`的结果取之此表 . 
* TABLES
提供了关于数据库中的表的信息(包括视图) . 详细表述了某个表属于哪个schema , 表类型 , 表引擎 , 创建时间等信息 . 是show tables from schemaname的结果取之此表 . 
* COLUMNS
提供了表中的列信息 . 详细表述了某张表的所有列以及每个列的信息 . 是`show columns from schemaname.tablename`的结果取之此表 . 
* STATISTICS
提供了关于表索引的信息 . 是`show index from schemaname.tablename`的结果取之此表 . 
* TABLE_CONSTRAINTS
描述了存在约束的表 . 以及表的约束类型 . 
* TRIGGERS
提供了关于触发程序的信息 . 必须有super权限才能查看该表 . 
* REFERENTIAL_CONSTRAINTS
这个表提供的外键相关的信息 , 而且只提供外键相关信息
* TABLE_CONSTRAINTS
这个表提供的是 相关的约束信息
* INNODB_SYS_FOREIGN_COLS
这个表也是存储的INNODB关于外键的元数据信息和SYS_FOREIGN_COLS 存储的信息是一致的
* INNODB_SYS_FOREIGN
存储的INNODB关于外键的元数据信息和SYS_FOREIGN_COLS 存储的信息是一致的 , 只不过是单独对于INNODB来说的
* KEY_COLUMN_USAGE
数据库中所有有约束的列都会存下下来 , 也会记录下约束的名字和类别

* 锁相关表

  ```sql
  -- 查看事务
  SELECT *FROM information_schema.INNODB_TRX;
  -- 查看锁
  SELECT *FROM information_schema.INNODB_LOCKS;
  -- 查看锁等待
  SELECT *FROM information_schema.INNODB_LOCK_WAITS;
  -- 关联查询,等待锁被那个锁锁住
  SELECT
    r.trx_id              waiting_trx_id,
    r.trx_mysql_thread_id waiting_thread,
    r.trx_query           waiting_query,
    b.trx_id              blocking_trx_id,
    b.trx_mysql_thread_id blocking_thread,
    b.trx_query           blocking_query
  FROM information_schema.innodb_lock_waits w INNER JOIN information_schema.innodb_trx b ON b.trx_id = w.blocking_trx_id
    INNER JOIN information_schema.innodb_trx r ON r.trx_id = w.requesting_trx_id;
    
  -- 在MySql比较高的版本 查询锁等待可以使用如下视图
  select * from sys.innodb_lock_waits;
  ```

## 常用查询

### show status  

查看服务器状态 , 可以查看一下当前的系统状态 详细可以参考 [MySQL中show语法使用总结](https://www.cnblogs.com/saneri/p/6963583.html)

### show variables

可以用来查看当前 的一些配置参数 , 比如判断当前是否 自动提交(即是否在事务中)

`show variables like 'autocommit'` 也可以简化为 `select @@autocommit;`

### 一些其他

* show warnings 

  显示最后一个执行的语句所产生的错误、警告和通知。

* show errors

   只显示最后一个执行语句所产生的错误。

## 引用

* [mysql 用户变量（@）和系统变量（@@ show variables 和 show global variables 区别）](http://www.uuboku.com/392.html)
* [MySQL--mysql常用函数打全](https://blog.csdn.net/u014320421/article/details/79814734)