---
title: nginx 常用配置
---

```
server
{
    listen 80;
    listen 443 ssl http2;
    server_name next.changfangwang.com www.changfangwang.com changfangwang.com *.changfangwang.com;
    index index.html index.htm default.htm default.html;
    #root /www/wwwroot/plantpc;

    #SSL-START SSL相关配置
    #error_page 404/404.html;
    ssl_certificate    /www/server/panel/vhost/cert/plantpc/fullchain.pem;
    ssl_certificate_key    /www/server/panel/vhost/cert/plantpc/privkey.pem;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    add_header Strict-Transport-Security "max-age=31536000";
    error_page 497  https://$host$request_uri;
    #SSL-END

    #ERROR-PAGE-START  错误页相关配置
    #error_page 404 /404.html;
    #error_page 502 /502.html;
    #ERROR-PAGE-END


    #REWRITE-START 伪静态相关配置
    include /www/server/panel/vhost/rewrite/node_plantpc.conf;
    #REWRITE-END

    #禁止访问的文件或目录
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md|package.json|package-lock.json|\.env) {
        return 404;
    }

    #一键申请SSL证书验证目录相关设置
    location /.well-known/ {
        root  /www/wwwroot/plantpc;
    }

    #禁止在证书验证目录放入敏感文件
    if ( $uri ~ "^/\.well-known/.*\.(php|jsp|py|js|css|lua|ts|go|zip|tar\.gz|rar|7z|sql|bak)$" ) {
        return 403;
    }


    # HTTP反向代理相关配置开始 >>>
    location ~ /purge(/.*) {
        proxy_cache_purge cache_one $host$request_uri$is_args$args;
    }

    location / {
        proxy_pass http://127.0.0.1:3010;
        proxy_set_header Host $host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header REMOTE-HOST $remote_addr;
        add_header X-Cache $upstream_cache_status;
        proxy_set_header X-Host $host:$server_port;
        proxy_set_header X-Scheme $scheme;
        proxy_connect_timeout 30s;
        proxy_read_timeout 86400s;
        proxy_send_timeout 30s;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    location /pc{
        # 将客户端的 Host 和 IP 信息一并转发到对应节点
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # 执行代理访问真实服务器
        proxy_pass http://localhost:9501;
    }
    location /ws/location/v1/ip {
        set $args "$args&key=3BDBZ-BUALW-MPBRB-RZ7NG-MUBX6-U4F2W";
        set $args "$args&ip=$remote_addr";
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass https://apis.map.qq.com;
    }
    location /loc/?key=3BDBZ-BUALW-MPBRB-RZ7NG-MUBX6-U4F2W {
        set $args "$args&key=3BDBZ-BUALW-MPBRB-RZ7NG-MUBX6-U4F2W";
        set $args "$args&ip=$remote_addr";
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass https://apis.map.qq.com;
    }

    location /article {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/article;
    }

    location /nanchang {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/nanchang;
    }
    location /guangzhou {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/guangzhou;
    }
    location /dongguan {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/dongguan;
    }
    location /zhuhai {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/zhuhai;
    }
    location /site {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/site;
    }

    location /tj {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/tj;
    }
    location /cd {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/cd;
    }
    location /jn {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/jn;
    }
    location /wx {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/wx;
    }
    location /cs {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/cs;
    }
    location /wh {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/wh;
    }
    location /sh {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/sh;
    }
    location /sjz {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/sjz;
    }
    location /sz {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/sz;
    }
    location /jm {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/jm;
    }

    location /fs {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/fs;
    }

    location /xm {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/xm;
    }


    location /bj {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/bj;
    }
    location /ls {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://49.232.78.187:8089/ls;
    }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|xml|txt)$
    {
    	proxy_set_header Host $http_host;
    	proxy_set_header X-Real-IP $remote_addr;
    	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    	proxy_pass http://localhost:9501;
    }




location /websocket{
  proxy_pass http://localhost:9502;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
if ($http_host ~ "^changfangwang.com$")
{
	rewrite ^(.*) https://www.changfangwang.com$1 permanent;
}

    #判断是否是pc端
		if ($http_user_agent ~* "(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox
			|netfront|operam(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino") {
			rewrite ^/register(.*) https://m.changfangwang.com/$1 last;
			rewrite ^/(.*) https://m.changfangwang.com/$1 redirect;
		}

    # HTTP反向代理相关配置结束 <<<

    access_log  /www/wwwlogs/plantpc.log;
    error_log  /www/wwwlogs/plantpc.error.log;
}

```
