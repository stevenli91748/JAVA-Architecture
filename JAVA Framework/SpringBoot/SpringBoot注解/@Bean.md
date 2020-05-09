
# @Bean

注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在
自己的IOC容器中。它是一个方法级别上的注解，主要用在@Configuration注解的类里
