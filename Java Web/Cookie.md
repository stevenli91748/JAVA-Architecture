# JAVA EE 中的cookie

   在JavaEE中的javax.servlet.http包中存在一个类Cookie,就是用于完成会话跟踪的Cookie,而HTTPServletResponse中有一个方法，可以将Cookie
   添加到响应头中，一个Response中可以添加多个Cookie的键值对。
 
 ```java  
    
    // AAAweb.project
    // ABCServlet.java  Servlet文件
    
   // 创建Cookie
   Cookie cookie = new Cookie("company"."google");
   Cookie cookie2 = new Cookie("CEO"."steven");
   
   // 向响应中添加Cookie
   
   response.addCookie(cookie);
   response.addCookie(cookie2);

 ```  
   把项目部署到Tomcat,可以修改web.xml文件,如果要訪问 ABCServlet，就需要输入/xxx/ooo/jjj/some路径
   
   <servlet-mapping>
      <servlet-name>ABCServlet</servlet-name>
      <url-pattern>/xxx/ooo/jjj/some</url-pattern>
   </servlet-mapping>
   
   在开发中要查看HTTP Response，使用工具 HTTP WATCH
   先安装HTTP WATCH，然后启动tomcat服务器，在网页上右击，选HTTP WATCH,然后显示出http watch， 按 record 按钮 启动 HTTP WATCH
   
   在浏览器上输入：   localhost:8080/AAAweb/xxx/ooo/jjj/some
   
  在http watch 看到一条记录，选择它，然后选择 stream Tab ,可看到Cookie and Cookie2
   
   
 
   







* Cookie机制
* Cookie创建与使用
