

# @EnableAsync
开启异步任务的支持(多线程)

注解的意思是可以异步执行，就是开启多线程的意思。可以标注在方法、类上。
为了让@Async注解能够生效，需要在Spring Boot的主程序中配置@EnableAsync
