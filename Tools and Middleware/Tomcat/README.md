
单纯比较 Tomcat 与 Jetty 的性能意义不是很大，只能说在某种使用场景下，它表现的各有差异。因为它们面向的使用场景不尽相同。从架构上来看 Tomcat 在处理少数非常繁忙的连接上更有优势，也就是说连接的生命周期如果短的话，Tomcat 的总体性能更高。

而 Jetty 刚好相反，Jetty 可以同时处理大量连接而且可以长时间保持这些连接。例如像一些 web 聊天应用非常适合用 Jetty 做服务器，像Taobao的 web 旺旺就是用 Jetty 作为 Servlet 引擎。


# 已安装Tomcat
安装目录
c:\apache-tomcat-9.0.16

* [Tomcat热部署的实现原理](#Tomcat热部署的实现原理)

# Tomcat配置和使用
  
     逐步部署一个WAP项目例子，最后达到在浏览器输入网址就可訪问，
     
     1. 在tomcat_home/webapps/目录下建立一个目录 /myemall和在/myemall下创建一个index.html文件
         webapps/myemall/index.html
         
        启动tomcat，在brower中输入訪问地址： localhost:8080/myemall/index.html 就可訪问
        
     2. 但是如果在myemall目录下有多个文件，不可能一一输入地址訪问，必须有一个默认的WELCOME訪问地址，
     
        在/myemall下建立/WEB-INF, 在/WEB-INF目录下创建一个web.xml文件，index.html文件就成了默认的welcome页面，可以指定任一在/myemall目录
        下的文件为默认的welcome页面
        
        <?xml version="1.0" encoding="UTF-8"?>
        <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                                    http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
  
           <welcome-file-list>
               <welcome-file>index.html</welcome-file>
           </welcome-file-list>
    
        </web-app>

        在brower中输入訪问地址： localhost:8080/myemall/ 就可訪问默认的WELCOME訪问地址
    
      3. 在/WEB-INF下再建一个目录/classes放置 由servelet 编译成的xxx.class文件。
          
          /webapps/myemall/WEB-INF/classes/xxx.class
                                  /web.xml
          如果在xxx.class文件中要用到的其它的jar包，就必须在/WEB-INF下建立/lib目录,把jar包放到该目录下
          
          /webapps/myemall/WEB-INF/classes/xxx.class
                                  /lib
                                  /web.xml
      4. 当项目已经开发好了，要部署到工作服务器的tomcat/webapps目录下，但是一般项目都会很大，需要把项目打成WAR包压缩，
         需要进入到该项目的目录种，输入打包命令 jar cvf /目的目录/myemall.war . , 最后一个点表示要将当前目录中所有内容进行打包
         
         cd myemall
         
         jar cvf d:\目的目录/myemall.war .
         
         就可把myemall.war部署到工作服务器的tomcat/webapps目录下, 当tomcat启动后， 该myemall.war会自动解压，然后就可訪问了
         
         在brower中输入訪问地址：  localhost:8080/myemall
      
      5.如果不想把项目放到/webapps目录中，想放到任意目录中，有两种实现方法
      
         5.1  第一种方法 在/conf/server.xml文件中配置，然后重启tomcat，但是这种方法不可取，因为同时有多个程序在同一tomcat服务器上。
         
              
              
              
         5.2  第二种方法

         
      
# Tomcat的核心

     /tomcat安装目录/conf/server.xml
   
     1. 为什麽开发的项目war文件要方在/wabapps目录中?
     
         因为在server.xml中的 host标签中的appBase设置了webapps
         
        <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">

     2. 当部署项目时，启动tomcat后，是否在wabapps目录下自动解压war包？
        
        unpackWARs="true"是设置是否自动解压war包
        
        <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
        
      3. 当部署项目时，在tomcat服务器在运行当中，当你把war包一放到webapps目录时，tomcat服务器自动解压和发布到服务器上
      
         autoDeploy="true"
         
         <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
      
# Tomcat热部署的实现原理

  [Tomcat热部署的实现原理](https://my.oschina.net/xianggao/blog/364068)
  
  [如何让Tomcat热部署](https://blog.csdn.net/qq_40803316/article/details/83829868)
  
  [Tomcat热部署的三种方式](https://my.oschina.net/u/1455528/blog/801631)
  
  [菜鸟教程之工具使用——借助JRebel使Tomcat支持热部署](https://blog.csdn.net/liushuijinger/article/details/39898415)
  
  
# [TOMCAT性能调优](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Performance/tomcat.md)


# 有用的参考

* [Tomcat结构及处理请求过程](http://objcoding.com/2017/06/12/Tomcat-structure-and-processing-request-process/)
* [Tomcat集群搭建超详细](https://blog.csdn.net/qq_35661171/article/details/78359681#comments)
* [Linux Tomcat日志查看实用命令](https://blog.csdn.net/qq_40803316/article/details/83582726)
