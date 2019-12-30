
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
   
* a、配置运行Nginx服务器用户（组）
         
      如：user nobody nobody;   user是个主模块指令，指定Nginx Worker进程运行以及用户组。
      指令格式：user user [group];
      user：指定可以运行Nginx服务器的用户；group：可选项，可以运行Nginx服务器的用户组。
      如果user指令不配置或者配置为user nobody nobody，默认由nobody账户运行。

* b、worker process数    
      
      如：worker_processes 2;    woker_processes是个主模块指令，制定了Nginx要开启的进程数。每个Nginx进程平均耗费10M~12M内存。建议指定和CPU
      的数量一致即可。Nginx服务器实现并发处理服务的关键。
      
      指令格式：worker_processes number | auto;
      number : Nginx 进程最多可以产生的worker process 数。
      auto ： Nginx 进程将自动检测      
      在按照上面的配置格式配置了之后，假如上面的数目是2，那么启动Nginx服务器后，在后台主机上查看Nginx的进程情况，可以看到应该是有2个Nginx进程。

c、错误日志的存放路径

      如：error_log logs/error.log  notice;  error_log 是个主模块指令，用来定义全局错误日志文件。日志输出级别有debug，info，notice，
      warn，error，erit可供选择，其中，debug输出日志最为详细，而crit输出日志最少。
      指定格式：error_log file  | stderr;
      file : 日志输出到某个文件file
      stderr : 日志输出到标准错误输出 （日志输出级别）。 

 d、Nginx进程PID存放路径
  
       如：pid logs/nginx.pid;   pid是个主模块指令，用来指定进程pid的存储文件位置。
       Nginx进程是作为系统守护进程在进行，需要在某个文件中保存当前运行程序的主进程号，Nginx支持该保存文件路径的定义。
       指令格式：pid file;
       file：指定存放路径和文件名称。
       如果不指定，则默认置于路径 logs/nginx.pid

 e、worker_rlimit_nofile 
  
      如：worker_rlimit_nofile 65535;  用来绑定worker进程和CPU，Linux内核2.4 以上可用


## EVENTS模块

events事件指令是设定Nginx的工作模式及连接数上限

1. use epoll;                //use是事件模块指令，用来指定Nginx的工作模式。Nginx支持的工作模式有select、poll、kqueue、epoll、rtsig和
                               /dev/poll 。其中select 和poll 都是标准的工作模式，kqueue和epoll是高效的工作模式，不同的是epoll用在Linux
                               平台上，而kqueue用在BSD系统中。对于Linux系统，epoll工作模式是首选

2. worker_connections 65536;  // work_connections也是个事件模块指令，用于定义Nginx每个进程的最大连接数，默认是1024

## HTTP模块


## HTTP全局模块

1. 定义MIMI-Type
   
  1.1  include  mime.types;     //该指令主要用于将其他的Nginx配置或第三方模块的配置引用到当前的主配文件中，减少主配置文件的复杂度
  
  1.2  default_type  application/octet-stream; //HTTP核心模块指令，这里设定默认类型为二进制流。也就是当文件类型未定义时使用这种方式

2. 自定义服务日志

     log_format main   //  log_format 是Nginx的HttpLog模块指令，用于指定Nginx日志的输出日志

     access_log  

3. 允许sendfile方式传输文件

   sendfile  on;      //参数on是表示开启高效文件传输模式，默认是关闭状态（off），
   
   tcp_nopush on;

tcp_nodelay on;     //将tcp_nopush和tcp_nodelay两个指令设置为on用于防止网络阻塞；

4. 连接超时时间

   keepalive_timeout 65;  // 设置客户端连接保持活动的超时时间。在超过这个时间之后，服务器会关闭该连接；

## Upsteam
upstream（负载均衡服务器设置）：指令主要用于负载均衡，设置一系列的后端服务器


## Server
server 块是对虚拟主机的配置

A、listen用于指定虚拟主机的服务端口，

B、server_name 用来指定IP地址或域名，多个域名之间用空格分开。

C、index用于设定访问的默认首页地址.。

D、root指令用于指定虚拟主机的网页根目录，这个目录可以是相对路径，也可以是绝对路径。

E、charset用于设置网页的默认编码格式。

F、access_log 用来指定虚拟主机的访问日志存放路径，最后的main 用于指定访问日志的输出格式。

## location   (Nginx配置中最灵活的部分)
location（URL匹配特定位置的设置）：用于匹配网页位置。URL地址匹配是进行Nginx配置中最灵活的部分。 location支持正则表达式匹配，也支持条件判断匹配，用户可以通过location指令实现Nginx对动、静态网页进行过滤处理。使用location URL匹配配置还可以实现反向代理，用于实现PHP动态解析或者负载负载均衡。

a、location配置

b、请求根目录配置

c、更改location的URI

d、网站默认首页配置



# 有用的参考

* [Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)



