# MySqlDump 学习

## 常用命令

* 后台导出数据到sql文件

    ```
    nohup mysqldump -h127.0.0.1  -u root -R -C  -ppassword dbname --result-file=/tmp/new_sql.sql &
    ```

* 直接导出数据到另外一个MySql服务器

  ```
  mysqldump --host=127.0.0.1  -uroot -ppassword -C  --opt  ssr_test |mysql --host=localhost  -uroot -prootpwd  -C  ssr_test 
  ```


## 常用参数

* **--no-data**

  不导出数据,只导出结构

* --no-create-db
  
  取消创建数据库sql(默认存在)
  
* --no-create-info

  取消创建表sql(默认存在)

* --add-drop-database

  增加删除数据库sql（默认不存在）

* --skip-add-drop-table

  取消每个数据表创建之前添加drop数据表语句(默认每个表之前存在drop语句)

* **--skip-add-locks** 

  取消在每个表导出之前增加LOCK TABLES（默认存在锁）

* **--ignore-table**
  不导出指定表。指定忽略多个表时，需要重复多次，每次一个表。每个表必须同时指定数据库和表名。例如：--ignore-table=database.table1 --ignore-table=database.table2 …… mysqldump  -uroot -p --host=localhost --all-databases --ignore-table=mysql.user

* **--compress, -C**

  在客户端和服务器之间启用压缩传递所有信息

* **--result-file,  -r**

  直接输出到指定文件中。该选项应该用在使用回车换行对（\\r\\n）换行的系统上（例如：DOS，Windows）。该选项确保只有一行被使用。

## 引用

本文基本摘抄自:

* [MySQL mysqldump数据导出详解](https://www.cnblogs.com/chenmh/p/5300370.html)