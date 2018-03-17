* yum 安装

  ```shell
  yum install transmission-cli transmission-common transmission-daemon -y
  ```

* 初始化配置

  ```shell
  transmission-daemon -g /usr/local/transmission
  ```

* 停止transmission-daemon

  ```shell
  ps -ef |grep transmission
  kill -9 XXXX
  ```

* 修改配置`vim /usr/local/transmission/settings.json`

  ```json
  {
      "rpc-authentication-required": true,#开启认证登录
      "rpc-password": "密码",#直接修改，保存后会自动加密
      "rpc-username": "账号",#管理员账号
      "rpc-port": 9091,#管理登录端口，最好修改
      "rpc-whitelist-enabled": false,#关闭白名单，所有IP都能登录
  }
  ```

* 重新启动

  ```shell
  transmission-daemon -g /usr/local/transmission
  ```

* 安装web-ui

  ```shell
  wget https://github.com/ronggang/transmission-web-control/raw/master/release/tr-control-easy-install.sh
  sh tr-control-easy-install.sh
  ```

* 打开 http://ip:9091 即可

##	默认配置

* setting.json默认位置是:`/var/lib/transmission/.config/transmission-daemon/settings.json`
* systemctl status transmission-daemon
