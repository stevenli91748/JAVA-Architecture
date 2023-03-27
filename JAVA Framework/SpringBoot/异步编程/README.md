


在Spring Boot中，可以使用异步编程来提高应用程序的性能和并发处理能力。以下是在Spring Boot中编写异步程序的几种方法：

使用@Async注解
使用@Async注解可以轻松地将方法标记为异步方法。在标记为@Async的方法内部，可以使用任何Java线程库或框架来异步执行代码。要使用@Async注解，需要在启动类上添加@EnableAsync注解。

```

@Service
public class MyService {

  @Async
  public void asyncMethod() {
    // 异步执行的代码
  }
}


```
