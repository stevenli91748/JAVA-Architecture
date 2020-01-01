# 防盗链

设置静态资源只能192.168.189.132访问，其他ip访问返回404

 location ~ .*\.(js|css|png|svg|ico|jpg)$ {
     
         #防盗链
         #设置静态资源只能192.168.189.132访问，其他ip访问返回404
         valid_referers none blocked 192.168.189.132;
         if ($invalid_referer) {
             return 404;
         }
         root static-resource;     // static-resource是目录路径
         expires 1d;
    }












# 有用的参考

* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)
