# linux备忘录
* unar
  * 全功能型解压工具
  * `unar test.zip`
  * `yum install unar -y`
* zcat
  
  * 查看解压包里面的日志
* [icdiff](https://github.com/jeffkaufman/icdiff)
  
  * 分屏diff
* [htop](http://hisham.hm/htop/)
  * 代替top命令 , 方便查看系统状态
  * `yum install -y htop` (需要EPEL)
* [ripgrep(rg)](https://github.com/BurntSushi/ripgrep)
  * 比ag更快的递归搜索文件内容
  * ` rg exception -i`
  * 安装
    ```shell
    yum-config-manager --add-repo=https://copr.fedorainfracloud.org/coprs/carlwgeorge/ripgrep/repo/epel-7/carlwgeorge-ripgrep-epel-7.repo
    yum install ripgrep
    ```
* [mycli](https://github.com/dbcli/mycli)
  * 代替mysql-client 支持语法高亮和命令补全
  * `pip install -U mycli`
* [fzf](https://github.com/junegunn/fzf)
  * 命令行下模糊搜索工具
  * `ctrl r` 历史命令 `ctrl t` 文件搜索 ,kill 也能用
  * 
    ```shell
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
    ~/.fzf/install
    ```
* sz/rz
  * 通过ssh链接上传下载文件
  * `yum install lrzsz`
* [tldr](https://github.com/tldr-pages/tldr)
  * 以用例解释软件用途 , 类似还有`cheat`
  * `npm install -g tldr` , 有问题的话 ,[可能需要](https://github.com/tldr-pages/tldr-node-client#npm-install--g-tldr-throws-an-error) `npm install -g tldr --unsafe-perm`
* denyhosts
  * 自动禁用ssh失败ip
  * `yum install denyhosts`
* EPEL
  * 我也不知道是啥 , 貌似东西会多一些 , 比如htop会依赖这个
  * `yum install -y epel-release` , 替代源 :[科技大学](http://mirrors.ustc.edu.cn/help/epel.html)
* [ncdu](https://dev.yorhel.nl/ncdu)
  * du可视化
  * `yum install ncdu`
## 部分备用 
* [mosh](https://mosh.org/#getting)
  * 基于UDP的终端链接工具
* [pm2](http://pm2.keymetrics.io/)
  * 后台运行项目查看日志 等等
  * ` npm install pm2 -g`
* [cv](https://github.com/Xfennec/progress)

  * cp mv 添加进度条
* [Nmap](https://nmap.org/)
  * 端口扫描工具
## 部分命令
* ps -ef|grep java|grep -v grep|awk '{print $2}'|xargs kill -9
* find / -type f -size +200M
* cat /dev/null > saa_wx.log
* nohup xxx  >/dev/null 2>&1  &
* `ctrl a` 终端开始 ,`ctrl e`

## todo

* ranger 预览

## 启停脚本
* 启动
  ```shell
  #!/bin/sh
  rm -f ~/java/xx.pid
  nohup java -jar xx.jar --spring.profiles.active=dev > /dev/null 2>&1 &
  echo $! > ~/java/xx.pid
  echo Start Success!
  ```

* 停止
  ```shell
  #!/bin/sh
  APP_NAME=myapp
  tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
  if [ ${tpid} ]; then
    echo 'Stop Process...'
    kill -15 $tpid
  fi
  sleep 3
  tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
  if [ ${tpid} ]; then
    echo 'Kill Process!'
    kill -9 $tpid
  else
    echo 'Stop Success!'
  fi
  ```

* 检查

  ```shell
  #!/bin/sh
  APP_NAME=myapp
  tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
  if [ ${tpid} ]; then
      echo 'App is running.'
  else
      echo 'App is NOT running.'
  fi
  ```

## 分割日志脚本

* ```shell
  current_date=`date -d "-1 day" "+%Y%m%d"`
  echo $current_date
  split  -b +100m -d -a 4  /home/nohup.out  /home/log/log_${current_date}_
  tail -180 /home/nohup.out> /home/log/log_${current_date}_tail
  cat /dev/null > /home/nohup.out
  xz -v -T0 -9 /home/log/log_${current_date}_*
  ```

## 关闭防火墙

* `systemctl stop firewalld.service `
* `systemctl disable firewalld.service `

## linux 正则

正则其实有不同的标准 , 不同标准实现上略有差异 , 比如 默认情况下 grep 并不支持 \D 和\d , 需要加额外的参数.具体可见 [grep中使用"\d"匹配数字不成功的原因](https://blog.csdn.net/yufenghyc/article/details/51078107) , `grep -P "\D2939304\D"` 需要加 -P参数