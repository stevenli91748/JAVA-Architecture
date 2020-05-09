# @Autowired

这个注解就是spring可以自动帮你把bean里面引用的对象的setter/getter方法省略，它会自动帮你set/get。

<property name="userDao">

  <ref bean="userDao"/>

</property>

这样你在userService里面要做一个userDao的setter/getter方法。但如果你用了@Autowired的话，你只需要在UserService的实现类中声明即可
