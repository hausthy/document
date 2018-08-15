# Ubuntu下安装与卸载Nginx

## Ubuntu下安装Nginx

    sudo apt-get update
    sudo apt-get install nginx

## Ubuntu下卸载

    sudo apt-get remove nginx nginx-common # 卸载删除除了配置文件以外的所有文件    
    sudo apt-get purge nginx nginx-common # 卸载所有东东，包括删除配置文件    
    sudo apt-get autoremove # 在上面命令结束后执行，主要是卸载删除Nginx的不再被使用的依赖包    
    sudo apt-get remove nginx-full nginx-common #卸载删除两个主要的包