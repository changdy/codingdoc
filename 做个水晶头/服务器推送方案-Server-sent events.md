# 服务器推送方案-Server-sent events

> 一直以为真正的服务器推送只有 ws 一个方案 , 偶然间看到了 Server-sent events , 比较轻量级 ,可以了解下

Server-sent events 是H5标准的一部分 , 相比较ws更轻量一些 , 并且天然支持重连 , 实现也比较简单一些 , 不过IE/EDGE这对老妖精仍然不支持.实现仍旧沿用了HTTP协议 , 相当于告诉客户端不要断开链接. 

## Spring Boot代码

```java
@RestController
public class SseEmitterController {
    private Logger log = LoggerFactory.getLogger(this.getClass());

    public volatile SseEmitter sseEmitter;
    @GetMapping("/test")
    public SseEmitter init() {
        SseEmitter currentEmitter = new SseEmitter();
        this.sseEmitter = currentEmitter;
        currentEmitter.onError(x -> log.info("error:" + currentEmitter));
        currentEmitter.onCompletion(() -> log.info("complete:" + currentEmitter));
        currentEmitter.onTimeout(() -> log.info("timeout:" + currentEmitter));
        // 必须要返回 SseEmitter 对象
        return currentEmitter;
    }
    @PreDestroy
    public void close() throws IOException {
        if (sseEmitter != null) {
            sseEmitter.send("正在重启");
            sseEmitter.complete();
        }
    }
    @Scheduled(fixedDelay = 60 * 1000, initialDelay = 2000)
    public void syncEveryMin() throws IOException {
        if (sseEmitter != null) {
            sseEmitter.send("hello");
            this.sseEmitter = null;
        }
    }
}
```

## JS代码

```javascript
new EventSource('/test')
    .onmessage= (x)=>console.log(x)
```

## 需要注意的一些事项

* 不建议使用tomcat 作为 Server-sent events 服务器的容器 , 可以尝试netty或者Undertow , 相比较而言占用资源更少 , 并且对该节点进行单独部署
* 由于断开服务器之后 前端会自动尝试 , 所以 前端需要做好判断下线的条件
* IE/EDGE系列并不支持SSE

## 引用

* [Server-Sent Events 教程](http://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)
* [Server-sent events](https://developer.mozilla.org/zh-CN/docs/Server-sent_events)