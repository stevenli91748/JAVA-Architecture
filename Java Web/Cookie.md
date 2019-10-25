# JAVA EE 中的cookie

   在JavaEE中的javax.servlet.http包中存在一个类Cookie,就是用于完成会话跟踪的Cookie,而HTTPServletResponse中有一个方法，可以将Cookie
   添加到响应头中，一个Response中可以添加多个Cookie的键值对。
 
 ```java  
 
   Public void addCookie(Cookie cookie)
 
 ```  

* Cookie机制
* Cookie创建与使用
