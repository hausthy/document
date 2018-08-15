    user www-data;
    worker_processes auto;
    pid /run/nginx.pid;
    include /etc/nginx/modules-enabled/*.conf;

    events {
        worker_connections 768;
        # multi_accept on;
    }

    http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;
        gzip_disable "msie6";

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
                
        # 设定负载均衡gateway服务器列表 
        upstream gateway  { 
                server   104.223.65.91:18500;  
        }

        # gateway虚拟主机配置
        server {
            listen   80;
            server_name  gateway.hausthy.cn;

            #对 / 所有做负载均衡+反向代理
            location / {
                proxy_pass  http://gateway;  
                proxy_redirect off;
                # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
                proxy_set_header  Host  $host;
                proxy_set_header  X-Real-IP  $remote_addr;  
                proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
                proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
            }
        }

        # 设定负载均衡consul服务器列表 
        upstream consul  { 
                server   104.223.65.91:8500;  
        }

        # consul虚拟主机配置
        server {
            listen   80;
            server_name  consul.hausthy.cn;

            #对 / 所有做负载均衡+反向代理
            location / {
                proxy_pass  http://consul;  
                proxy_redirect off;
                # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
                proxy_set_header  Host  $host;
                proxy_set_header  X-Real-IP  $remote_addr;  
                proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
                proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
            }
        }

        ##这里是将默认请求https的443端口拦截
        ##并请求转发到http://127.0.0.1:8888/
        server {
            listen 443;
            server_name gateway.hausthy.cn;
            ssl on;
            ssl_certificate /etc/letsencrypt/live/hausthy.cn/fullchain.pem;
            ssl_certificate_key /etc/letsencrypt/live/hausthy.cn/privkey.pem;
            ssl_session_timeout 5m;
            location / {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-NginX-Proxy true;            
                proxy_pass http://gateway;
                proxy_redirect off;
            }
        }
        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
    }