
# 参考
* [Spring Cloud Alibaba Nacos注册中心](https://mrbird.cc/Spring-Cloud-Alibaba-Nacos%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83.html)


# 目录

## 第一步：下载安装

通过https://github.com/alibaba/nacos/releases链接可以下载Nacos的最新发行版，解压到指定目录

## 第二步：配置数据层

解压后，打开conf目录下的配置文件，在末尾添加数据源配置：

···

spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=123456

···

然后在MySQL数据库中新建nacos数据库，并导入Nacos解压包conf目录下的nacos-mysql.sql脚本，[通过Navicate for MySQL导入SQL文件](https://help.aliyun.com/document_detail/36315.html)

## 第三步：启动Nacos

* [单机模式启动 NACOS](#3-.-1-单机模式启动-NACOS)
* [集群模式启动 NACOS](#集群模式启动-NACOS)

### 3.1 单机模式启动 NACOS

数据层准备好后，我们就可以启动Nacos了

在指定目录中的bin目录下为启动和关停脚本，conf下为nacos的配置文件，target目录下为nacos的fat jar。

修改conf/application.properties配置文件，将应用端口改为8001 or 维持8848 原端口

server.port=8001

进入到 bin目录，

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
    
### 集群模式启动 NACOS
























