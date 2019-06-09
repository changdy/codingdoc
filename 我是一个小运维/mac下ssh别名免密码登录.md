# ssh免密码别名登录

> centos:7.0 64 作为服务器
>
> mac:10.12.6 作为客户端

## 免密码登录

* 开启centos7的免密码登录设置 , 修改 `/etc/ssh/sshd_config`, 以下两行反注释掉 ,(也有可能只有一行)

  ```
  RSAAuthentication yes
  PubkeyAuthentication yes
  ```

* 在mac中使用`ssh-keygen -t rsa`生成key, 中间会让选择生成公钥和私钥的地址 , 以及一个暂时不太理解的密码 , 默认情况下直接回车就行了 .

* 在mac中使用`ssh-copy-id $user@$remote_ip`把生成的公钥发送给服务器 , 并保存到`~/.ssh/authorized_keys`文件中

* 使用ssh命令登录 , 如果免密码即成功

## 别名登录

* 在`.ssh`目录新增`config`文件 , 按照如下格式配置自己的远程服务器

  ```Yml
  Host linux
      HostName 192.168.2.233
      Port 22
      User root
      IdentityFile  /Users/test/.ssh/id_rsa.pub
      IdentitiesOnly yes
  ```

* 使用 `ssh-add -k`把**config**文件添加到**ssh-agent**缓存中 , 之后即可通过`ssh linux`登录linux主机


## 开机命令

上述方法虽然可以在mac下实现别名登录 , 但是每次重启电脑后 , 都需要再次运行`ssh-add -k`命令导入缓存 , 对于这种情况 , 则可以使用开机命令自动的运行脚本 , 详见[mac下解决开机需要每次ssh-add的问题](https://www.jianshu.com/p/ada03bd51ed5)