
# 类型类

类型类注释包括controller、service等，需要重点了解

其中component和bean注解的区别如下：

@Component注解在类上使用表明这个类是个组件类，需要Spring为这个类创建bean。

@Bean注解使用在方法上，告诉Spring这个方法将会返回一个Bean对象，需要把返回的对象注册到Spring的应用上下文中。


[@Controller](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Controller.md)|[@Service](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Service.md)|[@Repository](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Repository.md)|[@Component](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Component.md)|[@Configuration](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Configuration.md)|[@Bean](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Bean.md)|
---|---|---|---|---|---|

# 设置类

重点了解@Autowire和@Qualifier以及bytype、byname等不同的自动装配机制。

[@Required](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Required.md)|[@Autowired](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Autowired.md)|[@Qualifier](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Qualifier.md)|[@Scope](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Scope.md)|
---|---|---|---|

# Web类

主要以了解为主，关注@RequestMapping、@GetMapping、@PostMapping等路径匹配注解，以及@PathVariable、@RequestParam 等参数获取注解。

[@RequestMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40RequestMapping.md)|[@GetMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40GetMapping.md)|[@PostMapping](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40PostMapping.md)|[@PathVariable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40PathVariable.md)|[@RequestParam](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40RequestParam.md)|
---|---|---|---|---|

[@RequestBody](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40RequestBoy.md)|[@ResponseBody](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40ResponseBody.md)|
---|---|

# 功能类

包括@ImportResource引用配置、@ComponentScan注解自动扫描、@Transactional事务注解等等，这里不一一介绍了。

[@ImportResource](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40ImportResource.md)|[@ComponentScan](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40ComponentScan.md)|[@EnableCaching](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40EnableCaching.md)|[@Cacheable](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Cacheable.md)|[@Transactional](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Transactional.md)|[@Aspect](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Aspect.md)|
---|---|---|---|---|---|

[@Pointcut](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Pointcut.md)|[@Scheduled](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring/Spring常用注解/%40Scheduled.md)|
---|---|


# 有用的参考

*  [Spring注解的意义](https://www.cnblogs.com/xiaoxi/p/5935009.html)
* [Spring全注解式开发](https://blog.csdn.net/qq_35170365/article/details/81184435)
