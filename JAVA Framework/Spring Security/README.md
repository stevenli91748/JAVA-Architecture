

**Spring Security 的关键评分位列所有 Java 项目的第 29 位。很遗憾的是，Shiro 并未上榜, 相比于 Shiro 来说，Spring Security 的学习曲线更陡峭。但是，从生态等方面来说，Spring Security 依然是你做身份验证以及权限控制时的首选框架**

# 过滤器登录验证细节

<a href="https://ibb.co/bQgqZsD"><img src="https://i.ibb.co/ckcMH6V/ce5e1f7877e0155577d5511b95ea2100-660x44100000000000.png" alt="ce5e1f7877e0155577d5511b95ea2100-660x44100000000000" border="0"></a>

# 以用户名、密码登录方式为例讲解一下Spring Security的登录认证流程

<a href="https://ibb.co/z2zs8Cf"><img src="https://i.ibb.co/qgvB0h1/965b390cafd239b8f33c44496a99dd64-978x674.png" alt="965b390cafd239b8f33c44496a99dd64-978x674" border="0"></a>

# [Spring Security4 例子程序](http://websystique.com/spring-security-tutorial/)


---


# 视频

* []()

# 博客

[Spring Security Tutorial with example](https://www.javaguides.net/p/spring-security-tutorial.html)|[Spring Security-JWT-OAuth2一本通(基于SpringBoot2.0)-字母哥课堂](https://www.kancloud.cn/hanxt/springsecurity/1221477)|[Spring Security --字母哥课堂](https://www.bilibili.com/video/BV1zD4y19716/?spm_id_from=333.788)|
---|---|---|

[Security with Spring](https://www.baeldung.com/security-spring)|
---|


[Spring Security 和 SpringCloud Security 有什么区别](https://www.cxyzjd.com/article/hadues/109646510)|[Spring Cloud Gateway 整合 Spring Security 统一鉴权](https://segmentfault.com/a/1190000039390248)|
---|---|

[Spring Security 最佳实践，看了必懂！](https://mp.weixin.qq.com/s/rJqXpL7Zy9q_TU5B_E7nSw)|
---|

[spring security 原理+实战](https://www.cnblogs.com/crazymakercircle/p/12040402.html)|[SpingSecurity + SpringSession 整合](https://www.cnblogs.com/crazymakercircle/p/12037584.html)|
---|---|


[Spring Security初识-练气初期](https://juejin.im/post/6863236247880466439)|[Spring Security拦截器加载流程分析--练气中期](https://juejin.im/post/6863238199674667022)|
---|---|

[Spring Security自定义认证页面（动态网页解决方案+静态网页解决方案）--练气中期圆满](https://juejin.im/post/6863463297796112392)|[Spring Security认证流程分析--练气后期](https://juejin.im/post/6863693023314706446)|
---|---|

[Spring Security使用数据库数据完成认证--练气后期2](https://juejin.im/post/6865579379620053006)|
---|

[springBoot整合spring security实现权限管理(单体应用版)--筑基初期](https://juejin.im/post/6867530830446034958)|[springBoot整合spring security+JWT实现单点登录与权限管理前后端分离--筑基中期](https://juejin.im/post/6868558729353117710)|
---|---|


# 目录

* 身份验证
  * [HTTP 基本认证](https://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)---只有在部署了 SSL 证书的情况下才可以使用，否则用户密码会有暴露的风险，当然，最好不要使用
  * [JSON Web Token](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-json-web-token-25)---支持通过登录接口使用账号密码获取，在请求接口时使用 Authorization: Bearer #{token} 头标或者 token 参数的值的方式进行验证  
    * [Json Web Tokens: Introduction](http://angular-tips.com/blog/2014/05/json-web-tokens-introduction/)
    * [son Web Tokens: Examples](http://angular-tips.com/blog/2014/05/json-web-tokens-examples/)
    * Cookies vs Tokens. Getting auth right with Angular.JS 
  * OAuth 2.0
    * [官网 ](https://oauth.net/2/)
    * [理解OAuth 2.0 ](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

* [SpringSecurity用到的类](https://javaniuniu.com/SpringSecurity/class)
* Spring Security 核心组件
  * SecurityContext
    * [SecurityContextHolder](https://javaniuniu.com/SpringSecurity/SecurityContextHolder)
    * [SecurityContext使用实例](https://javaniuniu.com/SpringSecurity/SecurityContext)  
  * Userdetails组件
    * [user](https://javaniuniu.com/SpringSecurity/User) 
    * [Userdetails组件1](https://www.cnblogs.com/crazymakercircle/p/12040402.html) 
    * [Userdetails组件2](https://javaniuniu.com/SpringSecurity/UserDetails)
  * Authentication组件
    * [Authentication组件](https://www.cnblogs.com/crazymakercircle/p/12040402.html) 
    * [GrantedAuthority](https://javaniuniu.com/SpringSecurity/GrantedAuthority)
    * [AuthenticationException ](https://javaniuniu.com/SpringSecurity/authenticationException)
  * UserDetailsService组件
    * [UserDetailsService组件1](https://javaniuniu.com/SpringSecurity/UserDetailsService) 
    * [UserDetailsService组2](https://www.cnblogs.com/crazymakercircle/p/12040402.html)
  * WebSecurityConfigurerAdapter
    * [WebSecurityConfigurerAdapter](https://javaniuniu.com/SpringSecurity/WebSecurityConfigurerAdapter) 
  * [AuthenticationProvider组件 ](https://www.cnblogs.com/crazymakercircle/p/12040402.html)
    * DaoAuthenticationProvider---数据库加载用户信息
    * RememberMeAuthenticationProvider---定义了“记住我”功能的登录验证逻辑
    * DaoAuthenticationProvider---加载数据库用户信息，进行用户密码的登录验证  



# 例子程序

* [Spring Security4 例子程序](http://websystique.com/spring-security-tutorial/)
* [[Spring Security官方例子程序](https://github.com/spring-projects/spring-security)

# 实战案例

## [案例1：Spring Security 案例实现和执行流程剖析](https://www.cnblogs.com/xifengxiaoma/p/10020960.html)

## 案例2： Spring Cloud Gateway + Spring Security OAuth2 + JWT实现微服务统一认证授权鉴权

线上Demo地址：www.youlai.tech

Gitee: https://gitee.com/haoxr

项目文档：https://www.cnblogs.com/haoxianrui/

* 1 后台微服务
  * [Spring Cloud实战 | 第一篇：Windows搭建Nacos服务](https://www.cnblogs.com/haoxianrui/p/13581881.html)
  * [Spring Cloud实战 | 第二篇：Spring Cloud整合Nacos实现注册中心前言](https://www.cnblogs.com/haoxianrui/p/13584204.html)
  * [Spring Cloud实战 | 第三篇：Spring Cloud整合Nacos实现配置中心](https://www.cnblogs.com/haoxianrui/p/13585125.html)
  * [Spring Cloud实战 | 第四篇：Spring Cloud整合Gateway实现API网关](https://www.cnblogs.com/haoxianrui/p/13608650.html)
  * [Spring Cloud实战 | 第五篇：Spring Cloud整合OpenFeign实现微服务之间的调用](https://www.cnblogs.com/haoxianrui/p/13615592.html)
  * [Spring Cloud实战 | 第六篇：Spring Cloud Gateway + Spring Security OAuth2 + JWT实现微服务统一认证授权鉴权](https://www.cnblogs.com/haoxianrui/p/13719356.html)
  * [Spring Cloud实战 | 最七篇：Spring Cloud Gateway+Spring Security OAuth2集成统一认证授权平台下实现注销使JWT失效方案](https://www.cnblogs.com/haoxianrui/p/13740264.html)
  * [Spring Cloud实战 | 最八篇：Spring Cloud +Spring Security OAuth2+ Vue前后端分离模式下无感知刷新实现JWT续期](https://www.cnblogs.com/haoxianrui/p/14022632.html)
  * [Spring Cloud实战 | 第九篇：Spring Cloud整合Spring Security OAuth2认证服务器统一认证自定义异常处理](https://www.cnblogs.com/haoxianrui/p/14028366.html)
  * [Spring Cloud实战 | 第十篇 ：Spring Cloud + Nacos整合Seata 1.4.1最新版本实现微服务架构中的分布式事务，进阶之路必须要迈过的槛](https://www.cnblogs.com/haoxianrui/p/14280184.html)
  * [Spring Cloud实战 | 第十一篇：Spring Cloud Gateway统一鉴权下针对RESTful接口的RBAC权限设计方案，附Vue按钮权限控制](https://www.cnblogs.com/haoxianrui/p/14396990.html)

* 2 后台管理前端
    
    有来商城youlai-mall管理前端 
    Github:https://github.com/hxrui/mall-admin-web

  * [vue-element-admin实战 | 第一篇： 移除mock接入后台微服务接口，搭建SpringCloud+Vue前后端分离的管理平台](https://www.cnblogs.com/haoxianrui/p/13624548.html)
  * [vue-element-admin实战 | 第二篇： 最小改动接入后台实现动态路由菜单加载](https://www.cnblogs.com/haoxianrui/p/13676619.html)

* 3 微信小程序
  * [uni-app实战 | 第一篇：从0到1快速开发一个开源商城微信小程序，无缝接入Spring Security OAuth2认证授权实现登录](https://www.cnblogs.com/haoxianrui/p/13882310.html)

* 4 应用部署
  * [Docker实战 | 第一篇：Linux 安装 Docker](https://www.cnblogs.com/haoxianrui/p/14067423.html)
  * [Docker实战 | 第二篇：Docker部署nacos-server:1.4.0](https://www.cnblogs.com/haoxianrui/p/14059009.html)
  * [Docker实战 | 第三篇：IDEA集成 Docker 插件实现一键自动打包部署微服务项目](https://www.cnblogs.com/haoxianrui/p/14088400.html)
  * [Docker实战 | 第四篇：Docker安装Nginx，实现基于vue-element-admin框架构建的项目线上部署](https://www.cnblogs.com/haoxianrui/p/14091762.html)
  * [Docker实战 | 第五篇：Docker启用TLS加密解决暴露2375端口引发的安全漏洞，被黑掉三台云主机的教训总结](https://www.cnblogs.com/haoxianrui/p/14095306.html)

---

### 微服务安全
  * [spring security 原理+实战](https://www.cnblogs.com/crazymakercircle/p/12040402.html)
  * [SpingSecurity + SpringSession 死磕](https://www.cnblogs.com/crazymakercircle/p/12037584.html)

---

### Mybird
* [基于数据库用户认证](https://mrbird.cc/%E5%9F%BA%E4%BA%8E%E6%95%B0%E6%8D%AE%E5%BA%93%E7%94%A8%E6%88%B7%E8%AE%A4%E8%AF%81.html)
* [自定义Spring Security登录页](https://mrbird.cc/%E8%87%AA%E5%AE%9A%E4%B9%89Spring-Security%E7%99%BB%E5%BD%95%E9%A1%B5.html)
* [Spring Security JSP标签库](https://mrbird.cc/Spring-Security-JSP%E6%A0%87%E7%AD%BE%E5%BA%93.html)
* [Spring Security保护方法](https://mrbird.cc/Spring-Security%E4%BF%9D%E6%8A%A4%E6%96%B9%E6%B3%95.html)
* [Spring Security OAuth2 SSO](https://mrbird.cc/Spring-Security-OAuth2-SSO.html)
* [Spring Security OAuth2自定义令牌配置](https://mrbird.cc/Spring-Security-OAuth2-Token-Config.html)
* [Spring Security OAuth2自定义Token获取方式](https://mrbird.cc/Spring-Security-OAuth2-Customize.html)
* [Spring Security OAuth2入门](https://mrbird.cc/Spring-Security-OAuth2-Guide.html)
* [Spring Security权限控制](https://mrbird.cc/Spring-Security-Permission.html)
* [Spring Security退出登录](https://mrbird.cc/Spring-Security-logout.html)
* [Spring Security Session管理](https://mrbird.cc/Spring-Security-Session-Manage.html)
* [Spring Security短信验证码登录](https://mrbird.cc/Spring-Security-SmsCode.html)
* [Spring Security添加记住我功能](https://mrbird.cc/Spring-Security-RememberMe.html)
* [Spring Security添加图形验证码](https://mrbird.cc/Spring-Security-ValidateCode.html)
* [Spring Security自定义用户认证](https://mrbird.cc/Spring-Security-Authentication.html)
* [Spring Boot中开启Spring Security](https://mrbird.cc/Spring-Boot&Spring-Security.html)
* [Spring Security保护方法](https://mrbird.cc/Spring-Security%E4%BF%9D%E6%8A%A4%E6%96%B9%E6%B3%95.html)
* [Spring Security JSP标签库](https://mrbird.cc/Spring-Security-JSP%E6%A0%87%E7%AD%BE%E5%BA%93.html)
* []()

---

# 有用的参考
* []()
* [Spring Security 控制Session详解](https://www.jianshu.com/p/057fcf061b94)
* [springboot security 自定义 AuthenticationEntryPoint 和 AccessDeineHandler](https://blog.csdn.net/jkjkjkll/article/details/79975975#:~:text=AuthenticationEntryPoint%E7%AE%80%E4%BB%8BAuthenticationEntryPoint%E6%98%AFSpring,%E4%BA%8E%E5%BC%80%E5%90%AF%E7%89%B9%E5%AE%9A%E8%AE%A4%E8%AF%81%E6%96%B9%E6%A1%88()
* [基于token身份认证的完整实例](https://www.jianshu.com/p/75f6d7426118)
* [手把手教你搞定权限管理，结合Spring Security实现接口的动态权限控制](https://www.jianshu.com/p/a8e66020fad1)
* [1. mall整合SpringSecurity和JWT实现认证和授权（一）](https://mp.weixin.qq.com/s/HFAfcSGANrdVJeTmT-7X_A)
* [2. mall整合SpringSecurity和JWT实现认证和授权（二）](https://mp.weixin.qq.com/s/yO_8nAN-zoCB86ep4nuAOg)
* [3. 仅需四步，整合SpringSecurity+JWT实现登录认证！](http://www.macrozheng.com/#/technology/springsecurity_use)
* [4. 手把手教你搞定权限管理，结合Spring Security实现接口的动态权限控制！---实现了对后端接口的动态权限控制](http://www.macrozheng.com/#/technology/permission_back)
* [5. 手把手教你搞定权限管理，结合Vue实现菜单的动态权限控制！---在前端项目如何结合Vue来实现菜单的动态权限控制](http://www.macrozheng.com/#/technology/permission_front)
