# Ubuntu下安装与卸载Ngrok

## 理解 ngrok 的工作流程

    在公司、家庭的两台电脑上分别有 8080 和 8888 两个端口运行了某个服务，我们想让它们能分别通过测试域名company.ngrok.w-more.club和home.ngrok.w-more.club被访问到。这就需要ngrok服务来做“请求转发”了。

    添加域名解析

    假设准备提供ngrok服务的域名，以及两个测试域名的根域名为 ngrok.w-more.club。

    运行 ngrok 服务端的服务器 IP 地址为 1.2.3.4 添加一条A记录，主机 ngrok， 记录值 1.2.3.4

    添加一条A记录，主机 *.ngrok， 记录值 1.2.3.4

## Ubuntu环境搭建

### 安装golang

    apt-get install golang 安装可能安装到低版本，所以采用手动配置方式。 从 https://golang.org/dl/ 或 https://studygolang.com/dl 下载最新的发布版本go1.10即go1.10.linux-amd64.tar.gz；

    也可使用wget https://storage.googleapis.com/golang/go1.8.linux-amd64.tar.gz方式进行下载

    编辑 /etc/profile 文件添加环境变量：

    export GOROOT=/data/go

    export GOPATH=$GOROOT/bin

    export PATH=$PATH:$GOPATH

    source /etc/profile

### 安装 ngrok 服务端

    apt-get install build-essential golang mercurial git

    # 看看是不是小于等于 1.2.1
    go version
    # 卸载
    sudo apt-get purge golang*
    #下载最新版并解压 https://golang.org/dl/
    wget https://storage.googleapis.com/golang/go1.7.1.linux-amd64.tar.gz
    tar -C /usr/local -xzf go1.7.1.linux-amd64.tar.gz
    #创建目录
    mkdir ~/.go
    # 设置环境变量
    vi ~/.profile
    export GOROOT=/usr/local/go
    export GOPATH=~/.go
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
    source .profile
    # 升级
    sudo update-alternatives --install "/usr/bin/go" "go" "/usr/local/go/bin/go" 0
    sudo update-alternatives --set go /usr/local/go/bin/go
    go version

### 拉取ngrok

    git clone https://github.com/tutumcloud/ngrok.git ngrok
    cd ngrok
    #生成并替换源码里默认的证书，注意域名要修改为你自己的，这里是一个虚拟的测试域名
    NGROK_DOMAIN="ngrok.w-more.club"
    openssl genrsa -out base.key 2048
    openssl req -new -x509 -nodes -key base.key -days 10000 -subj "/CN=$NGROK_DOMAIN" -out base.pem
    openssl genrsa -out server.key 2048
    openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
    openssl x509 -req -in server.csr -CA base.pem -CAkey base.key -CAcreateserial -days 10000 -out server.crt
    cp base.pem assets/client/tls/ngrokroot.crt
    #开始编译，服务端客户端会基于证书来加密通讯，保证了安全性
    sudo make release-server release-client
    # 如果一切正常，ngrok/bin 目录下应该有 ngrok、ngrokd 两个可执行文件，ngrokd 是服务端文件，ngrok 是 Linux 的客户端
    ls -al ./bin

### 生成不同的客户端(windows、macOS 等)

    完成安装后，默认只会生成 Linux 版本的客户端，而我们如果需要 Mac、Windows 的客户端，就要手动生成。

    cd ngrok
    GOOS=linux GOARCH=amd64 make release-server release-client
    GOOS=windows GOARCH=amd64 make release-server release-client
    GOOS=darwin GOARCH=amd64 make release-server release-client
    GOOS=linux GOARCH=arm make release-server release-client

    生成的客户端文件放在 bin目录下，按操作系统代号分目录存储，例如ngrok/bin/darwin_amd64/ngrok是运行在 macOS 上面的。稍后我们会将它们复制到不同的操作系统下来启动他们。

### 启动服务端

    在服务器上运行下面的命令启动ngrok服务端
    ./bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="ngrok.w-more.club" -httpAddr=":8081" -httpsAddr=":8082"

    注意，这里httpAddr和httpsAddr是 ngrok服务 转发 http 和 https 请求的端口，为了避免和 Nginx/Apache 等的 80 端口冲突，使用了8081和8082（后面会有步骤进行 80 端口转发，这里先不表）。

    默认还会启动一个4443端口，用于跟活动的客户端进行通讯，如果需要更换端口，使用 -tunnelAddr=":xxx"参数

    现在你可以在浏览器里访问 http://ngrok.w-more.club:8081了，如果有一行提示，表示 ngrok的服务端已经运行起来了
    Tunnel ngrok.w-more.club:8081 not found

    然后再访问http://company.ngrok.w-more.club:8081，如果有下面的提示，表示 CNAME 里面设置的域名别名也已经成功了。
    Tunnel company.ngrok.w-more.club:8081 not found

### 启动客户端

    配置连接参数

    根据你的操作系统不同，将上面生成的不同客户端下载到本地。例如我的本地机器是 macOS，把服务器上 /root/ngrok/bin/darwin_amd64/ngrok下载后另存为 ~/Dowonload/ngrok。

    给这个ngork客户端文件添加执行权限，然后创建一个配置文件，指定客户端等下要连接的ngrok的服务端参数

    cd ~/Download
    chmod +x ngrok
    vi ngrok.cfg
    # 填写如下信息，server_addr 指定了服务端的域名和与客户端通信的端口
    server_addr: ngrok.w-more.club:4443
    trust_host_root_certs: false

    运行客户端

    然后运行下面的命令启动客户端。-subdomain参数指定一个子域名(这里为 test)，最后一位数字是你本地要暴露的端口。-proto是协议，这里是http（ngrok 还支持 tcp协议，原理和方法类似）。

    ./ngrok -subdomain test -proto=http -config=ngrok.cfg 80
    # 如果连接正常，会有提示
    ngrok                                                        (Ctrl+C to quit)
    Tunnel Status                 online
    Version                       1.7/1.7
    Forwarding                    http://test.ngrok.w-more.club:8081 -> 127.0.0.1:80
    Web Interface                 127.0.0.1:4040
    # Conn                        5
    Avg Conn Time                 192.70ms

    windows 也是类似的启动方法

### 数据监控面板

    通过打开 127.0.0.1:4040这个监控面板里看到转发过来的 request/response 等数据。

### 隐藏 URL 里的 8081 端口，直接使用 80 端口

    因为我们运行 ngrok 服务端的时候，为了避免和 nginx/apache 的 80 端口冲突，指定了 ngrok 转发 http 请求的端口为 8081，那么 ngrok 的客户端也必须使用 8081 端口才能接收数据。每次测试的时候，还要在 URL 后面加上:8081，这太繁琐了，那么如何使用 80 端口来访问呢？

    通过使用 web 服务的反向代理可以实现，以 nginx 为例。

    为服务端使用的域名的 8081 端口添加反向代理

    server {
            listen 80;
            server_name ngrok.mydomain.com;
            location / {
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header Host $http_host:8081;
                    proxy_set_header X-Nginx-Proxy true;
                    proxy_set_header Connection "";
                    proxy_pass http://127.0.0.1:8081;
            }
    }
