# Mysql 5.7安装

## 先检测之前是否安装

* 先卸载mysql

  ```shell
  yum remove  mysql mysql-server mysql-libs mysql-server;
  yum remove mariadb;
  ```

## 安装步骤

* 下载并安装MySQL官方的 Yum Repository

  ```shell
  wget http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
  ```

* 安装rpm

  ```sehll
  yum -y install mysql57-community-release-el7-10.noarch.rpm
  ```

* yum安装 这个步骤耗时较多

  ```shell
  yum -y install mysql-community-server
  ```

## 初始化步骤

* systemctl start  mysqld.service

* grep "password" /var/log/mysqld.log

* mysql -uroot -p

  并用前一个步骤获取的临时密码登陆

* 针对个人数据库可以修改下安全校验

  ```sql
  set global validate_password_policy=0;
  set global validate_password_length=1;
  ```

* 修改密码

  ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootpwd';
  ```

* 设置远程登陆

  ```sql
  update user set host ='%' where user ='root';
  ```

## 设置成UTF8-MB4

* 完善`my.cnf`文件

  ```
  [client] 
  default-character-set = utf8mb4 
  [mysql] 
  default-character-set = utf8mb4 
  [mysqld] 
  character-set-client-handshake = FALSE 
  character-set-server = utf8mb4 
  collation-server = utf8mb4_general_ci 
  init_connect='SET NAMES utf8mb4'
  ```

  另外注意最后一行`init_connect='SET NAMES utf8mb4'`  , 也可以不在这里设置 而是在连接池里写入 `init-sql`

## 参考

* [Mysql中的排序规则utf8_unicode_ci、utf8_general_ci的区别](https://www.cnblogs.com/zjfjava/p/7639535.html)
* [CentOS7安装MySQL](https://www.cnblogs.com/bigbrotherer/p/7241845.html)
* [Download MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum/)
* [A Quick Guide to Using the MySQL Yum Repository](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)