


# @ResponseBody

@responseBody注解的作用是将controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到response对象的body区，通常用来返回JSON数据或者是
XML数据，需要注意的呢，在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。

```java

  @RequestMapping("/login")
  @ResponseBody
  public User login(User user){
    return user;
  }
  //User字段：userName pwd
  //那么在前台接收到的数据为：'{"userName":"xxx","pwd":"xxx"}'

  //效果等同于如下代码：
  @RequestMapping("/login")
  public void login(User user, HttpServletResponse response){
    response.getWriter.write(JSONObject.fromObject(user).toString());
  }
  
  ```
