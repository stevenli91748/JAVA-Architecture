
# 目录

* [IntelliJ IDEA 的配置](#IntelliJ-IDEA-的配置)
  * [IDEA的配置](#IDEA的配置)
    * [IDEA 设置代码行宽度](#IDEA-设置代码行宽度)
    * [IDEA 提示不区分大小写](#IDEA-提示不区分大小写)
    * [自动编译](#自动编译)
    * [自动导包和智能移除（默认配置）](#自动导包和智能移除)
    * [主题的配置](#主题的配置)
    * [设置窗体及菜单的字体及字体大小](#设置窗体及菜单的字体及字体大小)
    * [设置窗体背境图案](#设置窗体背境图案)
    * [表现模式的字体大小](#表现模式的字体大小)
    * [鼠标滚轮修改字体的配置](#鼠标滚轮修改字体的配置)
    * [设置鼠标悬浮提示](#设置鼠标悬浮提示)
    * [设置行号和方法间的分隔符](#设置行号和方法间的分隔符)
    * [多行显示Tab的操作](#多行显示Tab的操作)
    * [设置默认的字体 字体大小 字体行间距](#设置默认的字体-字体大小-字体行间距)
    * [设置代码中注释的字体颜色](#设置代码中注释的字体颜色)
    * [设置类头的文档注释信息](#设置类头的文档注释信息)
    * [设置项目文件编码](#设置项目文件编码)
    * [设置代码水平或垂直显示](#设置代码水平或垂直显示)
  * [Project的配置]()
  * [Maven的配置](#Maven的配置)
    * [设置maven](#设置maven)
    * [IntelliJ强制更新Maven Dependencies](#IntelliJ强制更新Maven-Dependencies)
  * [Version Control的配置](#Version-Control的配置)
    * [Git的配置](#Git的配置)
  * [Tomcat的配置]()
  * [Nginx的配置]()
  * [Database的配置]()
    * [MySQL的配置]()
    * [Oracle的配置]()
  * [Web工程的配置]()
    * [Static Web的配置]()
    * [Spring的配置]()
    * [SpringMVC的配置]()
    * [MyBatis的配置]()
    * [SSM集成的配置]()
    * [Spring Boot的配置]()
    * [Spring Cloud的配置]()
  
* plugin
  * [Maven Helper插件](#Maven-Helper插件) 
  * [GsonFormat 转化Json字符串](#GsonFormat-转化Json字符串)
  * [Markdown](#Markdown)
  * [FindBugs](#FindBugs)
  * [ECTranslation](#ECTranslation)
  * [CheckStyle](#CheckStyle)
  * [MyBatisCodeHelp](#MyBatisCodeHelp)

* IntelliJ IDEA 使用技巧

  * [Intellij IDEA神器居然还有这些小技巧](http://blog.didispace.com/intellij-idea-some-features-sam-1/)

# IntelliJ IDEA 的配置

## IDEA的配置

### IDEA 设置代码行宽度

    第一种方式：    在File->settings->Editor->Code Style
    第二种方式      在File->settings->Code Style->Java中

### IDEA 提示不区分大小写

    File–>Settings–>Editor–>General–>Code Completion–>Match case的勾取消掉就可以了 
    
### 主题的配置

     file->settings->Appearance & Behavior-> Appearance-> Theme

### 设置窗体及菜单的字体及字体大小

     file->settings->Appearance & Behavior-> Appearance->  use custom font      size  
     
### 设置窗体背境图案

     file->settings->Appearance & Behavior-> Appearance-> background Image... 
     
### 表现模式的字体大小

     file->settings->Appearance & Behavior-> Appearance->Presentation Mode font size  

### 鼠标滚轮修改字体的配置

     file->settings->Editor->General->change font size (Zoom) with Ctrl+Mouse Wheel

### 设置鼠标悬浮提示

     file->settings->Editor->General-> Show quick documentation on mouse move

### 设置行号和方法间的分隔符

    file->settings->Editor->General->Appearance-> Show line numbers
                                                  Show method separators

### 多行显示Tab的操作

      file->settings->Editor->General->Editor Tabs->show tabs in single row

### 设置默认的字体 字体大小 字体行间距

       file->settings->Editor->Font->

### 设置代码中注释的字体颜色

       file->settings->Editor->Color Scheme->Language Defaults->Comments->Line Comment
                                                                          Block Comment
                                                                          Doc  Comment

### 设置类头的文档注释信息

     file->settings->Editor->File and Code Templates-> tab includes-> file header

### 设置项目文件编码

     file->settings->Editor->File Encodings-> Global Encoding
                                              Project Encoding
                                              Default encoding for properties files
                                              Transparent native to ascii conversion
                                              
### 设置代码水平或垂直显示

     代码tab上右击，选 Split Vertically or Split Horizontally
                                              
# Maven的配置

## 设置maven

    1.在File->settings->搜索maven

    2.Mavan home directory--设置maven安装包的bin文件夹所在的位置

    3.User settings file--设置setting文件所在的位置

    4.Local repository--设置本地仓库

## IntelliJ强制更新Maven Dependencies

      Intellj IDEA 的自动载入Mave依赖的功能虽然好用，但有时候依赖多了或者缓存出问题，就会导致修改pom文件却没有触发自动重新载入的动作，这个时候
      就需要手动强制让Intellj IDEA更新依赖了。主要需要以下两步：

       手动删除Project Settings里面的Libraries内容
       mvn clean删除之前编译过的文件
       Reimport all maven projects

# Version Control的配置

## Git的配置

     1. Setting->version control->git->path    (git.exe 安装路径)
     
     2. Setting->version control->Github       (github account and password)

---


## 全局JDK（默认配置）
 
      具体步骤：顶部工具栏  File ->Other Settins -> Default Project Structure -> SDKs -> JDK
      同理，当前项目在Project Structure可为工程和各模块设置喜欢的JDK版本
 
 ## 全局Maven（默认配置）
 
      具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Build & Tools -> Maven
      理论上只要配置了Maven主目录即可，实际开发推荐采用User Settins file .
 
 

## 版本控制Git/Svn（默认配置）

     具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Version Control -> Git


## 自动导包和智能移除

      具体步骤：顶部工具栏  File ->Settings -> Editor->General -> Auto Import-> set all for insert imports on paste
                                                                              Add unambiguous imports on the fly 自动导入不明确的结构
                                                                              Optimize imports on the fly (for current project) 自动帮我们优化导入的包
                                                                

## Tomcat Server（当前项目配置）

     配置Tomcat方法： File -> Settings -> Deployment -> Application Servers -> Tomcat Server  

## 自动编译

    1. 开启IDEA的自动编译（静态）

       具体步骤：打开顶部工具栏 File -> Settings ->Build Execution Deployment->Complier-> Build project automatically
                                                                                        Compile independent modules in parallel

    2. 开启IDEA的自动编译（动态）

       具体步骤：同时按住 Ctrl + Shift + Alt + /  然后进入Registry ，勾选自动编译并调整延时参数。

         compiler.automake.allow.when.app.running   -> 自动编译
         compile.document.save.trigger.delay  -> 自动更新文件

         PS：网上极少有人提到compile.document.save.trigger.delay 它主要是针对静态文件如JS CSS的更新，将延迟时间减少后，免编译直接按F5刷新页
         面就能看到效果！



# plugin

 ## Maven Helper插件
  
     可在项目中查找到版本冲突的插件，并解决冲突。  

## GsonFormat 转化Json字符串

## Markdown

## FindBugs

    查找BUG

## ECTranslation

## CheckStyle

## MyBatisCodeHelp
    




# 有用的参考
  * [IntelliJ IDEA官方文档](https://www.w3cschool.cn/intellij_idea_doc/)
  * [intellij idea SpringMVC 配置FreeMarker模板引擎](https://blog.csdn.net/w8897282/article/details/71215591#理解springmvc的配置)
  * [intellij idea搭建SSM框架摘录](https://blog.csdn.net/w8897282/article/details/71173211)
  * [IntelliJ IDEA 使用教程(2019图文版) -- 从入门到上瘾](https://www.jianshu.com/p/9c65b7613c30)
  * [SpringBoot 在IDEA中实现热部署(实用版)](https://www.jianshu.com/p/f658fed35786)
  * [IntelliJ IDEA 简体中文专题教程](https://github.com/judasn/IntelliJ-IDEA-Tutorial)
  * [IntelliJ IDEA使用教程 1 （总目录篇）](https://blog.csdn.net/weixin_42872270/article/details/81512713)
  * [IntelliJ IDEA使用教程 2 （总目录篇） ](https://blog.csdn.net/weixin_33400820/article/details/79105890)
  * [五分钟阅读阿里巴巴架构师如何在IntelliJ IDEA上使用微服务框架搭建电商平台全过程](https://blog.csdn.net/pingdouble/article/details/79527044)
  * [IDEA上面搭建一个SpringBoot的web-mvc项目](https://blog.csdn.net/linzhiqiang0316/article/details/52589789)
  * [IDEA中创建Maven聚合项目（best）](https://blog.csdn.net/CarryBest/article/details/84339784)
  * [IntelliJ IDEA中手工创建Web聚合项目(Maven多模块项目) 不用模板创建](https://blog.csdn.net/u012702547/article/details/77431765)
  * [SSM框架——详细整合教程](https://blog.csdn.net/zhshulin/article/details/37956105#comments)
  * [Intellij IDEA神器居然还有这些小技巧](http://blog.didispace.com/intellij-idea-some-features-sam-1/)
  * [使用intellij idea搭建SSM架构的maven项目 超详细](https://blog.csdn.net/niqinge/article/details/79280204)
  * [用intellij idea2018搭建ssm框架：SpringMVC+Spring+Mybatis](https://blog.csdn.net/nba_linshuhao/article/details/82799685)
  * [idea中配置maven](https://blog.csdn.net/nba_linshuhao/article/details/82715485)
  * [超详细图解从0搭建SSM框架【intellij idea】](https://blog.csdn.net/FYGu18/article/details/79056075)
  * [IntelliJ IDEA使用教程（很全](https://www.imooc.com/article/28558)
  * [实战 java 第1天：从零开始搭建项目,实现第一个接口（IDEA）](https://blog.csdn.net/weixin_44135121/article/details/92801713)
  * [用IntelliJ IDEA新建web项目 一步一步从零开始 搭建](https://blog.csdn.net/loveyouluobin/article/details/80062382)
  * [IDEA导入Tomcat源码](https://blog.csdn.net/u013268035/article/details/81349341)
