安装不用说了，官网上都已经完善了。记录下安装后如何配置吧，不过首先有个概念，PostgreSQL会新建一个'postgres'用户，并用这个用户来启动。

* `su - postgres`切换到postgres用户

* `psql -U postgres`登录到数据库

* 更改数据库密码，并用`\q`退出登录

  ```Sql
   ALTER USER postgres WITH PASSWORD 'abc123';
  ```

* 开启远程访问

  * ```shell
    vi /var/lib/pgsql/10/data/postgresql.conf 
    ```

  * `#listen_addresses = 'localhost'`改为`listen_addresses = '*'`

* 设置远程登录的方式

  * ```shell
    vi /var/lib/pgsql/10/data/pg_hba.conf
    ```

  * 在文件的底部添加`host all all 0.0.0.0/0  md5`,另postgres支持其他几种登录校验方式，此处不再累述

* `exit`退出postgres用户

* 防火墙开放postgresql，并让postgresql开机启动

  ```shell
  firewall-cmd --add-service=postgresql --permanent 
  firewall-cmd --reload
  systemctl enable  postgresql-10.service
  systemctl restart postgresql-10.service
  ```

  ​