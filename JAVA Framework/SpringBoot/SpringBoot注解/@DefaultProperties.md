


# @DefaultProperties
读取properties文件

如果每一个方法都提供一个熔断处理方法，那么会显的类特别的臃肿，所以我们可以提供一个通用的处理方式，也就是在类上面添加一个注解@DefaultProperties

```java

@RestController
@RequestMapping("consumer")
@DefaultProperties(defaultFallback = "defaultFallback")
public class ConsumerController {
    @Autowired
    private RestTemplate restTemplate;
    @GetMapping("{id}")
    @HystrixCommand//开启启用
    public String queryById(@PathVariable("id") Long id) {
        String url = "http://user-service/user/" + id;
        String user = restTemplate.getForObject(url, String.class);
        return user;
    }
    public String defaultFallback() {
        return "抱歉，服务器hin忙！";
    }
}

```
