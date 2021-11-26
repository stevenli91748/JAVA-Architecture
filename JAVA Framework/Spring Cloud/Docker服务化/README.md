


# 目录
* 前端程序部署
  * [mall前端项目的安装与部署例子](http://www.macrozheng.com/#/deploy/mall_deploy_web)
* 后端单体程序部署
  * [mall在Windows环境下的部署](http://www.macrozheng.com/#/deploy/mall_deploy_windows) 
  * [mall在Linux环境下的部署（基于Docker容器）例子](http://www.macrozheng.com/#/deploy/mall_deploy_docker) 
  * [mall在Linux环境下的部署（基于Docker Compose）例子](http://www.macrozheng.com/#/deploy/mall_deploy_docker_compose)
  * [mall在Linux环境下的自动化部署（基于Jenkins）例子](http://www.macrozheng.com/#/deploy/mall_deploy_jenkins)
* 后端微服务程序部署
  * [mall-swarm微服务在Windows环境下的部署例子](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_windows)
  * [mall-swarm在Linux环境下的部署（基于Docker容器）例子](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_docker)
  * [微服务架构下的自动化部署，使用Jenkins来实现！例子](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_jenkins) 
  * [mall-swarm微服务项目在K8S下的实践！例子](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_k8s)
* [结合Jenkins自动化部署例子](http://www.macrozheng.com/#/technology/springboot_auto_deploy?id=%e7%bb%93%e5%90%88jenkins%e8%87%aa%e5%8a%a8%e5%8c%96%e9%83%a8%e7%bd%b2)
* [ Java服务Docker化](https://weread.qq.com/web/reader/71d32370716443e271df020k398323202893988c7f885f0)
  * [使用Docker方式发布微服务](https://weread.qq.com/web/reader/ca932ea071d7c798ca9a714k5fd32dd02725fd0b37cd75e) 
  * [使用Docker部署日志分析平台](https://weread.qq.com/web/reader/ca932ea071d7c798ca9a714kc45328f0274c45147dee704)
  * [基于Docker的高级部署工具](https://weread.qq.com/web/reader/ca932ea071d7c798ca9a714keb132680275eb160de1d35c)
  * [Spring Cloud组件的Docker化](https://weread.qq.com/web/reader/71d32370716443e271df020k01332b9028a013d407161b5)
    * [建造基础镜像](https://weread.qq.com/web/reader/71d32370716443e271df020k398323202893988c7f885f0)---基础镜像dockerfile的模板基本一致，这里都放到每个工程的根目录下面，跟pom.xml文件一个目录
      * DOckerfile的编写
        * 以 操作系统 + OpenJDK为基础编写的基础镜像
        * 以 操作系统 + Oracle JDK为基础编写的基础镜像  
      * 镜像构建插件
      * 打包构建
      * Push镜像
      * 运行镜像            
    * [Spring Cloud Config-Server的Docker化](https://weread.qq.com/web/reader/71d32370716443e271df020k01332b9028a013d407161b5)
    * [Spring Cloud Eureka-server的Docker化]()
    * [Spring Cloud Gateway的Docker化]()
    * [turbine的Docker化]()
    * [Spring Admin的Docker化]()
    * [业务微服务的Docker化]()
* [使用Kubernetes管理Spring Cloud 各组件Docker化应用实例](https://weread.qq.com/web/reader/71d32370716443e271df020ke0032e0028be00da03b6659)---以上仅仅是Docker化而已，并没有管理起来，需要将应用实例部署到kubernetes上进行管理
