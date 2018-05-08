# linux备忘录
* unar
  * 全功能型解压工具
  * `unar test.zip`
  * `yum install unar -y`
* [htop](http://hisham.hm/htop/)
  * 代替top命令，方便查看系统状态
  * `yum install -y htop` (需要EPEL)
* [rg](https://github.com/BurntSushi/ripgrep)
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
  * `ctrl r` 历史命令 `ctrl t` 文件搜索,kill 也能用
  * 
    ```shell
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
    ~/.fzf/install
    ```
* sz/rz
  * 通过ssh链接上传下载文件
  * `yum install lrzsz`
* [tldr](https://github.com/tldr-pages/tldr)
  * 以用例解释软件用途，类似还有`cheat`
  * `npm install -g tldr` ,有问题的话 ,[可能需要](https://github.com/tldr-pages/tldr-node-client#npm-install--g-tldr-throws-an-error) `npm install -g tldr --unsafe-perm`
* denyhosts
  * 自动禁用ssh失败ip
  * `yum install denyhosts`
* EPEL
  * 我也不知道是啥,貌似东西会多一些,比如htop会依赖这个
  * `yum install -y epel-release` ,替代源:[科技大学](http://mirrors.ustc.edu.cn/help/epel.html)
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
* `ctrl a` 终端开始，`ctrl e`

## todo

* ranger 预览
* [z](https://github.com/rupa/z) 尝试使用
* 一些简单脚本