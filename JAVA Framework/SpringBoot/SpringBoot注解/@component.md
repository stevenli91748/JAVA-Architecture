

# @component

把普通pojo实例化到spring容器中，相当于配置文件中的

@RestController Spring4之后新加入的注解，原来返回json需要@ResponseBody和@Controller配合。
即@RestController是@ResponseBody和@Controller的组合注解。

@RestController
public class HelloController {

    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}
与下面的代码作用一样

@Controller
@ResponseBody
public class HelloController {

    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}
此注解即可以作用在控制器的某个方法上，也可以作用在此控制器类上。当控制器在类级别上添加@RequestMapping注解时，这个注解会应用到控制器的所有处理器方法上。处理器方法上的@RequestMapping注解会对类级别上的@RequestMapping的声明进行补充。

@RestController
@RequestMapping("consumer")
public class ConsumerController {
    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("{id}")
    @HystrixCommand(fallbackMethod = "queryByIdFallback")
    public String queryById(@PathVariable("id") Long id) {
        String url = "http://user-service/user/" + id;
        String user = restTemplate.getForObject(url, String.class);
        return user;
    }
    public String queryByIdFallback(Long id) {
        return "服务器忙！";
    }
}
