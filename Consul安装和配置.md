# Consul安装和配置

获取consul
wget <https://releases.hashicorp.com/consul/1.2.0/consul_1.2.0_linux_amd64.zip>

解压consul
unzip consul_1.2.0_linux_amd64.zip -d /etc/consul

软连接到环境变量
sudo ln -s /etc/consul/consul /bin/consul

启动consul服务端
consul agent -server -ui -bootstrap-expect=1 -data-dir=/data/consul -node=consul-2 -client=0.0.0.0 -bind=104.223.65.91 -datacenter=dc1

启动consul客户端
consul agent -ui -data-dir=/data/consul -node=consul-1 -client=0.0.0.0 -bind=107.172.243.239 -datacenter=dc1 -join=104.223.65.91