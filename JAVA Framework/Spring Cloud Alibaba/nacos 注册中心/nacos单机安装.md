
通过https://github.com/alibaba/nacos/releases链接可以下载Nacos的最新发行版

bin目录下为启动和关停脚本，conf下为nacos的配置文件，target目录下为nacos的fat jar。

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
    
