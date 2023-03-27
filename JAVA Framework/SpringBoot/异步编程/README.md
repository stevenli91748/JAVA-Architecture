


在Spring Boot中，可以使用异步编程来提高应用程序的性能和并发处理能力。以下是在Spring Boot中编写异步程序的几种方法：

## 使用@Async注解

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

## 使用CompletableFuture

CompletableFuture是Java 8中引入的一个强大的异步编程库。它可以在Spring Boot应用程序中使用，通过创建异步任务来提高应用程序的性能。在使用CompletableFuture时，可以将一些耗时的任务异步执行，然后使用join()方法等待任务完成。


```
@Service
public class MyService {

  public CompletableFuture<String> asyncMethod() {
    return CompletableFuture.supplyAsync(() -> {
      // 异步执行的代码
      return "Hello World!";
    });
  }
}

```

## 使用WebFlux
WebFlux是Spring框架中的响应式编程模型，它提供了异步非阻塞的I/O操作。在使用WebFlux时，可以使用响应式流和异步操作来处理请求。

```

@RestController
public class MyController {

  @GetMapping("/async")
  public Mono<String> asyncMethod() {
    return Mono.fromCallable(() -> {
      // 异步执行的代码
      return "Hello World!";
    });
  }
}


```

以上是在Spring Boot中编写异步程序的几种方法



