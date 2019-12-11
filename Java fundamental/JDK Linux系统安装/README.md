
* 1. 从官网下载JDK到Linux中/opt目录中
* 2. rpm -ivh /opt/jdk-7uq-linux-x64.rpm
* 3. 配置环境变量
     1、 打开配置文件
          vim /etc/profile

     2、添加配置信息
          JAVA_HOME=/usr/java/jdk1.8.0_131
          TOMCAT_HOME=/home/tomcat/apache-tomcat-7.0.77
          PATH=$JAVA_HOME/bin:$PATH
          CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
          export JAVA_HOME
          export TOMCAT_HOME
          export PATH
          export CLASSPATH

