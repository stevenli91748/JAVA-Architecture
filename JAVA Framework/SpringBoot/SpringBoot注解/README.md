

@SpringBootApplication包含了@ComponentScan、@EnableAutoConfiguration、@SpringBootConfiguration三个注解

而@SpringBootConfiguration注解包含了@Configuration注解。也就是springboot的自动配置功能。

@Conditional注解就是控制自动配置的生效条件的注解，例如bean或class存在、不存在时进行配置，当满足条件时进行配置等等。

[Spring3.X 注解精华](https://blog.csdn.net/chen19901211/article/details/84227368?utm_medium=distribute.pc_relevant.none-task-blog-OPENSEARCH-9.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-9.nonecase)|[Spring中的各种注解深入理解](https://zhuanlan.zhihu.com/p/115107628)|[标记接口,注解和注解处理器的前世今生](https://zhuanlan.zhihu.com/p/143143682)|[Spring Boot 这么火，常用注解和原理都给你整理好了！](https://zhuanlan.zhihu.com/p/97711788)|
---|---|---|---|


# [Java 元注解](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484195&idx=1&sn=e28f9c3a2f7628061fe15bef668f2ea7&scene=21#wechat_redirect)

[@Targe](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Target%20.md)|[@Retention](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Retention.md)|[@Documented](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Documented.md)|[@Inherited](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Inherited.md)|@interface|[@Native](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Native.md)|[@Repeatable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Repeatable.md)|
---|---|---|---|---|---|---|

# Spring的注解
 
* [Spring Core Annotations](https://www.baeldung.com/spring-core-annotations)
* [Spring Web Annotations](https://www.baeldung.com/spring-mvc-annotations)
* [Spring Scheduling Annotations](https://www.baeldung.com/spring-scheduling-annotations)
* [Spring Data Annotations](https://www.baeldung.com/spring-data-annotations)
* [Spring Bean Annotations](https://www.baeldung.com/spring-bean-annotations)

[@Autowired](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Autowired.md)|[@Bean](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Bean.md)|
---|---|


# Spring Boot的注解

* [Spring Boot框架里经常用到的注解 ](https://www.pianshen.com/article/8357683291/)
* [Spring Boot Annotations](https://www.baeldung.com/spring-boot-annotations) 
 
[@SpringBootApplication](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringBootApplication.md)|[@SpringBootConfiguration](https://github.com/stevenli91748/JAVA-Architecture/tree/master/JAVA%20Framework/SpringBoot/SpringBoot注解)|[@EnableAutoConfiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAutoConfiguratio.md)|
---|---|---|

### Spring Boot配置绑定
[@ConfigurationProperties](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ConfigurationProperties.md)|[@Value](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Value.md)|[@PropertySource ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PropertySource.md)|
---|---|---|

### Spring Boot导入Spring配置

[@ImportResource](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ImportResource.md)|
---|


@ConditionalOnBean|[@ConditionalOnClass](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ConditionalOnClass.md)|@ConditionalOnExpression|@ConditionalOnMissingBean|[@ConditionalOnProperty](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ConditionalOnProperty.md)|
---|---|---|---|---|

@ConditionalOnMissingClass|@ConditionalOnNotWebApplication|[@Conditional](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Conditional.md)|
---|---|---|



[@ResponseStatus](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ResponseStatus.md)|
---|

[@Configuration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@Configuration.md)|[@ComponentScan](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@ComponentScan.md)|[@RunWith](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RunWith.md)|[@SpringBootTest](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringBootTest.md)|[@MapperScan](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40MapperScan.md)|
---|---|---|---|---|

[@RequestPart](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestPart.md)|
---|

[@Service](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Service.md)|[@Controller](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Controller.md)|[@RequestMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestMapping.md)|[@ResponseBody](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ResponseBody.md)|[@EndPoint](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EndPoint.md)|[@QueryParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@QueryParam.md)|
---|---|---|---|---|---|

[@repository](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40repository.md)|[@component](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40component.md)|[@RestController](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot注解/%40RestController.md)|[@GetMapping与 @PostMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40GetMapping%E4%B8%8E%20%40PostMapping.md)|[@EnableEurekaServer](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableEurekaServer.md)|
---|--|---|---|---|

[@EnableDiscoveryClient与@EnableEurekaClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableDiscoveryClient%E4%B8%8E%40EnableEurekaClient.md)|[@LoadBalanced](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40LoadBalanced.md)|[@EnableCircuitBreaker](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableCircuitBreaker.md)|[@EnableHystrix](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableHystrix.md)|
---|------|---|---|

[@SpringCloudApplication](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringCloudApplication.md)|[@HystrixCommand](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40HystrixCommand.md)|[@DefaultProperties](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40DefaultProperties.md)|[@HystrixProperty](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40HystrixProperty.md)|[@FeignClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40FeignClient.md)|
---|--|---|---|---|

[@EnableFeignClient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableFeignClient.md)|[@EnableZuulProxy](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableZuulProxy.md)|
---|---

# Spring的注解

[@Mapper](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Mapper.md)|[@EnableTransactionManagement ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableTransactionManagement.md)|[@Transactional](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableTransactionManagement.md)|[@Async](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Async.md)|[@EnableAsync](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAsync.md)|[@ContextConfiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ContextConfiguration%20.md)|
---|---|---|---|---|---|

[@SpringApplicationConfiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringApplicationConfiguration.md)|[@Basic](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Basic.md)|[@JsonBackReference](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40JsonBackReference.md)|[@RepositoryRestResourcepublic](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RepositoryRestResourcepublic.md)|[@Scope](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/@Scope.md)|
---|---|---|---|---|

[@PostConstruct](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PostConstruct.md)|[@PreDestroy](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PreDestroy.md)|[@ActiveProfiles](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ActiveProfiles.md)|[@profile](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40profile.md)|[@EnableScheduling](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableScheduling.md)|
---|---|---|---|---|

[@Scheduled](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Scheduled.md)|[@WebAppCofiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40WebAppCofiguration.md)|[@Order](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Order.md)|
---|---|---|

[@EnableAaspectJAutoProxy](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAaspectJAutoProxy.md)|[@EnableWebMVC](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableWebMVC.md)|[@EnableConfigurationProperties](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableConfigurationProperties%20.md)|[@EnableJpaRepositories](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableJpaRepositories%20.md)|[@EnableCaching](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableCaching.md)|
---|---|---|---|---|

[@EnableWebSecurity](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableWebSecurity.md)|[@WithMockUser](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40WithMockUser.md)|[@WithUserDetails](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40WithUserDetails.md)|[@WebIntegrationTest](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40WebIntegrationTest.md)|
---|---|---|---|

[@InitBinder](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40InitBinder.md)|[@ModelAttrbuute](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ModelAttrbuute.md)|[@SuppressWarnings](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SuppressWarnings.md)|[@Resource](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Resource.md)|[@Qualifier](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Qualifier.md)|[@Inject](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Inject.md)|
---|---|---|---|---|---|


[@PropertySource](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PropertySource.md)|
---|

# springMVC相关注解

[@PathVariable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PathVariable.md)|
[@RequestParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestParam.md)|[@Import](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Import.md)|[@RequestBody](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestBody.md)|[@RequestHeader](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40RequestHeader.md)|
---|---|---|---|---|

[@CookieValue](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40CookieValue.md)|[@PathParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PathParam.md)|[@SessionAttributes](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SessionAttributes.md)|[@ModelAttributes](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ModelAttributes.md)|
---|---|---|---|

# AOP切面编程注解:

[@Aspect](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Aspect.md)|[@After @Before. @Around](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40After%20%40Before.%20%40Around.md)|[@PointCut](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PointCut.md)|[@Cacheable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Cacheable.md)|
---|---|---|---|

# Swagger注解

[@EnableSwagger2Doc](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableSwagger2Doc.md)|[@ApiOperation](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiOperation.md)|[@ApiResponses](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiResponses.md)|[@PostMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PostMapping.md)|[@ApiModel](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiModel.md)|[@ApiModelProperty](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiModelProperty.md)|
---|---|---|---|---|---|

[@Api](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Api.md)|[@ApiParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiParam.md)|[@ApiResponse](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApiResponse.md)|[@ApilmplicitParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApilmplicitParam.md)|[@ApilmplicitParams](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ApilmplicitParam.md)|
---|---|---|---|---|



# JPA的注解
[@Entity](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Entity.md)|[@Table](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Table.md)|[@Data](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Data.md)|[@Id](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Id.md)|[@GeneratedValue](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40GeneratedValue.md)
---|---|---|---|---|




[@MappedSuperClass](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40MappedSuperClass.md)|[@NoRepositoryBean](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40NoRepositoryBean.md)|[@Column](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Column.md)|[@SequenceGeneretor](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SequenceGeneretor.md)|[@Transient](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Transient.md)|
---|---|---|---|---|

[@JsonIgnore](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40JsonIgnore.md)|[@JoinColumn](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40JoinColumn.md)|[@OneToOne](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40OneToOne%E3%80%81%40OneToMany%E3%80%81%40ManyToOne.md)|[@Enumerated](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Enumerated.md)|
---|---|---|---|

[@ManyToMany](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ManyToMany.md)|[@idClass](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40idClass.md)|[@OneToMany](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40OneToMany.md)|[@ManyToOne](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ManyToOne.md)|[@EmbeddedId](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EmbeddedId.md)|[@OrderBy](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40OrderBy.md)|[@JoinTable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40JoinTable.md)|
---|---|---|---|---|---|---|



[@Temporal](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Temporal.md)|
---|

# 数据校验

[@NumberFormatter](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40NumberFormatter.md)|@CurrencyFormatter|[@PercentFormatter](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40PercentFormatter.md)|@DateTimeFormat|@Valid|@Pattern|
---|---|---|---|---|---|


[@Null](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40Null.md)|[@NotNull](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40NotNull.md)|@AssertTrue|@AssertFalse|@Min(value)|
---|---|---|---|---|

@Max(value)|@DecimalMin(value)|@DecimalMax(value)|@Size|@Digits|@Past|@Future|
---|---|---|---|---|---|---|

# 全局异常处理

[@ControllerAdvice](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ControllerAdvice.md)|[@ExceptionHandler（Exception.class）](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40ExceptionHandler.md)|
---|---|



---

||@Value|@Inject|@Qualifier|@Resource|@EnableWebMvc|
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

|@Table||@id|@GeneratedValue|
---|---|---|---|---|---|---|

@SequenceGeneretor|@Transient|@Basic|@Jsonlgnore|@JoinColumn|
---|---|---|---|---|

@OneToOne|@OneToMany|@ManyToOne|
---|---|---|


# 视频

* [尚硅谷--spring-spring注解驱动开发-源码版-雷丰阳](https://www.bilibili.com/video/BV1ME411o7Uu?from=search&seid=2831014032148563470)

# 有用的参考
* [接近8000字的Spring/SpringBoot常用注解总结](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486635&idx=1&sn=9028d00727923f51240053666c9eb3a4&chksm=cea24360f9d5ca76e8afc7159c0dc463cf83adb798e68a39b2ba4f92f66971a60b70855384c4&token=248911937&lang=zh_CN#rd)
* [Spring Boot 微服务常用注解大全](https://1o24bbs.com/t/topic/13499)
* [Java开发必须掌握的 20+ 种 Spring 常用注解](https://www.jianshu.com/p/5bbbc2f5552c)
* [自己写注解，了解注解的原理](https://www.bilibili.com/read/cv4802402)
* [SpringBoot注解最全详解(整合超详细版本)](https://blog.csdn.net/weixin_40753536/article/details/81285046)
* [springboot的注解的作用说明（全）](https://blog.csdn.net/fenlin88l/article/details/89466723?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
