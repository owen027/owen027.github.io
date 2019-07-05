---
title: Nginx 学习
date: 2019-07-05 11:12:21
categorise:
- nginx
tags:
- nginx
---
# 什么是 Nginx

  Nginx 是一款轻量级高性能的web 和 反向代理服务器，类似于`Apache`，也是一个 IMAP/POP3/SMTP （电子邮件）代理服务器。，由俄罗斯程序设计师 `Igor Sysoev`开发；
在高连接并发的情况下，Nginx能够支持高达 50000 个并发连接数的响应，是 Apache 服务器不错的替代品。

**nginx做为HTTP服务器，有以下几项基本特性：**
        1. 处理静态文件，索引文件以及自动索引；打开文件描述符缓冲．
        2. FastCGI和反向代理加速(无缓存)，简单的负载均衡和容错．
        3. 模块化的结构。包括gzipping, byte ranges, chunked responses,以及 SSI-filter等filter。如果由FastCGI或其它代理服务器处理单页中存在的多个SSI，则这项处理可以并行运行，而不需要相互等待。
        4. 支持SSL 和 TLSSNI．

Nginx支持热部署。它的启动特别容易, 并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够在不间断服务的情况下，对软件版本进行进行升级。
nginx是异步的，多个连接（万级别）可以对应一个进程。 apache是同步多进程模型，一个连接对应一个进程；
**nginx的优势是处理静态请求，cpu内存使用率低，apache适合处理动态请求，所以现在一般前端用nginx作为反向代理抗住压力，apache 作为后端处理动态请求。**

**正向代理：** 服务器代理客户端向服务端发送请求，并将数据分发给客户端，服务端无法知道客户端的信息
**反向代理：** 服务器代理服务端接收客户端的请求，并分发给服务器（分布式部署），反向代理隐藏了服务器的信息。
**负载均衡：**  客户端发送的、Nginx反向代理服务器接收到的请求数量，就是负载量。请求数量按照一定的规则进行分发到不同的服务器处理的规则，就是一种均衡规则，即代理服务器将请求按一定的规则分发的过程就是负载均衡。

## 安装

**下载**
[点击进入官网下载](http://nginx.org/en/download.html) Windows版本,解压至 C盘

**启动**
双击 `nginx.exe` 或者 打开 CMD 进入 nginx 目录 输入 `start nginx`,如果启用防火墙，允许访问即可

##  常用命令
须使用CMD 进入跟目录才能使用 nginx 
```cmd
nginx -h //查看帮助
nginx -v // 查看版本
nginx -s stop  //停用
nginx -s reload //重载配置，重启进程
nginx -s reopen //重启日志
```

## 代理配置

```perl

#user  nobody;
#开启进程数 <=CPU数   
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#进程号保存文件  
#pid        logs/nginx.pid;


events {
    #每个进程最大连接数（最大连接=连接数x进程数）每个worker允许同时产生多少个链接，默认1024 
    worker_connections  1024; 
}


http {
    #文件扩展名与文件类型映射表  
    include       mime.types;
    #默认文件类型  
    default_type  application/octet-stream;

    #日志文件输出格式 这个位置相对于全局设置  
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #请求日志保存位置
    #access_log  logs/access.log  main;
    
    #打开发送文件 
    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;

    #连接超时时间  
    keepalive_timeout  65;

    #打开gzip压缩  
    #gzip  on;

    server {
        #监听端口，默认是80端口  
        listen       80;
        #监听域名 
        server_name  localhost;

        #charset koi8-r;
        #nginx访问日志放在logs/host.access.log下，并且使用main格式（可以自定义格式）
        #access_log  logs/host.access.log  main;

        #如果没有location更明确的匹配访问路径的话，访问请求都会被该location处理
        location / {
            #root指定nginx的根目录为/usr/local/nginx/html  
            root   html;

            #默认访问文件，欢迎页先去html目录下找index.html，如果找不到再去找index.htm
            index  index.html index.htm;
        }

        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        #错误页面及其返回地址，错误码为500、502、503、504都会返回50.html错误页面
        error_page   500 502 503 504  /50x.html;
        #location后面是"="的话，说明是精确匹配  
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}


```

要监听多个端口和域名 可配置多个 `server`, 