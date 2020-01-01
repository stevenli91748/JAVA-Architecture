* 第0步  安装tomcat前需要确认jdk 安装了没有
          
          [root@single ~]# java -version
                           java version "1.7.0_65"
                           OpenJDK Runtime Environment (rhel-2.5.1.2.0.1.el6_5-x86_64 u65-b17)
                           OpenJDK 64-Bit Server VM (build 24.65-b04, mixed mode)
          [root@single ~]# rpm -qa | grep java
                            tzdata-java-2014g-1.el6.noarch
                            java-1.7.0-openjdk-1.7.0.65-2.5.1.2.0.1.el6_5.x86_64
                            java-1.6.0-openjdk-1.6.0.0-11.1.13.4.el6.x86_64
          [root@single ~]# 

         如果发现有安装好的OpenJDK以及安装包的话那么首先依次执行卸载。
         卸载命令：

          [root@single ~]# rpm -e java-1.7.0-openjdk-1.7.0.65-2.5.1.2.0.1.el6_5.x86_64
          [root@single ~]# rpm -qa | grep java
          tzdata-java-2014g-1.el6.noarch
          java-1.6.0-openjdk-1.6.0.0-11.1.13.4.el6.x86_64
          [root@single ~]# rpm -e java-1.6.0-openjdk-1.6.0.0-11.1.13.4.el6.x86_64
          [root@single ~]# rpm -qa | grep java
          tzdata-java-2014g-1.el6.noarch
          [root@single ~]# 

* 第一步  [root@single src]# tar zxvf apache-tomcat-7.0.77.tar.gz 
* 第二步  [root@single src]# cd apache-tomcat-7.0.77
* 第三步  [root@single apache-tomcat-7.0.77]# cd conf/
  
         [root@single apache-tomcat-7.0.77]# vim server.xml

         进入tomcat的conf目录下，修改server.xml文件，通过vi命令打开文件后直接输入/8080检索到端口号的位置，进入编辑模式后修改端口号为9090

         [root@single apache-tomcat-7.0.77]# vim tomcat-user.xml
         
         <role rolename="tomcat"/>
         <role rolename="role1"/>
         <role rolename="manager-gui"/>
         <user username="tomcat" password="tomcat" roles="tomcat,rolel,manager-gui"/>
         
         复制一份tomcat目录到 /home/tomcat
         
         [root@single src]# cp -Rf apache-tomcat-7.0.77 /home/tomcat

         配置环境变量
         
         打开配置文件
         vim /etc/profile

         添加配置信息
                    JAVA_HOME=/usr/java/jdk1.8.0_131
                    TOMCAT_HOME=/home/tomcat/apache-tomcat-7.0.77
                    CATALINA_HOME=/home/tomcat/apache-tomcat-7.0.77
                    PATH=$JAVA_HOME/bin:$PATH
                    PATH=$CATALINA_HOME/bin:$PATH
                    CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
                    export JAVA_HOME
                    export TOMCAT_HOME
                    export CATALINA_HOME
                    export PATH
                    export CLASSPATH
     
       
         
 * 第四步 启动tomcat
         查看tomcat的安装目录
         [root]# cat /etc/profile
 
         [root@single src]# cd apache-tomcat-7.0.77/bin
         [root@single bin]# ./startup.sh  
         
         tomcat start
         
  * 第五步 在本虚拟机上用brower浏览
  
           http://localhost:8080
           
           出现不能防问，可能Linux系统的防火墙没有开启端口
           
           [root@single apache-tomcat-7.0.77]# firewall-cmd --zone=public --add-port=8080/tcp --permanent
           [root@single apache-tomcat-7.0.77]# firewall-cmd --reload
           
           看一下public区域下所有已打开的端口
           
           [root@single apache-tomcat-7.0.77]# firewall-cmd --zone=public --list-ports
           [root@single apache-tomcat-7.0.77]# 9090/tcp
           可以看到9090端口已经成功打开：
     
    * 第六步 在本虚拟机浏览器上输入
    
            http://localhost:9090
            
            成功出现Tomcat猫的图像
            
            
     * 第七步 在物理主机浏览器上输入
         
             http://虚拟机IP：9090
             
             成功出现Tomcat猫的图像
             

# 疑难解析

* [Tomcat中不能通过访问自己IP，但可以通过localhost/127.0.0.1访问](https://www.cnblogs.com/12three/p/11127338.html)
