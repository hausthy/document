# Consul安装和配置

获取consul
wget <https://releases.hashicorp.com/consul/1.2.0/consul_1.2.0_linux_amd64.zip>

解压consul
unzip consul_1.2.0_linux_amd64.zip -d /etc/consul

软连接到环境变量
sudo ln -s /etc/consul/consul /bin/consul

consul的配置信息可以在文档-配置查看，其中部分选项如下：
    -advertise：通知展现地址用来改变我们给集群中的其他节点展现的地址，一般情况下-bind地址就是展现地址<br>
    -bootstrap：用来控制一个server是否在bootstrap模式，在一个datacenter中只能有一个server处于bootstrap模式，当一个server处于bootstrap模式时，可以自己选举为raft leader。<br>
    -bootstrap-expect：在一个datacenter中期望提供的server节点数目，当该值提供的时候，consul一直等到达到指定sever数目的时候才会引导整个集群，该标记不能和bootstrap公用<br>
    -bind：该地址用来在集群内部的通讯，集群内的所有节点到地址都必须是可达的，默认是0.0.0.0<br>
    -client：consul绑定在哪个client地址上，这个地址提供HTTP、DNS、RPC等服务，默认是127.0.0.1<br>
    -config-file：明确的指定要加载哪个配置文件<br>
    -config-dir：配置文件目录，里面所有以.json结尾的文件都会被加载<br>
    -data-dir：提供一个目录用来存放agent的状态，所有的agent允许都需要该目录，该目录必须是稳定的，系统重启后都继续存在
    -dc：该标记控制agent允许的datacenter的名称，默认是dc1
    -encrypt：指定secret key，使consul在通讯时进行加密，key可以通过consul keygen生成，同一个集群中的节点必须使用相同的key<br>
    -join：加入一个已经启动的agent的ip地址，可以多次指定多个agent的地址。如果consul不能加入任何指定的地址中，则agent会启动失败，默认agent启动时不会加入任何节点。<br>
    -retry-join：和join类似，但是允许你在第一次失败后进行尝试。<br>
    -retry-interval：两次join之间的时间间隔，默认是30s<br>
    -retry-max：尝试重复join的次数，默认是0，也就是无限次尝试<br>
    -log-level：consul agent启动后显示的日志信息级别。默认是info，可选：trace、debug、info、warn、err。<br>
    -node：节点在集群中的名称，在一个集群中必须是唯一的，默认是该节点的主机名<br>
    -protocol：consul使用的协议版本<br>
    -rejoin：使consul忽略先前的离开，在再次启动后仍旧尝试加入集群中。<br>
    -server：定义agent运行在server模式，每个集群至少有一个server，建议每个集群的server不要超过5个<br>
    -syslog：开启系统日志功能，只在linux/osx上生效<br>
    -pid-file:提供一个路径来存放pid文件，可以使用该文件进行SIGINT/SIGHUP(关闭/更新)agent

启动consul服务端
consul agent -server -ui -bootstrap-expect=1 -data-dir=/data/consul -node=consul-2 -client=0.0.0.0 -bind=104.223.65.91 -datacenter=dc1

启动consul客户端
consul agent -ui -data-dir=/data/consul -node=consul-1 -client=0.0.0.0 -bind=107.172.243.239 -datacenter=dc1 -join=104.223.65.91