---
title: "cpcexplorer配置"
date: 2018-11-07 20:40
categories: django
tags: cpc_explorer
---

## 记录下配置cpcexplorer的坑

​	倒着写， 能记起来慢慢补，首先是今天部署explorer时发现dwebsocket配合uwsgi死活用不了，开始以为是异步问题，配了半天没成功，看了很多文档发现uwsgi自带websocket的实现， 不过好像是阻塞收发websocket，没有实现异步，但是总算是实现了nginx+uwsgi+websocket，配置nginx如下：

```
server {
    listen 80;
    listen [::]:80;

    server_name _;
    
    location / {
        include     /etc/nginx/uwsgi_params;
        uwsgi_pass 127.0.0.1:8000;
    }
    location /static{
        alias /srv/www/explorer/static;
	}
    location ~ /wshandler/ {		
        include     /etc/nginx/uwsgi_params;
        uwsgi_pass     127.0.0.1:8000;
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

uwsgi配置

```
[uwsgi]
#使用nginx连接时使用
socket=127.0.0.1:8000
#直接做web服务器使用
#http=0.0.0.0:8000
#项目
chdir=/home/ubuntu/explorer/cpchain_explorer
#chdir=/home/cpc/PycharmProjects/cpchain_explorer
#项目中wsgi.py文件的目录，相对于项目目录
wsgi-file=cpc_explorer/wsgi.py
processes=4
threads=2
master=True
pidfile=uwsgi.pid
daemonize=uwsgi.log
http-websocket=True  # 开启websocket
#static-map = /static=static  #测试时读取static
```

​	