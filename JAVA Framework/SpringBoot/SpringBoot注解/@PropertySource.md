
@PropertySource不支持yml文件读取。

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

