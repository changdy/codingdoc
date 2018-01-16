> 主要为了实现跨系统文件的共享

* yum安装

  yum install -y samba

* 修改默认配置`vim /etc/samba/smb.conf`

  ```
  [global]
          workgroup = SHARE
          server string = Ted Samba Server %v
          netbios name = TedSamba
          security = user
          map to guest = Bad User
          passdb backend = tdbsam

  [FileShare]
          comment = share some files
          path = /downloads
          public = no
          writeable = yes
          create mask = 0644
          directory mask = 0755
          browseable = yes
  ```

* 添加用户`smbvisit`

  useradd smbvisit

* 修改用户密码

  pdbedit -a smbvisit

* 启动项目

  ```shell
  systemctl start smb
  ```

* 设置为可以开机启动

  ```shell
  systemctl enable smb
  ```

