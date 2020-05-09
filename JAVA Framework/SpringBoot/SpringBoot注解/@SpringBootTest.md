

# @SpringBootTest

进行单元测试

```java

@RunWith(SpringRunner.class)
@SpringBootTest(classes = HttpDemoApplication.class)
public class HttpDemoApplicationTests {

	@Autowired
	private RestTemplate restTemplate;

	@Test
	public void httpGet() {
		User user = this.restTemplate.getForObject("http://localhost/hello/1", User.class);
		System.out.println(user);
	}
}


```
