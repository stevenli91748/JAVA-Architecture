

@SpringBootApplication包含了@ComponentScan、@EnableAutoConfiguration、@SpringBootConfiguration三个注解

而@SpringBootConfiguration注解包含了@Configuration注解。也就是springboot的自动配置功能。

@Conditional注解就是控制自动配置的生效条件的注解，例如bean或class存在、不存在时进行配置，当满足条件时进行配置等等。



# 元注解

@Targe|@Retention|
---|---|

# Spring Boot的注解
[@SpringBootApplication](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringBootApplication.md)|[@SpringBootConfiguration](https://github.com/stevenli91748/JAVA-Architecture/tree/master/JAVA%20Framework/SpringBoot/SpringBoot注解)|[@EnableAutoConfiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAutoConfiguratio.md)|
---|---|---|


@ConditionalOnBean|@ConditionalOnClass|@ConditionalOnExpression|@ConditionalOnMissingBean|@Conditional|
---|---|---|---|---|

@ConditionalOnMissingClass|@ConditionalOnNotWebApplication|
---|---|

[@Bean](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Bean.md)|[@Configuration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@Configuration.md)|[@ComponentScan](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@ComponentScan.md)|[@RunWith](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RunWith.md)|[@SpringBootTest](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringBootTest.md)|[@Autowired](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Autowired.md)|[@MapperScan](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40MapperScan.md)|
---|---|---|---|---|---|---|

[@Table](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Table.md)|[@Data](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Data.md)|[@Id](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Id.md)|[@GeneratedValue](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40GeneratedValue.md)|[@Service](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Service.md)|[@Controller](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Controller.md)|[@RequestMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestMapping.md)|[@ResponseBody](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ResponseBody.md)|
---|--|---|---|---|---|---|---|

[@repository](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40repository.md)|[@component](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40component.md)|[@RestController](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot注解/%40RestController.md)|[@GetMapping与 @PostMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40GetMapping%E4%B8%8E%20%40PostMapping.md)|[@EnableEurekaServer](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableEurekaServer.md)|
---|--|---|---|---|

[@EnableDiscoveryClient与@EnableEurekaClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableDiscoveryClient%E4%B8%8E%40EnableEurekaClient.md)|[@LoadBalanced](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40LoadBalanced.md)|[@EnableCircuitBreaker](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableCircuitBreaker.md)|[@EnableHystrix](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableHystrix.md)|
---|------|---|---|

[@SpringCloudApplication](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringCloudApplication.md)|[@HystrixCommand](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40HystrixCommand.md)|[@DefaultProperties](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40DefaultProperties.md)|[@HystrixProperty](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40HystrixProperty.md)|[@FeignClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40FeignClient.md)|
---|--|---|---|---|

[@EnableFeignClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableFeignClient.md)|[@EnableZuulProxy](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableZuulProxy.md)|
---|---

# Spring的注解

[@Mapper](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Mapper.md)|[@EnableTransactionManagement ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableTransactionManagement.md)|[@Transactional](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableTransactionManagement.md)|[@Async](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Async.md)|[@EnableAsync](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAsync.md)|
---|---|---|---|---|





@ImportResource|@Import|@Value|@Inject|@Qualifier|@Resource|@EnableWebMvc|
---|---|---|---|---|---|---|

@PathVariable|@JsonBackReference|@RepositoryRestResourcepublic|@RequestParam|
---|---|---|---|

@MappedSuperclass|@Getter|Setter|@Data|@Log4j2|@Log4j|@NoArgsConstructor|@val|
---|---|---|---|---|---|---|---|

@AllArgsConstructor|@EqualsAndHashCode|@toString|@RequiredArgsConstructor|@NonNull|
---|---|---|---|---|

@ResponseStatus|@WebMethod|@WebResult|@WebParam|@BeforeClass|@AfterClass|
---|---|---|---|---|---|

@Runwith|@interface|
---|---|


# 当你在执行各种持久化方法的时候，实体的状态会随之改变，状态的改变会引发不同的生命周期事件。这些事件可以使用不同的注释符来指示发生时的回调函数。

@javax.persistence.PostLoad|@javax.persistence.PostPersist|@javax.persistence.PrePersist|@javax.persistence.PreUpdate|
---|---|---|---|

@javax.persistence.PostUpdate|@javax.persistence.PreRemove|@javax.persistence.PostRemove|
---|---|---|



# 异常处理

@ControllerAdvice|@ExceptionHandler|
---|---|







# JPA注解

@Entity|@Table|@MappedSuperClass|@NoRepositoryBean|@Column|@id|@GeneratedValue|
---|---|---|---|---|---|---|

@SequenceGeneretor|@Transient|@Basic|@Jsonlgnore|@JoinColumn|
---|---|---|---|---|

@OneToOne|@OneToMany|@ManyToOne|
---|---|---|


# 视频

* [尚硅谷--spring-spring注解驱动开发-源码版-雷丰阳](https://www.bilibili.com/video/BV1ME411o7Uu?from=search&seid=2831014032148563470)

# 有用的参考
* [Spring Boot 微服务常用注解大全](https://1o24bbs.com/t/topic/13499)
* [Java开发必须掌握的 20+ 种 Spring 常用注解](https://www.jianshu.com/p/5bbbc2f5552c)
* [自己写注解，了解注解的原理](https://www.bilibili.com/read/cv4802402)
* [SpringBoot注解最全详解(整合超详细版本)](https://blog.csdn.net/weixin_40753536/article/details/81285046)
* [springboot的注解的作用说明（全）](https://blog.csdn.net/fenlin88l/article/details/89466723?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
