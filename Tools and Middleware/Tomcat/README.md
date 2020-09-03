

tomcat的优势在于少量的接收并处理复杂的http请求（将用户请求读写数据库等），nginx的优势在于能够大量的接收并处理简单的http请求（将http请求转发或者加个header、body等）,将Html、img、js、css等这种静态资源交给nginx，将用户需要读写数据库等请求交给tomcat是对各自优势的最大利用。

单纯比较 Tomcat 与 Jetty 的性能意义不是很大，只能说在某种使用场景下，它表现的各有差异。因为它们面向的使用场景不尽相同。从架构上来看 Tomcat 在处理少数非常繁忙的连接上更有优势，也就是说连接的生命周期如果短的话，Tomcat 的总体性能更高。

而 Jetty 刚好相反，Jetty 可以同时处理大量连接而且可以长时间保持这些连接。例如像一些 web 聊天应用非常适合用 Jetty 做服务器，像Taobao的 web 旺旺就是用 Jetty 作为 Servlet 引擎。


# 在/etc/hosts文件中添加域名解析
  
  192.168.28.130 www.mywebapp-name.com
  
  本机访问

  [root@www html]# curl www.mywebapp-name.com
  就可用域名訪问了



# [Tomcat面试题]()

# 已安装Tomcat
windows安装目录
c:\apache-tomcat-9.0.16

[Tomcat Linux系统安装](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Tomcat/Tomcat%20Linux系统安装/README.md)|[在Docker安装Tomcat](https://github.com/stevenli91748/Engineering-special/blob/master/Docker/在Docker安装Tomcat/README.md)|
---|---|



* Tomcat基础知识
  * [Tomcat源码部署](#Tomcat源码部署)
  * [Tomcat的核心](#Tomcat的核心)
  * [Tomcat的组件](#Tomcat的组件)
  * [Tomcat整体架构](#Tomcat整体架构)
    * [Tomcat启动流程](#Tomcat启动流程)
    * [Tomcat请求处理](#Tomcat请求处理)
    * [Coyote连接器架构](#Coyote连接器架构)
    * [Catalina容器架构](#Catalina容器架构)
    * [HTTP工作流程](#HTTP工作流程)
  * [Jasper引擎](#Jasper引擎)
  * [Tomcat结构以及其类加载器流程](https://www.bilibili.com/video/av56917568?from=search&seid=9890313462804608132)
* Tomcat配置和使用  
  * [Tomcat配置和使用](#Tomcat配置和使用)
    * [Tomcat服务器配置---Executor](#Executor)
    * [Tomcat服务器配置---Connector](#Connector)
    * [Tomcat服务器配置---Engine Host](#Engine)
    * [Tomcat服务器配置---context](#Context)
  * [Tomcat Web应用配置](#Tomcat-Web应用配置)
  * Tomcat 管理配置
    * [Host Manager](#Host-Manager)
    * [Manage APP](#Manage-APP)
  * [Tomcat JVM配置](#Tomcat-JVM配置)  
  * [Web容器(Tomcat)自动对HTTP请求中参数进行URLDecode处理](https://blog.csdn.net/u013833031/article/details/78828539)
  * [Tomcat负载均衡](#Tomcat负载均衡)
  * [Tomcat反向代理机制](#Tomcat反向代理机制)
  * [Tomcat热部署的实现原理](#Tomcat热部署的实现原理)
  * [如何部署Tomcat](#如何部署Tomcat)
     * [Tomcat单机部署多应用](https://www.bilibili.com/video/av68236883/?p=14)
     * [Tomcat多机部署多应用](https://www.bilibili.com/video/av68236883/?p=16)
  * [Tomcat集群](#Tomcat集群)
    * [负载均衡](https://www.bilibili.com/video/av67233983/?p=35)
    * [Session共享](https://www.bilibili.com/video/av67233983/?p=36)
    * [Session复制](https://www.bilibili.com/video/av67233983/?p=37)
    * [SSO解决Session共享](https://www.bilibili.com/video/av67233983/?p=38)
    * [集群架构](https://www.bilibili.com/video/av68236883/?p=13)
    * [Nginx + Tomcat搭建集群](https://www.bilibili.com/video/av68236883/?p=18)
    * [Tomcat和Nginx配置回顾](https://www.bilibili.com/video/av68236883/?p=19)
    * [Tomcat集群实战](https://www.bilibili.com/video/av68236883/?p=20)
* Tomcat安全
  * [配置安全](https://www.bilibili.com/video/av67233983/?p=39)
  * [传输安全](https://www.bilibili.com/video/av67233983/?p=40)
  * [传输安全---HTTP配置](https://www.bilibili.com/video/av67233983/?p=41)
* [Tomcat的Websocket](#Tomcat的Websocket)
* TOMCAT性能调优
  * [TOMCAT性能调优](#TOMCAT性能调优)
  * [链接器配置调优](https://www.bilibili.com/video/av67233983/?p=47)
  * [JVM内存参数调优](https://www.bilibili.com/video/av67233983/?p=44)
  * [JVM垃圾收集器调优](https://www.bilibili.com/video/av67233983/?p=46)
  * [Tomcat监控](#Tomcat监控)

---

# Tomcat源码部署

  [Tomcat源码部署](https://www.bilibili.com/video/av68043130/?p=5)
  
# Tomcat整体架构

  [Tomcat整体架构](https://www.bilibili.com/video/av68043130/?p=7)

# Coyote连接器架构

  [Coyote连接器架构](https://www.bilibili.com/video/av68043130/?p=8)

# Catalina容器架构

  [Catalina容器架构](https://www.bilibili.com/video/av68043130/?p=10)

# Tomcat启动流程
  
  [Tomcat启动流程](https://www.bilibili.com/video/av68043130/?p=14)
  
# Tomcat请求处理

  [Tomcat请求处理](https://www.bilibili.com/video/av68043130/?p=15)

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

# Tomcat的组件
  
  [Tomcat组件详解](https://www.bilibili.com/video/av58099303/?p=6)
  [Tomcat组件的关连](https://www.bilibili.com/video/av58099303/?p=13)

# HTTP工作流程

  [HTTP工作流程](https://www.bilibili.com/video/av68043130/?p=6)

# Tomcat配置和使用
    视频
  [Javaweb开发视频教程之Tomcat9配置和使用](https://www.bilibili.com/video/av14548279?from=search&seid=17837694532059865818)
  
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
      
      5.虚拟目录     
      
         如果不想把项目放到/webapps目录中，想放到任意目录中，有两种实现方法
      
         5.1  第一种方法 在/conf/server.xml文件中配置，然后重启tomcat，但是这种方法不可取，因为同时有多个程序在同一tomcat服务器上。
         
              找到你想加载的主机标签 <host>，然后输入：<Context path="/xxx" docBase="c:\myemall"/>
         
              <Host name="localhost"  appBase="webapps"
                     unpackWARs="true" autoDeploy="true">

                    <Context path="/xxx" docBase="c:\myemall"/>
          
              </Host>
          
          指明项目目录是在c:\myemal上，
          
          该应用是在localhost主机下的
          
          启动tomcat后， 在brower中输入訪问地址：  localhost:8080/xxx, 就可以訪问了
              
              
         5.2  第二种方法
         
             不需要重启tomcat服务器
             
             找tomcat_home/conf/Catalina/localhost, catalina 是设置在server.xml文件上的引擎，是可删除的目录，当tomcat重启后自动生成的
             
             创建  aaa.xml文件在localhost目录下，输入如下内容
             
             <?xml version="1.0" ?>
             <Context docBase="c:/myemall"/>
             
             然后，在brower中输入訪问地址：  localhost:8080/aaa , 就可以訪问了

       6. 虚拟主机
       
          创建DNS www.myemall.com ，达到可用www.myemall.com:8080/aaa訪问的目的
          
          6.1 修改server.xml
          
             增加一个主机标志，修改 host name and appBase的值， appBase指定的目录是相对tomcat_home主目录，如果没有该目录，就创建一个项目目录
             也可以设置一个绝对路径：   appBase="c:/myemall"
          
             <Host name="www.myemall.com"  appBase="myemallapps"
                  unpackWARs="true" autoDeploy="true">

             </Host>
          
         6.2  修改本地 DNS 服务器做IP
          
              Windows 版本
          
               copy c:\windows\system32\drivers\etc\host文件 to 任意目录下修改
          
               增加 127.0.0.1   www.myemall.com  到 host文件
          
               然后再把该文件覆盖 to c:\windows\system32\drivers\etc
          
          6.3  copy 已经开发好的myemall项目目录 到 tomcat-home/myemallapps目录下，
                 tomcat_home\myemallapps\myemall
                             \webapps
                             \bin
                             \conf
                             \lib
                  
                  
             然后，在brower中输入訪问地址：  www.myemall.com:8080/myemall , 就可以訪问了            
                                        
          
          7. 在tomcat中有多个主机，而多个主机都影射同一个IP地址 127.0.0.1
             拿如果在brower中输入IP地址 127.0.0.1:8080/myemall， 将会訪问那个项目
             
             host文件
             
             127.0.0.1   localhost
             127.0.0.1   www.myemall.com
             
             TOMCAT将会訪问localhost的项目，因为在 /conf/server.xml文件中设定好了
             
             server.xml文件
             
               <Engine name="Catalina" defaultHost="localhost">
             
            8. 修改端口
            
               不想每次都输入  localhost:8080/myemall
                           or 127.0.0.1:8080/myemall
                           
               想省去8080的输入
               
               server.xml
               
               找到  
              
               <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
             
               改为
               <Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

               因为浏览器如果没有输入端口号，默任就是80端口
               
               在brower中输入訪问地址：  www.myemall.com/myemall , 就可以訪问了            


             9  指定虚拟主机的默认应用
             
                到现在为止，从浏览器訪问，输入地址象这样   www.myemall.com/myemall ， 而输入localhost就会出现tomcat页面
                能不能就输入www.myemall.com就能訪问到对应的项目？
                
                答案就是： 每一个虚拟主机都有一个默认的应用要加载，这个默认的应用是放在tomcat_home/webapps/ROOT目录下，当tomcat安装时
                          就已经把localhost虚拟主机对应的应用放在ROOT目录下了，所以输入localhost能訪问。
                          
                          如果想要tomcat接受我们项目的域名，就要在server.xml文件中查找对应的主机标签，
                          
                          <Host name="www.myemall.com"  appBase="C:/APACHE-TOMCAT-9.0.16/myemallapps"
                           unpackWARs="true" autoDeploy="true">
                           
                           项目是訪在C:/APACHE-TOMCAT-9.0.16/myemallapps目录下
                           
                           C:/APACHE-TOMCAT-9.0.16/myemallapps/myemall/*.*
                           
                           所以我们必须把项目目录名 /myemall 改成 /ROOT
                           
                  在brower中输入訪问地址：  www.myemall.com/ , 就能訪问对应默认项目          
                          
# Executor

  [服务器配置---Executor](https://www.bilibili.com/video/av68043130/?p=21)
  
# Connector

  [服务器配置---Connector](https://www.bilibili.com/video/av68043130/?p=22)


# Engine

  [服务器配置---Engine Host](https://www.bilibili.com/video/av68043130/?p=23)
  
# Context

  [服务器配置---Context](https://www.bilibili.com/video/av68043130/?p=24)


# Host Manager
  
  [Host Manager](https://www.bilibili.com/video/av71544536/?p=31)

# Manage APP

  [Manage APP](https://www.bilibili.com/video/av67233983/?p=32)

# Tomcat Web应用配置

   [Tomcat Web应用配置介绍](https://www.bilibili.com/video/av71544536/?p=26)
   []()
   
# Tomcat负载均衡

  [Tomcat负载均衡](https://www.bilibili.com/video/av58099303/?p=8)
  
  [Nginx负载均衡](https://www.bilibili.com/video/av68236883/?p=17)

# Tomcat集群

  [Tomcat负载均衡集群](https://www.bilibili.com/video/av58099303/?p=18)
  
  [Tomcat集群配置](https://www.bilibili.com/video/av67233983/?p=34)

# Jasper引擎

  [Jasper引擎](https://www.bilibili.com/video/av68043130/?p=17)


# Tomcat反向代理机制

  [Tomcat反向代理机制](https://www.bilibili.com/video/av58099303/?p=15)

# Tomcat热部署的实现原理

  [实例分析Tomcat热加载技术](https://www.bilibili.com/video/av24500273?from=search&seid=5658735684576843185)
  
  [Tomcat热部署的实现原理](https://my.oschina.net/xianggao/blog/364068)
  
  [如何让Tomcat热部署](https://blog.csdn.net/qq_40803316/article/details/83829868)
  
  [Tomcat热部署的三种方式](https://my.oschina.net/u/1455528/blog/801631)
  
  [菜鸟教程之工具使用——借助JRebel使Tomcat支持热部署](https://blog.csdn.net/liushuijinger/article/details/39898415)

# 如何部署Tomcat

  [教你如何部署Tomcat](https://www.bilibili.com/video/av55880985?from=search&seid=87210491669533119)

# Tomcat JVM配置

  [Tomcat JVM配置](https://www.bilibili.com/video/av67233983/?p=33)

# TOMCAT性能调优  

  [Tomcat内存调优](https://www.bilibili.com/video/av58099303/?p=24)

  [TOMCAT性能调优](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Performance/tomcat.md)

# Tomcat监控

  [Tomcat监控](https://www.bilibili.com/video/av58099303/?p=25)

# Tomcat的Websocket

  [Tomcat的Websocket](https://www.bilibili.com/video/av67233983/?p=47)

# Tomcat 视频
 * [Tomcat服务配置与性能优化](https://www.imooc.com/learn/1114)
 * [Tomcat从入门到精通](https://www.bilibili.com/video/av58099303?from=search&seid=17837694532059865818)
 * [java进阶教程Tomcat核心原理解析](https://www.bilibili.com/video/av67233983?from=search&seid=12930421917432531946)
 * [Tomcat知识点全集，你学会几个？](https://www.bilibili.com/video/av69510408/?p=5)
 * [多个tomcat课程导学(上)](https://www.bilibili.com/video/av68236883/?p=1)
 * [多个tomcat课程导学(下)](https://www.bilibili.com/video/av68237544/?spm_id_from=333.788.videocard.0)
 * [程序员们一般怎样实现Tomcat--如何用JAVA捕抓到浏览器的请求 ](https://www.bilibili.com/video/av57114754?p=2)
 
 
# 有用的参考
* [Tomcat系统架构与请求处理流程](https://blog.csdn.net/ThinkWon/article/details/102676442?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
* [Tomcat面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104397665)
* [Tomcat结构及处理请求过程](http://objcoding.com/2017/06/12/Tomcat-structure-and-processing-request-process/)
* [Tomcat集群搭建超详细](https://blog.csdn.net/qq_35661171/article/details/78359681#comments)
* [Linux Tomcat日志查看实用命令](https://blog.csdn.net/qq_40803316/article/details/83582726)
* [Linux下tomcat自起服务](https://blog.csdn.net/swiftly_654123/article/details/83903027)
* [linux下配置，启动多个tomcat](https://blog.csdn.net/guorui303/article/details/1505442)
* [服务器部署多个tomcat时需要修改的配置](https://blog.csdn.net/Jatham/article/details/80047119)
* [tomcat7插件将maven_web项目热部署到远程服务器](https://www.bilibili.com/video/av82792639?from=search&seid=13371764215596198302)
