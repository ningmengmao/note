# Nginx

#### 1. 概念

> * 反向代理
>
>   > ​       反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为Web加速，即使用反向代理作为Web服务器的前置机来降低网络和服务器的负载，提高访问效率。
>
> * 负载均衡
>
>   > 
>
> * 动静分离
>
>   > 1

#### 2. 配置文件

> * 全局块
>
>   > 开始行到events之间
>   >
>   > worker_processes 1;  并发值
>
> * events
>
>   > worker_connections 1024;  最大连接数
>
> * http
>
>   > 包含http全局块和server块
>   >
>   > 1. 全局server块
>   >
>   >    > ```
>   >    >     include       mime.types;
>   >    >     default_type  application/octet-stream;
>   >    > 
>   >    >     #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>   >    >     #                  '$status $body_bytes_sent "$http_referer" '
>   >    >     #                  '"$http_user_agent" "$http_x_forwarded_for"';
>   >    > 
>   >    >     #access_log  logs/access.log  main;
>   >    > 
>   >    >     sendfile        on;
>   >    >     #tcp_nopush     on;
>   >    > 
>   >    >     #keepalive_timeout  0;
>   >    >     keepalive_timeout  65;
>   >    > 
>   >    >     #gzip  on;
>   >    > ```
>   >
>   > 2. location块
>   >
>   >    > ```
>   >    >  server {
>   >    >      listen       80;
>   >    >      server_name  localhost;
>   >    > 
>   >    >      charset utf-8;
>   >    > 
>   >    >      #access_log  logs/host.access.log  main;
>   >    > 
>   >    >      location / {
>   >    >          # 文件目录, 默认为nginx的html目录
>   >    >          root   html;   
>   >    >          # 将80端口映射到8080端口
>   >    >          proxy_pass http://localhost:8080
>   >    >          index  index.html index.htm;
>   >    >      }
>   >    > 
>   >    >      #error_page  404              /404.html;
>   >    > 
>   >    >      # redirect server error pages to the static page /50x.html
>   >    >      #
>   >    >      error_page   500 502 503 504  /50x.html;
>   >    >      location = /50x.html {
>   >    >          root   html;
>   >    >      }
>   >    > ```
>   >    >
>   >    > URL匹配规则![image-20191027213547146](/home/ningmengmao/.config/Typora/typora-user-images/image-20191027213547146.png)
>   
>   #### 3. 命令
>   
>   > ```shell
>   > # 配置文件测试
>   > nginx -c nginx.conf -t  
>   > ```
>   >
>   > 