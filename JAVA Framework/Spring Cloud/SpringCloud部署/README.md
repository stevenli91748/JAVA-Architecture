

# 目录 
* 前端程序部署
  * [mall前端项目的安装与部署例子](http://www.macrozheng.com/#/deploy/mall_deploy_web)
* 后端单体程序部署
  * [mall在Windows环境下的部署](http://www.macrozheng.com/#/deploy/mall_deploy_windows) 
  * [mall在Linux环境下的部署（基于Docker容器）例子](http://www.macrozheng.com/#/deploy/mall_deploy_docker) 
  * [mall在Linux环境下的部署（基于Docker Compose）例子](http://www.macrozheng.com/#/deploy/mall_deploy_docker_compose)
  * [mall在Linux环境下的自动化部署（基于Jenkins）例子](http://www.macrozheng.com/#/deploy/mall_deploy_jenkins)
* 后端微服务程序部署
  * [mall-swarm微服务在Windows环境下的部署](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_windows)
  * [mall-swarm在Linux环境下的部署（基于Docker容器）](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_docker)
  * [微服务架构下的自动化部署，使用Jenkins来实现！](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_jenkins) 
  * [mall-swarm微服务项目在K8S下的实践！](http://www.macrozheng.com/#/deploy/mall_swarm_deploy_k8s)
* [结合Jenkins自动化部署](http://www.macrozheng.com/#/technology/springboot_auto_deploy?id=%e7%bb%93%e5%90%88jenkins%e8%87%aa%e5%8a%a8%e5%8c%96%e9%83%a8%e7%bd%b2)
* [使用Kubernetes管理Spring Cloud 各组件Docker化应用实例](https://weread.qq.com/web/reader/71d32370716443e271df020ke0032e0028be00da03b6659)---以上仅仅是Docker化而已，并没有管理起来，需要将应用实例部署到kubernetes上进行管理
  * 本地安装Kubernetes
  * 各组件Docker化应用实例部署到Kubernetes
    * 创建namespace
    * 创建replication controller
    * 创建service
    * 运行
* [kubernetes进行扩容、缩容以及滚动升级](https://weread.qq.com/web/reader/71d32370716443e271df020ke0032e0028be00da03b6659)
  * 一键伸缩---kubernetes有个scale命令，可以用来控制副本个数，既可用来扩容也可以用来缩容
  * 自动伸缩---Kubernetes1.2及以上版本提供了HPA(Horizontal Pod Autoscaling)功能特性，即可以根据CPU利用率等指标完成自动伸缩。这里我们需要组件来采集cpu利用率等指标，通常可以使用heapster或者轻量级的Kubernetes Metrics Server
  * 滚动升级
* [springcloud in k8s springcloud项目部署到k8s](https://www.jianshu.com/p/a58ff88ceda7)
