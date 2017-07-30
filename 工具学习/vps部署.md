#  vps记录
## vps基础
* ssh端口一般默认为 22
* 关闭firewall：
  * 停止firewall
    systemctl stop firewalld.service 
  * 禁止firewall开机启动
    systemctl disable firewalld.service 
  * 查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
    firewall-cmd --state 
* 后台运行
  nohup java -cp spider.jar com.smzdm.main.MainClass &
* 删除文件夹
  `rm -fi [文件夹]`
## jdk安装
* yum install java-1.8.0-openjdk
## MariaDB
* vim /etc/yum.repos.d/MariaDB.repo
  ```
  # MariaDB 10.0 CentOS repository list - created 2015-08-12 10:59 UTC
  # http://mariadb.org/mariadb/repositories/
  [mariadb]
  name = MariaDB
  baseurl = http://yum.mariadb.org/10.0/centos6-amd64
  gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
  gpgcheck=1
  ```
* yum remove MariaDB-server MariaDB-client
* yum  install MariaDB-server MariaDB-client
* service mysql status
* vi /etc/my.cnf
  ```
  [client]
  default-character-set=utf8
  [mysqld]
  character-set-server=utf8
  ```
* service mysql start
* mysqladmin -u root password rootpwd
* mysql -u root -p 
* 远程用户
  ```sql
  CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION;
  CREATE USER 'username'@'%' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' WITH GRANT OPTION;
  FLUSH PRIVILEGES;
  ```
## tomcat
* wget http://ftp.wayne.edu/apache/tomcat/tomcat-8/v8.5.15/bin/apache-tomcat-8.5.15.tar.gz
* tar xzf apache-tomcat-8.5.15.tar.gz
* vim apache-tomcat-8.5.15/conf/tomcat-users.xml
  ```xml
  <role rolename="admin-gui"/>
  <user username="tomcat" password="s3cret" roles="admin-gui"/>
  <role rolename="manager-gui"/>
  <user username="tomcat" password="s3cret" roles="manager-gui"/>
  ```
* vim  apache-tomcat-8.5.15/conf/server.xml
  URIEncoding="UTF-8"
 * 调用管理界面还需要去掉外网配置
* 改为中国时区,只需要在catalina.sh中第一行加入
`JAVA_OPTS="$JAVA_OPTS -Dfile.encoding=UTF8  -Duser.timezone=GMT+08"`