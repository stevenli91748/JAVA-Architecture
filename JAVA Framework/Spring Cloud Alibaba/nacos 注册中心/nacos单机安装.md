
# 参考
* [Spring Cloud Alibaba Nacos注册中心](https://mrbird.cc/Spring-Cloud-Alibaba-Nacos%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83.html)


# 目录
* [1 配置JAVA_HOME环境变量](#1-配置JAVA_HOME环境变量)
* [2 单机模式下运行Nacos使用内嵌的数据库方式](#1-单机模式下运行Nacos使用内嵌的数据库方式)



# 1 配置JAVA_HOME环境变量

**Nacos的单节点，也就是我们最开始使用的standalone模式，配置的数据是默认存储到内嵌的数据库derby中**

   * [linux 查看java的安装路径，并配置JAVA_HOME环境变量](https://blog.csdn.net/u013626215/article/details/88964331?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_paycolumn_v3&utm_relevant_index=5)
   * [linux配置java环境变量(详细)](https://www.cnblogs.com/samcn/archive/2011/03/16/1986248.html)
   * [Which: no javac in (/ usr / local / SBIN / usr / local / bin / usr / SBIN / usr / bin / root / bin)](https://cdmana.com/2021/07/20210722163907299e.html)
      
----

  * 配置JAVA_HOME环境变量, 下载安装Nacos
 
    配置JAVA_HOME环境变量，不配置会导致无法运行Nacos；
 
   Linux/Unix/Mac:
   
   假如没有设置JAVA_HOME环境变量， 就启动 Nacos , 会出现如下错误：
   
   [root@localhost bin]# bash startup.sh -m standalone

     which: no javac in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)
     readlink:  miss operand 
     Try 'readlink --help' for more information.
     dirname:  miss operand 
     Try 'dirname --help' for more information.
     ERROR: Please set the JAVA_HOME variable in your environment, We need java(x64)! jdk8 or later is better! !!
     
     
     [root@localhost bin]# yum search jdk  //查找在yum中的JDK version
     ......
     java-1.8.0-Openjdk.x86_64      // 这是OpenJDK 8 Runtime Environment
     .....
     java-1.8.0-Openjdk-devel.x86_64  // 这是 jdk的开发環境（OpenJDK 8 Development Environment）(必需选择jdk的开发環境 OpenJDK 8 Development Environment）
     .....
   
     [root@localhost bin]# yum install  java-1.8.0-Openjdk-devel.x86_64
     
     查找linux中 JDK的安装目录，以便设置JAVA_HOME的環境变量
     
      [root@localhost ~]# java -version
       java 1.8.0
      
      
      [root@localhost ~]# which java
       /usr/bin/java
      
      [root@localhost ~]# ll /usr/bin/java 
       lrwxrwxrwx. 1 root root 22 2 month   24 12:26 /usr/bin/java -> /etc/alternatives/java
       
      [root@localhost ~]# ll /etc/alternatives/java
      lrwxrwxrwx. 1 root root 73 2 month   24 12:26 /etc/alternatives/java -> /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64/jre/bin/java 
      
      得到JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64  (注意： 不要带 /)
      
      
      [root@centos7-2 nacos]# vi /etc/profile
      
      export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64         
      export JRE_HOME=$JAVA_HOME/jre
      export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
      export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
      
      [root@localhost ~]# source /etc/profile
      
      [root@localhost ~]# reboot
     

# 2 单机模式下运行Nacos使用内嵌的数据库方式

   通过https://github.com/alibaba/nacos/releases链接可以下载Nacos的最新发行版，解压到指定目录

   **单机模式下运行Nacos使用内嵌的数据库方式启动时根本不需要配置任何数据，直接到bin目录下查找 startup.sh 文件**
            
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
    

   **关闭Nacos:**

          在bin 目录下，查找 shutdown.sh
          
           Linux/Unix/Mac:
           
               root>  sh shutdown.sh
               
           windows
           
              cmd>  shutdown
          
























