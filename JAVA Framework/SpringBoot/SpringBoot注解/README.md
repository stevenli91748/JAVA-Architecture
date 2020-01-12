

@SpringBootApplication包含了@ComponentScan、@EnableAutoConfiguration、@SpringBootConfiguration三个注解

而@SpringBootConfiguration注解包含了@Configuration注解。也就是springboot的自动配置功能。

@Conditional注解就是控制自动配置的生效条件的注解，例如bean或class存在、不存在时进行配置，当满足条件时进行配置等等。

[@SpringBootApplication](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40SpringBootApplication.md)|[@RestController](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot注解/%40RestController.md)|[@SpringBootConfiguration](https://github.com/stevenli91748/JAVA-Architecture/tree/master/JAVA%20Framework/SpringBoot/SpringBoot注解)|[@EnableAutoConfiguration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%E6%B3%A8%E8%A7%A3/%40EnableAutoConfiguratio.md)|
---|---|---|---|

@Conditional|
---|

@ConditionalOnBean|@ConditionalOnClass|@ConditionalOnExpression|@ConditionalOnMissingBean|
---|---|---|---|

@ConditionalOnMissingClass|@ConditionalOnNotWebApplication|
---|---|




# 有用的参考



