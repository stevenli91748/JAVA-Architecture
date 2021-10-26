# HTTP客户端

 * 底层协议
   * Java’s HttpURLConnection and HttpsURLConnection---Feign在默认情况下使用的是JDK原生的URLConnection发送HTTP请求，没有连接池, 可参考该文 [Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
   * The new HttpClient, introduced in Java 11---可参考该文 [Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
   * Apache HTTPClient--- 可参考该文[ Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
   * [OkHttp](https://www.jianshu.com/p/1cb7c8d35dbe)---可参考该文[ Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
   * AsyncHttpClient---可参考该文[ Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
   * Jetty HttpClient--可参考该文[ Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
 * 微服务之间的HTTP客户端
      * RPC方式
      * [RESTful HTTP方式有几种服务调用方法](https://mp.weixin.qq.com/s/uSYygJRwav5GgrG4um8R4w)
        * [OpenFeign---最方便、最优雅的方式是通过Spring Cloud Open Feign进行服务间的调用 ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/OpenFeign.md)
        * [Feign](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Feign.md)（第一种调用方法）---微服务间调用，个人使用feign，同时使用OKhttp替换feign中默认的httpClient
        * Ribbon + RestTemplate（第二种调用方法）---第三方服务调用, 在使用Ribbon+ RestTemplate时，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐



# 参考
* [HttpClient、OKhttp、RestTemplate接口调用对比，选择一个优秀的 HTTP Client 的重要性](https://www.codenong.com/cs106097201/)
* [HttpClient、okhttp和RestTemplate的区别](https://blog.csdn.net/riemann_/article/details/109168384)
* [Which Java HTTP client should I use in 2020?](https://www.mocklab.io/blog/which-java-http-client-should-i-use-in-2020/)
* [参数校验](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%20%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%8F%82%E6%95%B0%E6%A0%A1%E9%AA%8C.md)
* [HttpMessageConverter的作用及替换---输入/输出数据格式和JAVA对象的转换](https://www.jianshu.com/p/333ed5ee958d)
* [精讲RestTemplate](http://www.zimug.com/tag/resttemplate)
* [精讲响应式webclient](http://www.zimug.com/java/spring/%e7%b2%be%e8%ae%b2%e5%93%8d%e5%ba%94%e5%bc%8fwebclient%e7%ac%ac1%e7%af%87-%e5%93%8d%e5%ba%94%e5%bc%8f%e9%9d%9e%e9%98%bb%e5%a1%9eio%e4%b8%8e%e5%9f%ba%e7%a1%80%e7%94%a8%e6%b3%95/.html)
* [SpringBoot实现本地存储文件上传及提供HTTP访问服务](http://www.zimug.com/java/spring/springboot%e5%ae%9e%e7%8e%b0%e6%9c%ac%e5%9c%b0%e5%ad%98%e5%82%a8%e6%96%87%e4%bb%b6%e4%b8%8a%e4%bc%a0%e5%8f%8a%e6%8f%90%e4%be%9bhttp%e8%ae%bf%e9%97%ae%e6%9c%8d%e5%8a%a1/.html)
* [Spring Boot 中处理跨域](https://mrbird.cc/Spring-Boot-Deal-CORS.html)
* [Spring Boot中使用thymeleaf](https://mrbird.cc/Spring-Boot%E4%BD%BF%E7%94%A8thymeleaf.html)
