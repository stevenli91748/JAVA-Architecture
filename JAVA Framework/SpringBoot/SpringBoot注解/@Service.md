

# @Service

加上这一行以后，将自动扫描路径下面的包，如果一个类带了@Service注解，将自动注册到Spring容器，不需要再在applicationContext.xml文件定义bean了

@Service("courseDAO")
@Scope("prototype")
public class CourseDAOImpl extends HibernateDaoSupport implements CourseDAO{

    ......
  
}

其作用就相当于applicationContext.xml文件里面的：

<bean id="courseDAO"
     class="com.hzhi.course.dao.CourseDAOImpl" scope="prototype">
     ......    
</bean>  
