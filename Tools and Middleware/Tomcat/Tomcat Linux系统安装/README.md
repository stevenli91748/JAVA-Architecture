
* 第一步  [root@single src]# tar zxvf apache-tomcat-7.0.77.tar.gz 
* 第二步  [root@single src]# cd apache-tomcat-7.0.77
* 第三步  [root@single apache-tomcat-7.0.77]# cd conf/
  
         [root@single apache-tomcat-7.0.77]# vim server.xml
         
         <role rolename="tomcat"/>
         <role rolename="role1"/>
         <role rolename="manager-gui"/>
         <user username="tomcat" password="tomcat" roles="tomcat,rolel,manager-gui"/>
 * 第四步 启动tomcat
 
         [root@single src]# cd apache-tomcat-7.0.77/bin
         [root@single bin]# ./startup.sh  
         
         tomcat start
         
  * 第五步 在本虚拟机上用brower浏览
  
           http://localhost:8080
           
           出现不能防问，可能Linux系统的防火墙没有开启端口
           
           [root@single apache-tomcat-7.0.77]# firewall-cmd --zone=public --add-port=8080/tcp --permanent
           [root@single apache-tomcat-7.0.77]# firewall-cmd --reload
           
           
         

