

# 目录 
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
