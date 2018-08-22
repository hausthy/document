# supervisor安装

## 1.安装

    sudo apt-get install supervisor

## 2.常用配置

    [program:HelloWebApp]
    command=dotnet HelloWebApp.dll  #要执行的命令
    directory=/home/yxd/Workspace/publish #命令执行的目录
    environment=ASPNETCORE__ENVIRONMENT=Production #环境变量
    user=www-data  #进程执行的用户身份
    stopsignal=INT
    autostart=true #是否自动启动
    autorestart=true #是否自动重启
    startsecs=1 #自动重启间隔
    stderr_logfile=/var/log/HelloWebApp.err.log #标准错误日志
    stdout_logfile=/var/log/HelloWebApp.out.log #标准输出日志

## 3.停止和启动

    sudo service supervisor stop
    sudo service supervisor start

## 4.更新新的配置到supervisord

    supervisorctl update

## 5.重新启动配置中的所有程序

    supervisorctl reload

## 6.启动某个进程(program_name=你配置中写的程序名称)

    supervisorctl start program_name

## 7.查看正在守候的进程

    supervisorctl

## 8.停止某一进程 (program_name=你配置中写的程序名称)

    supervisorctl restart program_name

## 9.停止全部进程

    supervisorctl stop all