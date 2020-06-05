
* [深入分析Spring 与 Spring MVC容器](https://juejin.im/post/5a38be61f265da431c706d2d)

core部分包含4个模块,它们的完整依赖关系

因为spring-core依赖了commons-logging，而其他模块都依赖了spring-core，所以整个spring框架都依赖了commons-logging，如果有自己的日志实现如log4j，可以排除对commons-logging的依赖，没有日志实现而排除了commons-logging依赖，编译报错

[spring-core]("依赖注入IoC与DI的最基本实现")|[spring-beans]("Bean工厂与bean的装配")|[spring-context]("spring的context上下文即IoC容器")|[spring-expression]("spring表达式语言")|
---|---|---|---|
