# Nginx配置SSL

## 证书申请

腾讯云购买一个免费得证书：<https://console.qcloud.com/ssl>

## nginx.conf配置

    ##这里是将http默认的80端口重定向到https
    server {
        listen       80;
        server_name  www.tencent.com;
        rewrite ^ https://$http_host$request_uri? permanent;
    }

    ##这里是将默认请求https的443端口拦截
    ##并请求转发到http://127.0.0.1:8888/
    server {
        listen 443;
        server_name www.tencent.com;
        ssl on;
        ssl_certificate 1_tencent.com_bundle.crt;
        ssl_certificate_key 2_tencent.com_.key;
        ssl_session_timeout 5m;
        location / {
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-NginX-Proxy true;
            #你的项目端口号
            proxy_pass http://127.0.0.1:8888/;
            proxy_redirect off;
        }
    }

## 重新加载Nginx配置

    nginx -s reload