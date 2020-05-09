

# @LoadBalanced

在使用springcloud ribbon客户端负载均衡的时候，可以给RestTemplate bean 加一个@LoadBalanced注解，就能让这个RestTemplate在请求时拥有客户端负载均衡
的能力

```java

@Bean
@LoadBalanced
public RestTemplate restTemplate() {
// 这次我们使用了OkHttp客户端,只需要注入工厂即可
return new RestTemplate();

```
