# @Autowired

[@Autowired 与@Resource的区别（详细）](https://blog.csdn.net/weixin_40423597/article/details/80643990)


这个注解就是spring可以自动帮你把bean里面引用的对象的setter/getter方法省略，它会自动帮你set/get。

<property name="userDao">

  <ref bean="userDao"/>

</property>

这样你在userService里面要做一个userDao的setter/getter方法。但如果你用了@Autowired的话，你只需要在UserService的实现类中声明即可



个人总结：

@Autowired//默认按type注入

@Qualifier("cusInfoService")//一般作为@Autowired()的修饰用

@Resource(name="cusInfoService")//默认按name注入，可以通过name和type属性进行选择性注入

 

一般@Autowired和@Qualifier一起用，@Resource单独用。

当然没有冲突的话@Autowired也可以单独用
