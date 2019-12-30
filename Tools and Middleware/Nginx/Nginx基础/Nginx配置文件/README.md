
# Nginx配置文件的路径
  /usr/local/nginx/conf/nginx.conf

 >MAIN 全局模块              // 全局设置: 设置的指令将影响其他所有设置
 >>[EVENTS 模块](https://www.jianshu.com/p/1593954d5faf)
 >>HTTP 模块
 >>>HTTP全局模块
 >>>Upsteam                //（负载均衡服务器设置）：指令主要用于负载均衡，设置一系列的后端服务器
 >>>Server1                //server1（主机设置）：指令主要用于指定主机和端口、
 >>>>Server1全局全局模块
 >>>location               //（URL匹配特定位置的设置）：用于匹配网页位置。
 >>>Server2
 >>>>Server2全局全局模块
 >>>location
      





# 有用的参考

* [Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)



