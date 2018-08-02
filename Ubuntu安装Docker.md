# Docker安装

## 服务器下载Docker

1.更新apt软件包索引。</br>
$ sudo apt-get update</br>
2.安装最新版本的Docker CE，或转到下一步安装特定版本。任何现有的Docker安装都会被替换。</br>
$ sudo apt-get install docker-ce</br>

在这里我遇到了错误E: Unable to locate package docker-ce
有人在stack overflow给出答案

sudo apt-get install apt-transport-https ca-certificates curl software-properties-common</br>
curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> | sudo apt-key add -</br>
sudo add-apt-repository "deb [arch=amd64] <https://download.docker.com/linux/ubuntu xenial stable>"</br>
sudo apt-get update</br>
sudo apt-get install docker-ce

## 检测安装成功

root@VM-42-54-ubuntu:/home/ubuntu# docker version</br>
Client:</br>
 Version:   17.12.1-ce</br>
 API version:   1.35</br>
 Go version:    go1.9.4</br>
 Git commit:    7390fc6</br>
 Built: Tue Feb 27 22:17:40 2018</br>
 OS/Arch:   linux/amd64</br>

Server:</br>
 Engine:</br>
  Version:  17.12.1-ce</br>
  API version:  1.35 (minimum version 1.12)</br>
  Go version:   go1.9.4</br>
  Git commit:   7390fc6</br>
  Built:    Tue Feb 27 22:16:13 2018</br>
  OS/Arch:  linux/amd64</br>
  Experimental: false