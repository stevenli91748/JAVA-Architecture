


# @Qualifier

[spring @Qualifier注解](https://blog.csdn.net/clerk0324/article/details/25198457)

当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定。与@Autowired配合使用。@Qualifier限定描述符除了能根据名字进行注入，但能进行更细粒度的控制如何选择候选者，具体使用方式如下：

```java

@Autowired 
@Qualifier(value = “demoInfoService”)
private DemoInfoService demoInfoService;

```
个人总结：

@Autowired//默认按type注入
@Qualifier("cusInfoService")//一般作为@Autowired()的修饰用
@Resource(name="cusInfoService")//默认按name注入，可以通过name和type属性进行选择性注入

一般@Autowired和@Qualifier一起用，@Resource单独用。

当然没有冲突的话@Autowired也可以单独用

