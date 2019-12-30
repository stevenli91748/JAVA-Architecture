
# 手机站点和PC站点的分离 

location / {
            if ($mobile_rewrite = 0) {
                proxy_pass http://localhost:8082;
            }
            proxy_pass   http://demo;
            root   html;
            index  index.html index.htm;
         }
         
站点增加则按照如下配置         

    upstream pc{
          ip_hash;
          server 192.168.2.138:8080;
          server 192.168.2.138:8081;
    }
    
    upstream mobile{
         ip_hash;
         server 192.168.2.138:8082;
         server 192.168.2.138:8083;
    }
 
    location / {
            if ($mobile_rewrite = 0) {
                proxy_pass http://mobile;
            }
            proxy_pass   http://pc;
            root   html;
            index  index.html index.htm;

    }         


