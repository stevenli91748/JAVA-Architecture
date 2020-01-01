nginx启动方法

 /usr/local/nginx/sbin/ ./nginx

停止方法

 /usr/local/nginx/sbin/ ./nginx  -s stop

重启方法(平滑重启)：

/usr/local/nginx/sbin/ ./nginx -s reload

检查配置文件是否正确(修改配置文件后，先检查一下)

/usr/local/nginx/sbin/ ./nginx -t

显示配置参数：

/usr/local/nginx/sbin/ ./nginx -V
