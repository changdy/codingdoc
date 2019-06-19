# MySql事务实践

本文章中的实践是基于 MySql的默认事务隔离级别 REPEATABLE_READ 即重复读.数据库引擎基于 InnoDB

## 杂项

* SQL 语言可以大致分为 [DQL](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80) ,  [DML](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E6%93%8D%E7%B8%B1%E8%AA%9E%E8%A8%80) , [DDL](https://en.wikipedia.org/wiki/Data_definition_language) ,[DCL](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E6%8E%A7%E5%88%B6%E8%AA%9E%E8%A8%80)  其中DDL 并不受事务影响 , 隐式自动提交 , 默认无法回退
* 在未提交的事务中执行DML 并不会影响到事务外的会话
* `InnoDB` 的行级锁是通过索引实现 , 如果当前条件没有索引则锁表
* `InnoDB` 对表进行行级锁的时候 , 同时会给表加上意向锁
* `select ... for update` 是排它锁, 而 `select ... lock in share mode` 是共享锁 ,但是并未发现两者的用途上的差别 , 而直接`select` 则是无锁 . 不受锁的影响
* 至于那些直接用 `LOCK` 锁表的 , 应该严重影响了效率 估计日常中不会见到

## 锁实践

建表如下,每次测试之后会清空表

```sql
CREATE TABLE test_table(
  id     INT AUTO_INCREMENT  PRIMARY KEY,
  string VARCHAR(10) NULL )
```

### 重复读测试

#### 步骤

| 会话1                                                    | 会话2                                                        |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| START TRANSACTION ;                                      |                                                              |
|                                                          | INSERT INTO test_table <br />(string) VALUES ('1');          |
| SELECT * FROM test_table;<br />// 能看到刚插入的1        |                                                              |
|                                                          | INSERT INTO test_table <br />(string) VALUES ('2');          |
| SELECT * FROM test_table;<br />// 仍旧只能看到1          |                                                              |
| SELECT * FROM test_table for update;<br />// 1,2均能看到 |                                                              |
|                                                          | INSERT INTO test_table <br />(string) VALUES ('2');<br />// 插入失败, 此时表已经上锁 |
| COMMIT;                                                  |                                                              |

#### 分析:

* 开启事务之后 , MySql仍然可以读取到已提交的修改 . 但是 对于同一张表 , 首次读取之后 , 该表的数据就不在受其他会话`DML` 语句的影响  . 通过这种方式实现了重复读 

* 推测重复读可能需要对表进行版本标识 , Mysql并不会在开启锁的时候给所有表加入标识,而是推迟到 首次进行`select` 操作时 (看来是基于MVCC)

  [Mysql可重复读（2） —— 快照真的就是快照吗](https://zhuanlan.zhihu.com/p/55872397)

* 可以看到 在事务里面直接`select` 并不会对表进行加锁 , 只有在`SELECT ..for update`时才会对表进行加锁

### 意向锁及DML测试

#### 步骤

```sql
TRUNCATE table test_table;
INSERT INTO test_table (id, string) VALUES (1, 1);
INSERT INTO test_table (id, string) VALUES (2, 2);
INSERT INTO test_table (id, string) VALUES (3, 3);
```

| 会话1                                                  | 会话2                                                        |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| START TRANSACTION                                      | START TRANSACTION                                            |
| SELECT *FROM test_table<br /> WHERE id =1 FOR UPDATE ; |                                                              |
|                                                        | SELECT * FROM test_table <br />WHERE id > 2 FOR UPDATE;      |
|                                                        | // SELECT * FROM test_table   FOR UPDATE ;<br />// 该条SQL实际上无法执行成功 |
| TRUNCATE test_table<br />// 该SQL亦无法执行成功        |                                                              |
|                                                        | ROLLBACK                                                     |
| TRUNCATE test_table                                    |                                                              |
| ROLLBACK                                               |                                                              |
| SELECT * FROM test_table<br />// 0条数据               |                                                              |

#### 分析

* 在给表添加行锁的时候 , 同时会给表添加一个意向锁 .当其他事物尝试给表添加行锁的时候 意向锁不会阻塞. 但如果尝试给表添加表锁的时候 ,则需要等待 之前行锁及意向锁的释放 . 意向锁不需要由用户操作 , MySql会自动根据情况添加
* TRUNCATE  不受事务影响 , 并且 TRUNCATE  或者 其他`DML` 语句也会隐式的给表添加表锁

## 参考

* [InnoDB 的意向锁有什么作用？](https://www.zhihu.com/question/51513268/answer/127777478)

  中间 关于如何判断一张表中有行锁的部分可以参考下

* [互联网项目中mysql应该选什么事务隔离级别](https://zhuanlan.zhihu.com/p/59061106)

  介绍了Mysql为什么使用**可重复读(Repeatable Read)**作为默认的隔离级别及其缺点,但推荐实际中应该选用**读已提交(Read Commited)**