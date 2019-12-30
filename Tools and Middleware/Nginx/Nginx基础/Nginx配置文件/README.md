
# Nginx配置文件的路径
     /usr/local/nginx/conf/nginx.conf

     main（全局设置）：设置的指令将影响其他所有设置；
     server（主机设置）：指令主要用于指定主机和端口、
     upstream（负载均衡服务器设置）：指令主要用于负载均衡，设置一系列的后端服务器
     location（URL匹配特定位置的设置）：用于匹配网页位置。


 
# 目录 
 
 >[MAIN全局模块](#MAIN全局模块)
 >>[EVENTS模块](#EVENTS模块)
 >>[HTTP模块](#HTTP模块)
 >>>[HTTP全局模块](#HTTP全局模块)
 >>>[Upsteam](#Upsteam)
 >>>[Server](#Server)
 >>>>[Server全局全局模块](#Server全局全局模块)
 >>>>[location](#location)

 
## MAIN全局模块

该部分配置主要影响Nginx全局，通常包括下面几个部分



## EVENTS模块

events事件指令是设定Nginx的工作模式及连接数上限


## HTTP模块


## HTTP全局模块


## Upsteam



## Server



## Server全局全局模块



## location



# 有用的参考

* [Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)



