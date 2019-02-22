
# 目录

* IntelliJ IDEA 的配置
  * [全局JDK（默认配置）](#全局JDK（默认配置）)
  * [全局Maven（默认配置）](#全局Maven（默认配置）)
  * [版本控制Git/Svn （默认配置）](#版本控制Git/Svn（默认配置）)
  * [自动导包和智能移除（默认配置）](#自动导包和智能移除（默认配置）)
  * [Tomcat Server（当前项目配置） ](#Tomcat-Server（当前项目配置）)
  * [自动编译](#自动编译)
  * [取消大小写敏感](#取消大小写敏感)
## 全局JDK（默认配置）
 
 具体步骤：顶部工具栏  File ->Other Settins -> Default Project Structure -> SDKs -> JDK
 同理，当前项目在Project Structure可为工程和各模块设置喜欢的JDK版本
 
 ## 全局Maven（默认配置）
 
 具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Build & Tools -> Maven
 理论上只要配置了Maven主目录即可，实际开发推荐采用User Settins file .

## 版本控制Git/Svn（默认配置）

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Version Control -> Git


## 自动导包和智能移除（默认配置）

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Auto Import

## Tomcat Server（当前项目配置）

配置Tomcat方法： File -> Settings -> Deployment -> Application Servers -> Tomcat Server  

## 自动编译

1. 开启IDEA的自动编译（静态）

具体步骤：打开顶部工具栏 File -> Settings -> Default Settings -> Build -> Compiler 然后勾选 Build project automatically 。

2. 开启IDEA的自动编译（动态）

具体步骤：同时按住 Ctrl + Shift + Alt + /  然后进入Registry ，勾选自动编译并调整延时参数。

compiler.automake.allow.when.app.running   -> 自动编译
compile.document.save.trigger.delay  -> 自动更新文件

PS：网上极少有人提到compile.document.save.trigger.delay 它主要是针对静态文件如JS CSS的更新，将延迟时间减少后，免编译直接按F5刷新页面就能看到效果！

## 取消大小写敏感

具体步骤：

File | Settings | Editor | General | Code Completion Case | Sensitive Completion = None

取消大小敏感，在编写代码的时候，代码的自动提示将更加全面和丰富。








# 有用的参考

  * [IntelliJ IDEA 使用教程(2019图文版) -- 从入门到上瘾](https://www.jianshu.com/p/9c65b7613c30)
  * [SpringBoot 在IDEA中实现热部署(实用版)](https://www.jianshu.com/p/f658fed35786)
  * [IntelliJ IDEA 简体中文专题教程](https://github.com/judasn/IntelliJ-IDEA-Tutorial)
  * [IntelliJ IDEA使用教程 1 （总目录篇）](https://blog.csdn.net/weixin_42872270/article/details/81512713)
  * [IntelliJ IDEA使用教程 2 （总目录篇） ](https://blog.csdn.net/weixin_33400820/article/details/79105890)
  
