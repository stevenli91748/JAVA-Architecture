
# @ComponentScan
如果不设置basePackage的话 默认会扫描包的所有类，所以最好还是写上basePackage （@componentScan({" ... "}）,减少加载时间。
默认扫描**/*.class路径 比如这个注解在com.wuhulala 下面 ，那么会扫描这个包下的所有类还有子包的所有类,

启用组件扫描，这样写的Web控制器类和其他组件才能被 自动发现并注册为Spring应用程序上下文里的Bean

表示程序启动是，自动扫描当前包及子包下所有类

会自动扫描指定包下的全部标有@Component的类，并注册成bean
