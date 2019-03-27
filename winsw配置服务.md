# winsw配置服务

## 1.下载

  首先要下载winsw。它是一个单个的可执行文件，我们到Github release这里就可以下载winsw了。

  一般来说当然是下载最新的。winsw可以运行在.NET2和.NET4两个版本上，当然如果使用Win10等比较新的系统，最好下载更新版本的.NET。

  下载完之后最好把文件改成一个有特定意义的名字，例如nginx-service.exe这样的，方便后面输入跟配置文件对应上。

## 2.编写配置文件

  我们需要编写一个和程序同名的XML文件作为winsw的配置文件。文件大体上长这样，这是我们配置Nginx服务的例子。

    <service>
    <id>Nginx</id>
      <name>Nginx</name>
      <description>nginx service</description>
      <executable>G:\nginx-1.14.0\nginx.exe</executable>
      <logpath>G:\nginx-1.14.0\logs\</logpath>
      <logmode>roll</logmode>
      <depend></depend>
      <startargument>-p G:\nginx-1.14.0</startargument>
      <stopargument>-p G:\nginx-1.14.0 -s stop</stopargument>
    </service>
  其中name为 服务名，executable为可执行程序路径，logpath为程序运行日志路径，其他大家看到XML的标签名应该就能知道是做什么的了，这里我就不再描述了。

## 3.注册服务

  编写好配置文件之后，记得把配置文件和可执行文件放在一起，这样winsw才能正确识别配置文件。一般建议将这两个文件一起放到要注册的应用程序的目录下。

  然后我们打开一个管理员权限的命令提示符或Powershell窗口，然后输入下面的命令 nginx-service.exe install

## 4.注：

  nginx-service.exe uninstall命令可删除对应的系统服务

  nginx-service.exe stop命令可停止对应的系统服务

  nginx-service.exe start命令可启动对应的系统服务

## 5.查看服务是否安装成功

  在计算机管理 -> 服务中寻找刚刚命名的服务，如果能找到就是注册成功

  如服务为未运行状态，可在此启动服务，或设置为自动启动