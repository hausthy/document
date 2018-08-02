# 安装Mysql

## 依次输入以下命令，安装数据库。期间出现root用户的密码设置，请认真输入并牢记，这是你以后用来登录mysql的密码。

    sudo apt-get install mysql-server</br>
    sudo apt-get install mysql-client</br>
    sudo apt-get install libmysqlclient-dev</br>

## 验证是否安装成功：登录mysql

    mysql -u root –p

## 配置mysql端口

    netstat -an|grep 3306</br>
    可以看到 mysql默认监听127.0.0.1：3306端口，我们需要把它修改掉。</br>
    输入 vi /etc/mysql/mysql.conf.d/mysqld.cnf</br>
    进入mysql配置文档，把bind-adress行注释掉</br>
    输入 :wq 保存，退出。

## 重启mysql服务使配置生效：

    service mysql restart

## 登陆MySQL

    //先输入密码登陆</br>
    mysql -u root -p</br>
    //然后选择数据库</br>
    mysql>use mysql；</br>
    //选择root的账户host改为%，上面2.3中已改地址，这一步不确定是否必要</br>
    mysql> update user set host='%' where user='root';</br>
    //授权</br>
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '填写root的密码' WITH GRANT OPTION;</br>
    //更新权限</br>
    FLUSH PRIVILEGES;</br>
    //查询数据库用户</br>
    mysql>SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;</br>
    //退出mysql重启mysql</br>
    service mysql restart</br>