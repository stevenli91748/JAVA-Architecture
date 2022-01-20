
# 参考
* [Spring Cloud Alibaba Nacos注册中心](https://mrbird.cc/Spring-Cloud-Alibaba-Nacos%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83.html)


# 目录

**Nacos的单节点，也就是我们最开始使用的standalone模式，配置的数据是默认存储到内嵌的数据库derby中**

## 第一步：下载安装

配置JAVA_HOME环境变量，不配置会导致无法运行Nacos；

   JAVA_HOME=D:\developer\env\Java\jdk1.8.0_91

通过https://github.com/alibaba/nacos/releases链接可以下载Nacos的最新发行版，解压到指定目录

## 第二步：启动Nacos


### 单机模式启动 NACOS

            单机模式启动根本不需要配置任何数据，直接到bin目录下查找 startup.sh 文件
            
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
    

























