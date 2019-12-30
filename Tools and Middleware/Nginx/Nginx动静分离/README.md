# 动静分离

  增加配置
        只要是以jpg或者gif结尾的，去配置的目录下查找
        
        location ~ .*\.(jpg|gif)$ {
            root /usr/local/images;
        }
        
    http://localhost/a.jpg



# 视频

* [Nginx动静分离](https://www.bilibili.com/video/av68136734?p=4)



# 有用的参考

* [Nginx实现动静分离](https://www.jianshu.com/p/037a088eca4f)
* [nginx 配置http服务器实现动静分离（一）](https://my.oschina.net/u/4034639/blog/3074294)
* [Nginx动静分离实现负载均衡](https://cloud.tencent.com/developer/news/264359)
