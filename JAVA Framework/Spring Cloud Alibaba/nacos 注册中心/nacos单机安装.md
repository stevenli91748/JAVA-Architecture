
# 参考
* [Spring Cloud Alibaba Nacos注册中心](https://mrbird.cc/Spring-Cloud-Alibaba-Nacos%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83.html)


# 目录

* [1 单机模式下运行Nacos使用内嵌的数据库方式](#1-单机模式下运行Nacos使用内嵌的数据库方式)
* [2 单机模式下运行Nacos使用mysql方式](#2-单机模式下运行Nacos使用mysql方式)

# 1 单机模式下运行Nacos使用内嵌的数据库方式

**Nacos的单节点，也就是我们最开始使用的standalone模式，配置的数据是默认存储到内嵌的数据库derby中**

  * 第一步：配置JAVA_HOME环境变量, 下载安装Nacos
 
     1 配置JAVA_HOME环境变量，不配置会导致无法运行Nacos；
      
   [linux 查看java的安装路径，并配置JAVA_HOME环境变量](https://blog.csdn.net/u013626215/article/details/88964331?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_paycolumn_v3&utm_relevant_index=5)
   
   [linux配置java环境变量(详细)](https://www.cnblogs.com/samcn/archive/2011/03/16/1986248.html)
   
   [Which: no javac in (/ usr / local / SBIN / usr / local / bin / usr / SBIN / usr / bin / root / bin)](https://cdmana.com/2021/07/20210722163907299e.html)
   
      配置完JAVA_HOME環境变量后，要重启(reboot)
      
     2 通过https://github.com/alibaba/nacos/releases链接可以下载Nacos的最新发行版，解压到指定目录

* 第二步：单机模式下使用内嵌的数据库方式启动Nacos

  **单机模式下运行Nacos使用内嵌的数据库方式**

            单机模式下运行Nacos使用内嵌的数据库方式启动时根本不需要配置任何数据，直接到bin目录下查找 startup.sh 文件
            
            Linux/Unix/Mac:

                启动命令(standalone代表着单机模式运行，非集群模式):

                sh startup.sh -m standalone

                如果您使用的是ubuntu系统，或者运行脚本报错提示[[符号找不到，可尝试如下运行：

                bash startup.sh -m standalone

            Windows:

                在DOS 下 以 单机模式启动 NACOS
                启动命令(standalone代表着单机模式运行，非集群模式):

                startup.cmd -m standalone


            当nacos启动成功后， 在浏览器 http://localhost:8001/nacos or http://localhost:8848/nacos    

            然后登录，

            ID：       nacos
            password:  nacos
    

* 第三步：关闭Nacos

          在bin 目录下，查找 shutdown.sh
          
           Linux/Unix/Mac:
           
               root>  sh shutdown.sh
               
           windows
           
              cmd>  shutdown
          

# 2 单机模式下运行Nacos使用mysql方式






















