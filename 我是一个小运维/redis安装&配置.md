安装过程直接`make`就行了，但是有可能报如下错误

```
zmalloc.h:50:31: error: jemalloc/jemalloc.h: No such file or directory
zmalloc.h:55:2: error: #error "Newer version of jemalloc required"
make[1]: *** [adlist.o] Error 1
make[1]: Leaving directory `/data0/src/redis-2.6.2/src'
make: *** [all] Error 2
```

这个时候则需要使用`make MALLOC=libc`

接下来则需要修改`redis.cnf`文件

* bind IPA IPB

  以空格分开，可以多个ip，如果想所有ip都被允许则`bind 0.0.0.0`

* daemonize yes

  值为yes则可以后台运行

* requirepass password

  设置密码为password

启动redis `src/redis-server redis.conf`,用cli登录`src/redis-cli -a password`