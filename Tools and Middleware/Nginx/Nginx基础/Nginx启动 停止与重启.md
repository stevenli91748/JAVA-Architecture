nginx启动方法

 /application/nginx/sbin/nginx

停止方法

 /application/nginx/sbin/nginx  -s stop

重启方法(平滑重启)：

 /application/nginx/sbin/nginx -s reload

检查配置文件是否正确(修改配置文件后，先检查一下)

 /application/nginx/sbin/nginx -t

显示配置参数：

 /application/nginx/sbin/nginx -V
