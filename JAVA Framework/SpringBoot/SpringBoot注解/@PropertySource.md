
@PropertySource不支持yml文件读取。

[@PropertySource+@ConfigurationProperties注解读取方式](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484575&idx=1&sn=56c88cd7283374345d891e85a800539b&scene=21#wechat_redirect)

资源目录下建立config/db-config.properties:

db.username=root

db.password=123456

@PropertySource+@Value注解读取方式

```java
@Component
@PropertySource
(value = {"config/db-config.properties"})
public class DBConfig1{
  
@Value("${db.username}")
private String username;
   
@Value("${db.password}")
private String password;
  
public String getUsername() 
{
return username;
   
}


    
public void setUsername(String username)
{      
this.username = username;
}



}

```

