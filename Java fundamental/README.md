
# [Java基本知识面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/README.md)

# 在linux下查看json文件  
 
     在linux下，可以通过curl工具访问url，因为返回的是json串，非格式化的。可以结合python命令，转为格式化的json，让看起来比较清楚。如：
     [root]# curl http://192.168.142.138:2375/images/json | python -mjson.tool
     or 
     [root]# cat /etc/docker/docker.json | python -mjson.tool

# 目录

* [java程序的执行顺序](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/java程序的执行顺序.md)

* [基本程序设计结构](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/基本程序设计结构.md)
  
* [Java面向对象](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Java面向对象.md)
 
* [继承](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/继承.md)
* [java 转义符](https://blog.csdn.net/stewen_001/article/details/22871737)  
  
* [接口](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/接口.md)
* [Lambda标达式](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Lambda标达式.md)
* [内部类](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/内部类.md)
  
* [异常 断言和日志](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/异常%20断言和日志.md)
* [泛型](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/泛型.md)
* [集合](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/集合.md)
* [JAVA 8的新特性](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/JAVA%208的新特性.md)
* [Java常用类](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Java常用类.md)
* [输入与输出](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/输入与输出.md)
* [XML](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/XML.md)
* [JAVA网络操作](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/JAVA网络操作.md)
* [JAVA数据库操作](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/JAVA数据库.md)
  *  [JDBC](#JDBC)
* [国际化](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/国际化.md)
* [本地方法](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/本地方法.md)
* [脚本 编译与注解](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/脚本%20编译与注解.md)
* [反射](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/反射.md)
* [注解](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/注解.md)
* [JSON](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/JSON.md)
* [正则表达式](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/正则表达式.md)
* [文本操作](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/文本操作.md)
* [数组](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/数组.md)
* [容器](#容器)

* [图形程序设计](#图形程序设计)
* [Swing界面组件](#Swing界面组件)
* [事件处理](#事件处理)
* [高级Swing 界面组件](#高级Swing-界面组件)
* [高级AWT](#高级AWT)
* [部署JAVA应用程序](#部署JAVA应用程序)



# Java基础视频

 * [尚硅谷_康师傅2019新版—30天搞定Java核心技术](https://www.bilibili.com/video/av47426548?from=search&seid=17342892563183546999)
 * [高淇老师JAVA300集](https://www.bilibili.com/video/av30023103/?spm_id_from=333.788.videocard.0)

# 有用的参考
 * [Java基础知识面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104390612)
 * [Java基础知识大全](https://blog.csdn.net/xingkonglfs/article/details/54695622)

 * [JAVA 笔记（四） RTTI - 运行时类型检查](https://blog.csdn.net/ghost_Programmer/article/details/51532064)

 * [Java基础知识总结（绝对经典）](https://blog.csdn.net/xyzopq100/article/details/50683899)


 * [从操作系统内核看Java非阻塞IO事件检测](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483667&idx=1&sn=316f9b08e43bb11c1b8c2cb2f1eec548&chksm=a69dac2d91ea253b4c1e62ab13b385bd4987f2bdabb1db2cad6c3f40d0b456b4f1951f637a5d&scene=21#wechat_redirect)
 * [如何让你的传输更安全——NIO模式和BIO模式实现SSL协议通信](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483868&idx=1&sn=968e2ac4a52dbdfca198c91cc9c3d925&chksm=a69dace291ea25f4337952ae9b3590ffc6dc3554be07a1859ebb6d31cd6218ca273ec24f9309&scene=21#wechat_redirect)
 
 
 * [图解 Java 中的数据结构及原理，傻瓜也能看懂](https://mp.weixin.qq.com/s/2t_XO3G1v1jx7q0-9YU0YA)





