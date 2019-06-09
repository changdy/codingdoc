nodejs的安装最简单的方法自然是使用源码安装 , 但是自从上次发现安装需要大半个钟头之后我就放弃了此种安装方法 . 本篇记录下如何使用编译好的二进制安装

* 打开[node官网下载页](https://nodejs.org/en/download/)
* 在下方选择[All download options](https://nodejs.org/dist/v8.9.4)
* 以`linux-x64.tar.gz`为关键字搜索 (32位请选择`linux-x86.tar.gz`), 并复制链接
* 下载后使用`sudo tar --strip-components 1 -xzvf node-v* -C /usr/local`进行解压
* `node --version`即可看到安装的版本号