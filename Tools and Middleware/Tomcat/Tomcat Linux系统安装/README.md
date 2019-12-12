
* 第一步  [root@single src]# tar zxvf apache-tomcat-7.0.77.tar.gz 
* 第二步  [root@single src]# cd apache-tomcat-7.0.77
* 第三步  [root@single apache-tomcat-7.0.77]# cd conf/
  
         [root@single apache-tomcat-7.0.77]# vim server.xml
         
         <role rolename="tomcat"/>
         <role rolename="role1"/>
         <role rolename="manager-gui"/>
         <user username="tomcat" password="tomcat" roles="tomcat,rolel,manager-gui"/>
         
         

