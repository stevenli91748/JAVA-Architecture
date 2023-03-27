


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


# 在Spring Boot中，写异步程序最流行的方法是使用@Async注解配合ThreadPoolTaskExecutor线程池。以下是具体步骤：

在Spring Boot应用的主类上添加@EnableAsync注解，启用异步执行。

在异步执行的方法上添加@Async注解。

创建一个线程池，用于异步执行任务。可以在配置类中定义ThreadPoolTaskExecutor bean，设置线程池的属性，如最小线程数、最大线程数、队列容量等。

在异步执行的方法中，调用AsyncTaskExecutor的execute方法，将任务提交到线程池中执行。

示例代码如下：

···

import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

@Service
public class AsyncService {
 
    @Async
    public void asyncMethod() {
        // 异步执行的方法体
    }
 
}

···
---------------------------------------------------------------------------------------------
···

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;
import java.util.concurrent.Executor;

@Configuration
@EnableAsync
public class AsyncConfig {
 
    @Bean
    public Executor asyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(50);
        executor.setQueueCapacity(1000);
        executor.setThreadNamePrefix("AsyncExecutor-");
        executor.initialize();
        return executor;
    }
 
}



···


以上是使用@Async注解配合ThreadPoolTaskExecutor线程池实现Spring Boot异步执行的方法，这种方法简单易懂、易于维护，并且能够充分利用CPU资源

