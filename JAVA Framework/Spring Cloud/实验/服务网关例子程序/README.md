
# Spring Cloud Gateway
* 整合 Spring Cloud 组件
  * 整合服务注册中心
    * Spring Cloud Gateway整合Eureka例子
    * Spring Cloud Gateway整合Consul例子
    * Spring Cloud Gateway整合Nacos例子  
  * 整合Hystrix的断路器功能例子
* 限流
  * Spring Cloud Gateway IP限流例子
  * Spring Cloud Gateway 用户限流例子
  * Spring Cloud Gateway 接口限流例子
* 断言（Predicate）
  * Header路由断言工厂例子---Header路由断言工厂用于根据配置的路由header信息进行断言匹配路由，匹配成功进行转发，否则不进行转发
  * Cookie路由断言工厂例子---Cookie路由断言工厂会取两个参数——cookie名称对应的key和value。当请求中携带的cookie和Cookied断言工厂中配置的cookie一致，则路由匹配成功，否则匹配不成功
  * After路由断言工厂例子---After Route Predicate Factory中会取一个UTC时间格式的时间参数，当请求进来的当前时间在配置的UTC时间之后，则会成功匹配，否则不能成功匹配。
  * Before路由断言工厂例子---Before路由断言工厂会取一个UTC时间格式的时间参数，当请求进来的当前时间在路由断言工厂之前会成功匹配，否则不能成功匹配
  * Between路由断言工厂例子---Between路由断言工厂会取一个UTC时间格式的时间参数，当请求进来的当前时间在配置的UTC时间工厂之间会成功匹配，否则不能成功匹配
  * Host路由断言工厂例子 ---Host路由断言工厂根据配置的Host，对请求中的Host进行断言处理，断言成功则进行路由转发，否则不转发
