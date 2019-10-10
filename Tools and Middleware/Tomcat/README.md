
单纯比较 Tomcat 与 Jetty 的性能意义不是很大，只能说在某种使用场景下，它表现的各有差异。因为它们面向的使用场景不尽相同。从架构上来看 Tomcat 在处理少数非常繁忙的连接上更有优势，也就是说连接的生命周期如果短的话，Tomcat 的总体性能更高。

而 Jetty 刚好相反，Jetty 可以同时处理大量连接而且可以长时间保持这些连接。例如像一些 web 聊天应用非常适合用 Jetty 做服务器，像Taobao的 web 旺旺就是用 Jetty 作为 Servlet 引擎。


# 已安装Tomcat
安装目录
c:\apache-tomcat-9.0.16

# [TOMCAT性能调优](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Performance/tomcat.md)


# 有用的参考

* [Tomcat结构及处理请求过程](http://objcoding.com/2017/06/12/Tomcat-structure-and-processing-request-process/)
* [Tomcat集群搭建超详细](https://blog.csdn.net/qq_35661171/article/details/78359681#comments)
