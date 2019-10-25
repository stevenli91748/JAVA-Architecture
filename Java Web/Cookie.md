# JAVA EE 中的cookie

   在JavaEE中的javax.servlet.http包中存在一个类Cookie,就是用于完成会话跟踪的Cookie,而HTTPServletResponse中有一个方法，可以将Cookie
   添加到响应头中，一个Response中可以添加多个Cookie的键值对。
 
 ```java  
    
  // CookieTest.project
  // 在服务器端的 responseServlet.java  Servlet文件, 生成Cookie 并放入响应头中
    
  // 创建Cookie
  Cookie cookie = new Cookie("company"."google");
  Cookie cookie2 = new Cookie("CEO"."steven");
   
  //设置Cookie的有效期，单位为秒
  //该值大于0，表示将Cookie存放到客户端的硬盘上
  //该值小于0，表示将Cookie存放到浏览器的缓存上
  //该值等于0，表示Cookie一生成，马上失效
  
  cookie.setMaxAge(60 * 60)  // 设置Cookie的有效期为1小时
  cookie2.setMaxAge(60 * 60 * 24 * 10) //设置Cookie的有效期为10天
  
  //  指定訪问路径的方法1： 在程序中动态指定
  //指定Cookie 绊定的路径，注意，路径要求必须要添加上项目名称
  // 在浏览器上输入：   localhost:8080/CookieTest/xxx/ooo/aaa,在浏览器发出的http中就只是包栝 cookie("company", "google")
  //  如果 在浏览器上输入：   localhost:8080/CookieTest/bbb, 在浏览器发出的http中就只是包栝 cookie2("CEO", "steven")
  
  // 指定訪问路径的方法2： 在web.xml中静态指定
   // 可以修改web.xml文件,如果要訪问 responseServlet，就需要输入/xxx/ooo/jjj/some路径
   //   <servlet-mapping>
   //        <servlet-name>responseServlet</servlet-name>
   //        <url-pattern>/xxx/ooo/jjj/some</url-pattern>
   //   </servlet-mapping>
   //
  // 在浏览器上输入：   localhost:8080/CookieTest/xxx/ooo/jjj/some
  
  cookie.setPath(request.getContextPath() + "/xxx/ooo/aaa");
  cookie2.setPath(request.getContextPath() + "/bbb");     
   
   // 向响应中添加Cookie
   
   response.addCookie(cookie);
   response.addCookie(cookie2);

 ```  

```java

    // 在服务端获取并解析Cookie,创建解析Cookie的servlet文件
    //该解析Cookie的servlet文件的訪问路径要和生成Cookie的文件路径一致，只能用指定訪问路径的方法2： 在web.xml中静态指定
    //executeServlet.java
  
    // 获取请求中的Cookie
    
    Cookie[] cookie= request.getCookies();
    for (Cookie cookie: cookie){
    
      system.out.println(cookie.getName() + “===” + cookie.getValue());
    
    
    }


```




   把项目部署到Tomcat, 想对HTTP 的cookie进行调试
      
   
   在开发中要查看HTTP Response，使用工具 HTTP WATCH
   先安装HTTP WATCH，然后启动tomcat服务器，在网页上右击，选HTTP WATCH,然后显示出http watch， 按 record 按钮 启动 HTTP WATCH
   
   在浏览器上输入：   localhost:8080/AAAweb/xxx/ooo/jjj/some
   
  在http watch 看到一条记录，选择它，然后选择 stream Tab ,可看到Cookie
   
   
   
   
   # HTTP WATCH 视频
   
   [ HTTP WATCH  ](https://www.37qiqi.com/video/c64db0605c83c5e7.html)
 
   







* Cookie机制
* Cookie创建与使用
