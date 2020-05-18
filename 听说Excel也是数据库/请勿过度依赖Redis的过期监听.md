# 请勿过度依赖Redis的过期监听

## Redis过期监听场景

业务中有类似等待一定时间之后执行某种行为的需求 , 比如30分钟之后关闭订单 . 网上有很多使用Redis过期监听的Demo , 但是其实这是个大坑 , 因为Redis不能确保key在指定时间被删除 , 也就造成了通知的延期 . 不多说 , 跑个测试

## 测试情况

先说环境 , redis 运行在Docker容器中 ,分配了 一个cpu以及512MB内存, 在Docker中执行 `redis-benchmark -t set -r 100000 -n 1000000` 结果如下:

```
====== SET ======
  1000000 requests completed in 171.03 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no
```

其实这里有些不严谨 `benchmark` 线程不应该在Docker容器内部运行 . 跑分的时候大概 benchmark 和redis 主线程各自持有50%CPU

测试代码如下:

```java
@Service
@Slf4j
public class RedisJob {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    public DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    public LocalDateTime end = LocalDateTime.of(LocalDate.of(2020, 5, 12), LocalTime.of(8, 0));

    @Scheduled(cron = "0 56 * * * ?")
    public void initKeys() {
        LocalDateTime now = LocalDateTime.now();
        ValueOperations<String, String> operations = stringRedisTemplate.opsForValue();
        log.info("开始设置key");
        LocalDateTime begin = now.withMinute(0).withSecond(0).withNano(0);
        for (int i = 1; i < 17; i++) {
            setExpireKey(begin.plusHours(i), 8, operations);
        }
        log.info("设置完毕: " + Duration.between(now, LocalDateTime.now()));
    }

    private void setExpireKey(LocalDateTime expireTime, int step, ValueOperations<String, String> operations) {
        LocalDateTime localDateTime = LocalDateTime.now().withNano(0);
        String nowTime = dateTimeFormatter.format(localDateTime);
        while (expireTime.getMinute() < 55) {
            operations.set(nowTime + "@" + dateTimeFormatter.format(expireTime), "A", Duration.between(expireTime, LocalDateTime.now()).abs());
            expireTime = expireTime.plusSeconds(step);
        }
    }
}
```

大概意思就是每小时56分的时候 , 会增加一批在接下来16小时过期的key , 过期时间间隔8秒 , 且过期时间都在55分之前

```java
@Slf4j
@Component
public class RedisKeyExpirationListener extends KeyExpirationEventMessageListener {

    public RedisKeyExpirationListener(RedisMessageListenerContainer listenerContainer) {
        super(listenerContainer);
    }

    public DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    @Autowired
    private StringRedisTemplate stringRedisTemplate;


    @Override
    public void onMessage(Message message, byte[] pattern) {
        String keyName = new String(message.getBody());
        LocalDateTime parse = LocalDateTime.parse(keyName.split("@")[1], dateTimeFormatter);
        long seconds = Duration.between(parse, LocalDateTime.now()).getSeconds();
        stringRedisTemplate.execute((RedisCallback<Object>) connection -> {
            Long size = connection.dbSize();
            log.info("过期key:" + keyName + " ,当前size:" + size + " ,滞后时间" + seconds);
            return null;
        });
    }
}
```

这里是监测到过期之后打印当前的dbSize 以及滞后时间

```java
@Bean
public RedisMessageListenerContainer configRedisMessageListenerContainer(RedisConnectionFactory connectionFactory) {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(100);
    executor.setMaxPoolSize(100);
    executor.setQueueCapacity(100);
    executor.setKeepAliveSeconds(3600);
    executor.setThreadNamePrefix("redis");
    // rejection-policy：当pool已经达到max size的时候，如何处理新任务
    // CALLER_RUNS：不在新线程中执行任务，而是由调用者所在的线程来执行
    executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
    executor.initialize();
    RedisMessageListenerContainer container = new RedisMessageListenerContainer();
    // 设置Redis的连接工厂
    container.setConnectionFactory(connectionFactory);
    // 设置监听使用的线程池
    container.setTaskExecutor(executor);
    // 设置监听的Topic
    return container;
}
```

设置Redis的过期监听 以及线程池信息 , 



最后的测试结果是当key数量小于1万的时候 , 基本上都可以在10s内完成过期通知 , 但是如果数量到3万 , 就有部分key会延迟120s . 顺便贴一下我最新的日志

```
2020-05-13 22:16:48.383  : 过期key:2020-05-13 11:56:02@2020-05-13 22:14:08 ,当前size:57405 ,滞后时间160
2020-05-13 22:16:49.389  : 过期key:2020-05-13 11:56:02@2020-05-13 22:14:32 ,当前size:57404 ,滞后时间137
2020-05-13 22:16:49.591  : 过期key:2020-05-13 10:56:02@2020-05-13 22:13:20 ,当前size:57403 ,滞后时间209
2020-05-13 22:16:50.093  : 过期key:2020-05-13 20:56:00@2020-05-13 22:12:32 ,当前size:57402 ,滞后时间258
2020-05-13 22:16:50.596  : 过期key:2020-05-13 07:56:03@2020-05-13 22:13:28 ,当前size:57401 ,滞后时间202
2020-05-13 22:16:50.697  : 过期key:2020-05-13 20:56:00@2020-05-13 22:14:32 ,当前size:57400 ,滞后时间138
2020-05-13 22:16:50.999  : 过期key:2020-05-13 19:56:00@2020-05-13 22:13:44 ,当前size:57399 ,滞后时间186
2020-05-13 22:16:51.199  : 过期key:2020-05-13 20:56:00@2020-05-13 22:14:40 ,当前size:57398 ,滞后时间131
2020-05-13 22:16:52.205  : 过期key:2020-05-13 15:56:01@2020-05-13 22:16:24 ,当前size:57397 ,滞后时间28
2020-05-13 22:16:52.808  : 过期key:2020-05-13 06:56:03@2020-05-13 22:15:04 ,当前size:57396 ,滞后时间108
2020-05-13 22:16:53.009  : 过期key:2020-05-13 06:56:03@2020-05-13 22:16:40 ,当前size:57395 ,滞后时间13
2020-05-13 22:16:53.110  : 过期key:2020-05-13 20:56:00@2020-05-13 22:14:56 ,当前size:57394 ,滞后时间117
2020-05-13 22:16:53.211  : 过期key:2020-05-13 06:56:03@2020-05-13 22:13:44 ,当前size:57393 ,滞后时间189
2020-05-13 22:16:53.613  : 过期key:2020-05-13 15:56:01@2020-05-13 22:12:24 ,当前size:57392 ,滞后时间269
2020-05-13 22:16:54.317  : 过期key:2020-05-13 15:56:01@2020-05-13 22:16:00 ,当前size:57391 ,滞后时间54
2020-05-13 22:16:54.517  : 过期key:2020-05-13 18:56:00@2020-05-13 22:15:44 ,当前size:57390 ,滞后时间70
2020-05-13 22:16:54.618  : 过期key:2020-05-13 21:56:00@2020-05-13 22:14:24 ,当前size:57389 ,滞后时间150
2020-05-13 22:16:54.819  : 过期key:2020-05-13 17:56:00@2020-05-13 22:14:40 ,当前size:57388 ,滞后时间134
2020-05-13 22:16:55.322  : 过期key:2020-05-13 10:56:02@2020-05-13 22:13:52 ,当前size:57387 ,滞后时间183
2020-05-13 22:16:55.423  : 过期key:2020-05-13 07:56:03@2020-05-13 22:14:16 ,当前size:57386 ,滞后时间159
```

可以看到 ,当数量到达5万的时候 , 大部分都已经滞后了两分钟 , 对于业务方来说已经完全无法忍受了

## 总结

可能到这里 , 你会说Redis 给你挖了一个大坑 , 但其实这些都在文档上写的明明白白

- [How Redis expires keys ](https://redis.io/commands/expire#how-redis-expires-keys)
- [Timing of expired events ](https://redis.io/topics/notifications#timing-of-expired-events)

尤其是在 [Timing of expired events ](https://redis.io/topics/notifications#timing-of-expired-events) 中 , 明确的说明了 "Basically `expired` events **are generated when the Redis server deletes the key** and not when the time to live theoretically reaches the value of zero." , 这两个文章读下来你会感觉 , 卧槽Redis的过期策略其实也挺'Low'的

其实公众号看多了 , 你会发现大部分Demo都是互相抄来抄去 , 以及翻译官方Demo . 建议大家还是谨慎一些 , 真要使用的话 , 最好读一下官方文档 , 哪怕用百度翻译也要有一些自己的理解 .

文章比较枯燥 , 感谢大家耐心阅读 , 如有建议 恳请留言.