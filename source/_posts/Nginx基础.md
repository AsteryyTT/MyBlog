---
title: Nginx基础
date: 2023-05-16 11:18:57
tags: 中间件
categories: 中间件
cover: /img/images/nginx.jpg
---

# Nginx基础

## Nginx特点
反向代理
负载均衡
动静分离

## Nginx安装
1.官网下载Nginx安装包
> cd /opt/
> wget http://nginx.org/download/nginx-x.xx.x.tar.gz

2.解压缩
> tar -xzvf nginx-x.xx.x.tar.gz

3.进入Nginx目录
> cd nginx-x.xx.x

4.安装
> ./configure --help	查看安装所需要的命令
> ./configure --prefix=PATH 安装的路径
> 安装过程中可能缺少某些库，例如pcre、zlib、openssl

5.编译
> make && make install

如果没有设置安装路径默认路径是 /usr/local/nginx
> cd /usr/local/nginx/sbin
> nginx -v 查看nginx版本

6.启动Nginx服务
> cd /usr/local/nginx/sbin
> ./nginx

7.查看服务是否启动
> ps -ef|grep nginx





所需依赖

>pcre   zlib 






## 常用命令
> 1. cd /usr/local/nginx/sbin
> 2. ./nginx				启动服务
> 3. ./nginx -s stop		强制停止
> 4. ./nginx -s quit		安全退出
> 5. ./nginx -s reload		重新加载配置文件
> 6. ps aux|grep nginx		查看nginx进程

注意：如果链接不上，检查服务器安全组是否开放端口，或者服务器防火墙是否开放端口
> service firewalld start		开启防火墙
> service firewalld restart		重启防火墙
> service firewalld stop		关闭防火墙
> firewall-cmd --list-all		查看防火墙规则
> firewall-cmd --query-port=端口号/tcp		查询端口号是否开放
> firewall-cmd --permanent --add-port=端口号/tcp	开放XX端口
> firewall-cmd --permanent --remove-port=端口号/tcp 移除XX端口号
> firewall-cmd --reload		重启防火墙(修改配置后重启防火墙)



Nginx的配置文件

cd /usrl/local/nginx/conf



全局块

#user  nobody;
worker_processes  1;并发处理量，值越大并发处理越高

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;





events块

Nginx服务器与用户的网络连接

events {
    worker_connections  1024;最大连接数
}



http块

代理、缓存、日志绝大数功能和第三方模块都在这里配置

http全局块：文件引入、MIME-TYPE定义，日志自定义，连接超时时间，单连接请求数上限等

```nginx
http {
    include       mime.types;
    default_type  application/octet-stream;
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #access_log  logs/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    #keepalive_timeout  0;
    keepalive_timeout  65;
    #gzip  on;
    server {
        #监听端口
        listen       80;
        #主机名称
        server_name  localhost;
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        
        location / {
            root   html;
            index  index.html index.htm;
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
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



示例1.反向代理

>1. 实现效果：打开浏览器地址栏输入www.123.com,跳转到linux系统tomcat主页面中
>2. 准备工作
>   1. 安装tomcat服务器使用默认端口



示例2.负载均衡

分配服务器策略

1. 默认轮询

2. weight权重

   默认1，权重越高被分配的请求就越多

3. ip_hash

   每个请求按照ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题





示例3.动静分离
