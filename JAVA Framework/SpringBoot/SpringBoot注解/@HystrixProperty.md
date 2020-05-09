


```java

@HystrixProperty
@GetMapping("{id}")
@HystrixCommand(
        commandProperties = {
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60")
})
public String queryById(@PathVariable("id") Long id) {
    if(id % 2 == 0) {
        throw new RuntimeException("");
    }
    String url = "http://user-service/user/" + id;
    String user = restTemplate.getForObject(url, String.class);
    return user;
}

```
