



# @FeignClient

这个注解标识当前是一个端， value 属性是对应的服务名称，也就是你需要调用哪个服务中的接口。

设置熔断等级

@FeignClient
@FeignClient(value = “eureka-client”, configuration = FeignConfig.class)value——指明要访问的服务名称
