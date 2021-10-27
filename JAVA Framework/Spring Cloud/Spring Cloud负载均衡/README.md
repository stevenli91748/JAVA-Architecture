
  * 负载均衡分类    
    * 集中式负载均衡---集中式负载均衡指位于因特网与服务提供者之间，并负责把网络请求转发到各个提供单位，这时候Nginx与F5就可以划为一类了，也可以称为服务端负载均衡
      * LVS
      * NGINX
    * 进程内负载均衡---进程内负载均衡是指从一个实例库选取一个实例进行流量导入，在微服务的范畴内，实例库一般是存储在Eureka、Consul、Zookeeper、etcd这样的注册中心，而此时的负载均衡器就是类似Ribbon的IPC（Inter-Process Communication，进程间通信）组件，因此，进程内负载均衡也叫作客户端负载均衡
      * [Ribbon---负载均衡](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Ribbon.md)
      * Spring Cloud LoadBalancer
      * RestTemplate的负载均衡机制
