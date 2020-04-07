


[intellij IDEA 2019.3 Linux系统安装](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/IntellijIDEA/intellij%20IDEA%202019.3%20Linux系统安装/README.md)| [Intellij IDEA快捷键大全](https://blog.csdn.net/alik20/article/details/54881003)|
---|---|

[IDEA中Debug javaScript功能](https://www.bilibili.com/video/av22537758?p=2)|[在IDEA中个性化配置JVM参数](https://www.bilibili.com/video/av22537758?p=1)|
---|---|



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
  * [快捷键的设置](#快捷键的设置)  
    * [快捷键的搜索](#快捷键的搜索)
    * [执行 alt+r]
    * [提示补全 alt+/]
    * [如何查看源码 ctrl+shift+t]
  * [Project的配置]()
    * [普通Java工程设置]()
    * [普通Java Web工程设置]()
    * [Maven工程的配置](#Maven的配置)
      * [设置maven](#设置maven)
        * [设置maven java 工程](#设置maven-java-工程)
        * [设置maven java Web工程](#设置maven-java-Web工程)
        * [设置maven父子工程](#设置maven父子工程)
          * [maven父子工程的第一种继承关系](https://www.jb51.net/article/157941.htm)
          * [maven父子工程的第二种继承关系](https://www.jb51.net/article/157941.htm)
    * [Web工程的配置]()
      * [Static Web的配置]()
      * [Spring的配置]()
      * [SpringMVC的配置]()
      * [MyBatis的配置]()
      * [SSM集成的配置]()
      * [Spring Boot的配置]()
      * [Spring Cloud的配置]()
    * [IntelliJ强制更新Maven Dependencies](#IntelliJ强制更新Maven-Dependencies)
  * [Version Control的配置](#Version-Control的配置)
    * [Git的配置](#Git的配置)
  * [Tomcat的配置]()
    * [tomcat实现热部署的配置](#tomcat实现热部署的配置)
  * [Nginx的配置]()
  * [Database的配置]()
    * [MySQL的配置]()
    * [Oracle的配置]()
  * [IDEA Debug处理]()
  * [Project的打包处理]()
    * [Maven Project的打包处理]()
    * [非Maven Project的打包处理]()

  
  
  
* plugin
  * [Maven Helper--Maven辅助插件](#Maven-Helper插件) 
  * [GsonFormat 转化Json字符串](#GsonFormat-转化Json字符串)
  * [FindBugs---潜在Bug检查](#FindBugs)
  * [ECTranslation](#ECTranslation)
  * [CheckStyle](#CheckStyle)
  * [ Grep Console 自定义控制台输出格式插件,可以设置不同级别log的字体颜色和背景色]
  * [SonarLint -代码质量检查插件]
  * [Alibaba Java Coding Guidelines   阿里巴巴代码规范检查插件]
  * [Lombok plugin -- 注解自动生成代码]
  * [CodeGlance -代码地图]
  * [.ignore -git忽略文件]
  * [String Manipulation -一款强大的字符串转换工具]
  * [Key Promoter X -一款可以进行快捷键提示的插件]
  * [Iedis  Redis可视化]
  * [JUnitGenerator 单元测试测试生成工具]
  * [MetricsReloaded 代码复杂度检查]
  * [Statistic 代码统计插件]
  * [JRebel Plugin 热部署插件]
  * [Markdown Navigator---Markdown 编辑器插件]
  * [Jindent-Source Code Formatter ---自定义模板插件]
  * [Properties to YAML Converter ---Properties 转 YAML 格式插件]
  * [Git Flow Integration ---Git Flow 集成插件]
  * [Custom Postfix Templates---自定义Postfix模板]
  * [RestfulToolkit---Java WEB开发必备，再也不用全局搜索RequestMapping]
  * [GenerateSerialVersionUID---Alt+Insert生成serialVersionUID]
  * [SonarLint---编码风格不好，这款插件很适合你。当你的编码不规范时，提醒你代码写得有毛病，快改]
  * [ASM Bytecode Outline---查看 Class 类的字节码]
  * [IDEA Mind Map---思维导图插件]



* [Troubshooting](#Troubshooting)

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
   
# 快捷键的设置

## 快捷键的搜索

    2种快捷键的搜索方式
    
    1. 按功能搜索
      
       搜索你只记的功能的部分英文名
    
    2. 按快捷键搜索
    
       输入快捷键，搜索出对应功能


# Maven的配置

## 设置maven
 
### 设置maven java 工程
    
    1.File->new->project->Maven

    2. create from archetype -> org.apache.maven.archetypemaven- archetype-quickstart 模板
    
    3. 设置 Groupid, Artifactid, Version
    
    4. Mavan home directory--设置maven安装包的bin文件夹所在的位置

    5. User settings file--设置setting文件所在的位置

    6. Local repository--设置本地仓库

    7. 在Properties 中 要加入一个属性，默认没有，必需要加入该属性，不然 Maven JAVA 工程的创建过程会很慢
       Name : archetypeCatalog     Value: internal 
       
    8. project name and project location
    
    9. 打开project 目录，找到src -> main ->java 目录，right click 选择 Mark Directory as Sources Root
                            src -> main -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as resource Root                                                                                      
                         找到src -> test -> java 目录,right click 选择 Mark Directory as test Sources Root
                             src -> test -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as test resource Root 
                             
### 

### 设置maven java Web工程

    1. File->new->project->Maven
     
    2. create from archetype -> maven-archetype-webapp 模板
    
    3. 设置 Groupid, Artifactid, Version
    
    4. Mavan home directory--设置maven安装包的bin文件夹所在的位置

    5. User settings file--设置setting文件所在的位置

    6. Local repository--设置本地仓库
    
    7. 清除project的pom.xml中的不要的信息
    
    8. 打开project 目录，找到src -> main ->java 目录，right click 选择 Mark Directory as Sources Root
                            src -> main -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as resource Root                                                                                      
                         找到src -> test -> java 目录,right click 选择 Mark Directory as test Sources Root
                             src -> test -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as test resource Root 
    9. 设置project的编译级别
       
       在setting->build execution deployment-> Compiler->java compiler查看编译级别。如果是 vresion 1.5（IDEA 会默任最小版本）,但是你
       在project中设置的JDK是version 1.8，就要修改POM.XML文件，
       
       <build>
         <plugins>
           <plugin>
             <artifacID>maven-compiler-plugin</artifacID>
             <!--插件的版本-->
             <version>3.5.1</version>
             <!--编译级别-->
             <configuration>
                 <source>1.8</source>
                 <target>1.8</target>
                 <!--编码格式-->
                 <encoding>UTF-8</encoding>
             </configuration>
           </plugin>
         </plugins>
       </build>
       
       10. 修改Web.xml文件
        
           在project目录树
           
           project->src->main-> webapp->web-inf->web.xml,可以用普通JavaWEB工程中的web.xml覆盖就行了
           
       11. 配置TOMCAT SERVER   
       
       12. DEBUG 运行程序
           
### 设置maven父子工程

    1. 先设置父工程
     
       1.1 创建maven父工程
        
           file->new->project->maven->不管选不选原型都可以->设置GAV->location
           
       1.2 在maven父工程中，如果它想当一个父工程，一定要删除掉SRC目录，也就是说在maven父工程目录下只有一个pom.xml文件。
       1.3 在maven父工程中的POM.XML文件中packaging标签值必需设置为"POM"
       
           <packaging> pom </packaging>
           
     2. 添加子工程
     
       2.1  file->new->Module-> maven-> 无论Java 程序或Java Web 程序选或不选原型都行 ->设置 Add as module to : 父工程名称
                                                                                         Parent           : 父工程名称
                                                                                         Groupid 继承 父工程
                                                                                         version 继承 父工程
                                                                                          antifiacID 不要输父工程相同的名称，要唯一的名字
       2.2  子工程物理目录必需放在父目录下面。 
       
       2.3  打开子工程目录，找到src -> main ->java 目录，right click 选择 Mark Directory as Sources Root
                            src -> main -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as resource Root                                                                                      
                         找到src -> test -> java 目录,right click 选择 Mark Directory as test Sources Root
                             src -> test -> resource目录, 如果没有就创建目录，right click 选择 Mark Directory as test resource Root 

### maven父子工程的第一种继承关系        





### maven父子工程的第二种继承关系                                                              
                                                              
           
           
       
       
     
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

## 自动导包和智能移除

      具体步骤：顶部工具栏  File ->Settings -> Editor->General -> Auto Import-> set all for insert imports on paste
                                                                              Add unambiguous imports on the fly 自动导入不明确的结构
                                                                              Optimize imports on the fly (for current project) 自动帮我们优化导入的包
                                                                

## Tomcat Server（当前项目配置）

     配置Tomcat方法1： File -> Settings ->Build Execution Deployment -> Application Servers -> + ->Tomcat Server-> Tomcat Home
                                                                                                                 Tomcat base directory
                                                                                                                 
                                                                                                                 
     配置Tomcat方法2: run-> Edit Configration -> + ->Tomcat Server->                                                                                                                 

## tomcat实现热部署的配置

   [Tomcat实现热部署的几种方式](https://blog.csdn.net/qq_34567887/article/details/80551251)


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

## Grep Console 可以设置不同级别log的字体颜色和背景色.

## SonarLint -代码质量检查插件

## Alibaba Java Coding Guidelines   阿里巴巴代码规范检查插件

## [Lombok plugin -简化实体类编写插件 代码注解插件](https://www.jianshu.com/p/b20d1973f819)

## CodeGlance -代码地图

## .ignore -git忽略文件

## String Manipulation -一款强大的字符串转换工具

## Key Promoter X -一款可以进行快捷键提示的插件

## Iedis  Redis可视化

## JUnitGenerator 单元测试测试生成工具


# Troubshooting

[IDEA中Maven依赖下载失败解决方案](https://blog.csdn.net/ThinkWon/article/details/101312918)|
---|


# 视频
* [IntelliJ IDEA神器使用技巧](https://www.imooc.com/learn/924)
* [【2019】IntelliJ IDEA 入门到实战教程](https://www.bilibili.com/video/BV1DE411r7r1/?spm_id_from=333.788.videocard.0)
* [IntelliJ IDEA | Full Course | 2020](https://www.youtube.com/watch?v=yefmcX57Eyg)
 * [2019Java青橙商城项目实战-IDEA版SSM大型分布式电商项目实战](https://www.bilibili.com/video/av80932917?from=search&seid=9392663643465024311)
 * [2019】IntelliJ IDEA 入门到实战教程](https://www.bilibili.com/video/av71609179/?spm_id_from=333.788.videocard.3)
 * [[win环境搭建教程]MySQL+Tomcat+Nginx+Redis+Maven+IDEA配置](https://www.bilibili.com/video/av50482524?from=search&seid=7338193802476677901)
 * [Intellij-全套IDEA视频教程](https://www.bilibili.com/video/av48076362/?spm_id_from=333.788.videocard.9)
 * [2019最新Java IDEA版教程-4(高级篇-mybatis、Spring、SSM等等）](https://www.bilibili.com/video/av71389596/?spm_id_from=333.788.videocard.1)
* [IDEA部署springboot程序到docker上](https://www.bilibili.com/video/av40407605/?spm_id_from=333.788.videocard.14)
* [IDEA教程-干货大合集](https://www.bilibili.com/video/av34324988?p=3)
# 有用的参考
  * [使用 IDEA + Maven + Git 快速开发 Java Web 应用](https://my.oschina.net/huangyong/blog/175363)
  * [IEDA教程](https://github.com/stevenli91748/IntelliJ-IDEA-Tutorial)
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
  * [Intellij IDEA 2017 debug断点调试技巧与总结详解篇](https://blog.csdn.net/qq_27093465/article/details/64124330)
