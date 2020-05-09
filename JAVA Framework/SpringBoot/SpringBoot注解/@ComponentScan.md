
# @ComponentScan
如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。

我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。如果没有配置的话，Spring Boot会扫描启动类所在包下以及子包下的使用了@Service,@Repository等注解的类

如果不设置basePackage的话 默认会扫描包的所有类，所以最好还是写上basePackage （@componentScan({" ... "}）,减少加载时间。
默认扫描**/*.class路径 比如这个注解在com.wuhulala 下面 ，那么会扫描这个包下的所有类还有子包的所有类,

启用组件扫描，这样写的Web控制器类和其他组件才能被 自动发现并注册为Spring应用程序上下文里的Bean

表示程序启动是，自动扫描当前包及子包下所有类

会自动扫描指定包下的全部标有@Component的类，并注册成bean
