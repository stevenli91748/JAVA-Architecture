ApplicationContext保存了ioc的整个应用上下文，可以通过其中的beanfactory获取到任意到bean；

BeanFactory主要的作用是根据bean definition来创建具体的bean；

BeanWrapper是对Bean的包装，一般情况下是在spring ioc内部使用，提供了访问bean的属性值、属性编辑器注册、类型转换等功能，方便ioc容器用统一的方式来访问
bean的属性；

FactoryBean通过getObject方法返回实际的bean对象，例如motan框架中referer对service的动态代理就是通过FactoryBean来实现的。


[ApplicationContext](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E6%A0%B8%E5%BF%83%E6%8E%A5%E5%8F%A3%E7%B1%BB---ApplicationContext.md)|[BeanFactory](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E6%A0%B8%E5%BF%83%E6%8E%A5%E5%8F%A3%E7%B1%BB---BeanFactory.md)|[BeanWrapper](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E6%A0%B8%E5%BF%83%E6%8E%A5%E5%8F%A3%E7%B1%BB---BeanWrapper.md)|[FactoryBean](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E6%A0%B8%E5%BF%83%E6%8E%A5%E5%8F%A3%E7%B1%BB---FactoryBean.md)|
---|---|---|---|
