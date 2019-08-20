# MySql零碎知识点

## 虚拟列(virtual columns)

先创建一张表

```sql
CREATE TABLE user (
  uid       INT AUTO_INCREMENT PRIMARY KEY,
  data      JSON     NULL,
  user_name VARCHAR(20) (JSON_UNQUOTE( DATA ->'$.name')) STORED,
  date      DATE AS (DATE(date_time)) STORED,
  date_time DATETIME NULL,
  KEY `name_index` (`user_name`)
);
```

创建一条数据

```sql
INSERT INTO user ( data, date_time) VALUES ( '{"date": "123", "name": "555"}', '2019-08-20 08:03:36');
```

查询的结果转json 为

```json
{
    "uid": 2,
    "data": "{\"date\": \"123\", \"name\": \"555\"}",
    "date_time": "2019-08-20 08:03:36",
    "user_name": "555",
    "date": "2019-08-20"
}
```

主要用途:

* 一些复杂处理可以再插入的时候完成 并且利用空间换效率

* MySql不支持在json上构建索引,可以曲线救国;创建一个虚拟列,然后在虚拟列上创建索引

## general_log 与bin_log

两者都可以记录数据库的日志,区别如下:

* general_log  可以把日志输出到MySql表中,或者文件中,bin_log只能到文件中
* general_log  更倾向于展示当前执行的原始sql,事务等信息也都会被展示,而bin_log展示的是数据库变动
* general_log  的粒度更细一些.可以用来观察当前的服务器状态, 而bin_log更适合做数据同步
* general_log   为文本模式, 而binlog为二进制

### 开启general_log 的方法

* 设置输出模式

  ```mysql
  set global log_output='TABLE,FILE'; --同事把日志输出到文件以及表中
  ```

* 查看文件的输出位置

  ```mysql
  show variables where Variable_name="general_log_file";
  ```

* 开启输出

  ```mysql
  set global general_log=ON;
  ```

* 查看日志

  通过日志文件

  ```shell
  tail -f general_log_file
  ```

  通过mysql表

  ```mysql
  select * from mysql.general_log limit 1
  ```

* 关闭输出

  ```mysql
  set global general_log=OFF;
  ```

### bin_log用途

* 一般可以用来做数据库的同步,但是需要注意,binlog会自动切割,如果需要初始化数据, 应该使用mysqldump之类
* bin_log 具有多种格式,一般会选择row,具有上下文信息
* bin_log可以配合解析工具做同步 , 比如canal. 另外mysql主从服务器也是基于bin_log实现,master写入信息,slave拉取信息并relay-log 以达到同步