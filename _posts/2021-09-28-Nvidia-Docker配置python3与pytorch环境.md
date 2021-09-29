---
layout:     post
title:      Nvidia-Docker配置python3与pytorch环境
subtitle:   Nvidia-Docker配置python3与pytorch环境
date:       2021-09-28
author:     JMX
header-img: img/post_bd_04.jpg
catalog: true
tags:
    - Nvidia-Docker
    - GPU
---

# 一、Docker与Nvidia-docker安装

## TIPS：为了避免下载源过慢，建议添加中科大源/清华源
```
1. sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
2. sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
3. sudo apt update
```

## 1. Docker安装


```
1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

 sudo apt-get update
 sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
    
2. Add Docker’s official GPG key:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

3. Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below. Learn about nightly and test channels.

 echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
4. Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

5. To install a specific version of Docker Engine, list the available versions in the repo, then select and install:

a. List the versions available in your repo:

apt-cache madison docker-ce

b. Install a specific version using the version string from the second column, for example, 5:18.09.1~3-0~ubuntu-xenial.

sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

6. Verify that Docker Engine is installed correctly by running the hello-world image.

sudo docker run hello-world

```

## 2. Nvidia-Docker 安装


```
1. Setup the stable repository and the GPG key:

distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

2. To get access to experimental features such as CUDA on WSL or the new MIG capability on A100, you may want to add the experimental branch to the repository listing:

curl -s -L https://nvidia.github.io/nvidia-container-runtime/experimental/$distribution/nvidia-container-runtime.list | sudo tee /etc/apt/sources.list.d/nvidia-container-runtime.list

3. Install the nvidia-docker2 package (and dependencies) after updating the package listing:

sudo apt-get update
sudo apt-get install -y nvidia-docker2

4. Restart the Docker daemon to complete the installation after setting the default runtime:

sudo systemctl restart docker

5. At this point, a working setup can be tested by running a base CUDA container:
sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```

# 二、docker内安装python与pytorch环境

> 拉取nvidia包含cuda的基础镜像。拟安装环境：python3.7, pytorch1.6

```
# 宿主机：提前在宿主机上下载好安装pip3.7要用到的包
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

# 宿主机与容器传输文件
docker cp a.txt containerid:/path

# 宿主机：运行ubuntu:18.04容器
docker run -it -d --name=lz-ubuntu -v /root/get-pip.py:/root/get-pip.py ubuntu:18.04

# 宿主机：进入到容器
docker exec -it lz-ubuntu bash

# 容器内：可选-安装vim
apt-get update
apt-get install vim -y

# 容器内：配置pip源，用以加速安装
sudo mkdir ~/.pip
sudo vim ~/.pip/pip.conf

添加以下内容：
[global]
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com

国内源：
清华：
https://pypi.tuna.tsinghua.edu.cn/simple
阿里云：
http://mirrors.aliyun.com/pypi/simple/
中国科技大学 
https://pypi.mirrors.ustc.edu.cn/simple/
华中理工大学：
http://pypi.hustunique.com/
山东理工大学：
http://pypi.sdutlinux.org/
豆瓣：
http://pypi.douban.com/simple/

# 容器内：可选-配置apt源
mv /etc/apt/sources.list /etc/apt/sources.list.bak
vim /etc/apt/sources.list
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

# 容器内：更新软件包列表
apt-get update

# 容器内：可选-安装调试工具
apt-get install iputils-ping net-tools curl

# 容器内：安装最主要的python包
apt-get install python3.7 python3.7-dev

# 容器内：安装pip3.7
apt install python3-distutils
python3.7 get-pip.py

# 容器内：安装pytorch
# CUDA 10.1
pip install torch==1.6.0+cu101 torchvision==0.7.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html
# 安装其他python包
pip install transformers==2.10.0
pip install pytorch-crf==0.7.2
pip install sklearn
pip install seqeval==1.2.2
pip install pandas

# 时区设置
# 宿主机：从宿主机中拷贝时区文件到容器内，/usr/share/zoneinfo/UCT这个文件是通过软链追溯到的，时区是亚洲/上海
docker cp /usr/share/zoneinfo/UCT  lyz-ubuntu:/etc/
# 容器内：然后在容器内将其改名为/etc/localtime
mv /etc/UCT /etc/localtime

# 容器内：清理无用的包
apt-get clean
apt-get autoclean
du -sh /var/cache/apt/
rm -rf /var/cache/apt/archives

# 容器内：清理pip缓存
rm -rf ~/.cache/pip

# 容器内：清理命令日志
history -c

# 宿主机：打包镜像
docker commit -a '提交人' -m '描述' <容器名/ID> <镜像名称>

```

# 三、nvidia-docker 运行

> nvidia-docker2版本下nvidia-docker run与docker run --runtime=nvidia效果无太大差异

```
# pytorch gpu 可运行
docker run -itd --gpus all --name 容器名 -eNVIDIA_DRIVER_CAPABILITIES=compute,utility -e NVIDIA_VISIBLE_DEVICES=all 镜像名

多出来的东西其实就是这个家伙：NVIDIA_DRIVER_CAPABILITIES=compute,utility
　　
也就是说，如果你不改这个环境变量，宿主机的nvidia driver在容器内是仅作为utility存在的，如果加上compute，宿主机的英伟达driver将对容器提供计算支持（所谓的计算支持也就是cuda支持）。

# nvidia-docker2验证
nvidia-docker --version

# nvidia-docker2 测试
docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi

# nvidia-docker2启动
启动nvidia-docker：
      $: systemctl start nvidia-docker
 查看docker服务是否启动：
      $: systemctl status nvidia-docker
```

## 1. docker run 参数介绍

**docker run 常用参数介绍：**

- --rm选项，这样在容器退出时就能够自动清理容器内部的文件系统。

- --i选项，打开STDIN，用于控制台交互

- --t选项，分配tty设备，该可以支持终端登录，默认为false

- --d选项，指定容器运行于前台还是后台，默认为false

```
其他参数介绍：
  -u, --user=""              指定容器的用户  
  -a, --attach=[]            登录容器（必须是以docker run -d启动的容器）
  -w, --workdir=""           指定容器的工作目录 
  -c, --cpu-shares=0        设置容器CPU权重，在CPU共享场景使用  
  -e, --env=[]               指定环境变量，容器中可以使用该环境变量  
  -m, --memory=""            指定容器的内存上限  
  -P, --publish-all=false    指定容器暴露的端口  
  -p, --publish=[]           指定容器暴露的端口 
  -h, --hostname=""          指定容器的主机名  
  -v, --volume=[]            给容器挂载存储卷，挂载到容器的某个目录  
  --volumes-from=[]          给容器挂载其他容器上的卷，挂载到容器的某个目录
  --cap-add=[]               添加权限，权限清单详见：http://linux.die.net/man/7/capabilities  
  --cap-drop=[]              删除权限，权限清单详见：http://linux.die.net/man/7/capabilities  
  --cidfile=""               运行容器后，在指定文件中写入容器PID值，一种典型的监控系统用法  
  --cpuset=""                设置容器可以使用哪些CPU，此参数可以用来容器独占CPU  
  --device=[]                添加主机设备给容器，相当于设备直通  
  --dns=[]                   指定容器的dns服务器  
  --dns-search=[]            指定容器的dns搜索域名，写入到容器的/etc/resolv.conf文件  
  --entrypoint=""            覆盖image的入口点  
  --env-file=[]              指定环境变量文件，文件格式为每行一个环境变量  
  --expose=[]                指定容器暴露的端口，即修改镜像的暴露端口  
  --link=[]                  指定容器间的关联，使用其他容器的IP、env等信息  
  --lxc-conf=[]              指定容器的配置文件，只有在指定--exec-driver=lxc时使用  
  --name=""                  指定容器名字，后续可以通过名字进行容器管理，links特性需要使用名字  
  --net="bridge"             容器网络设置:
				                bridge 使用docker daemon指定的网桥     
				                host 	//容器使用主机的网络  
				                container:NAME_or_ID  >//使用其他容器的网路，共享IP和PORT等网络资源  
				                none 容器使用自己的网络（类似--net=bridge），但是不进行配置 
  --privileged=false         指定容器是否为特权容器，特权容器拥有所有的capabilities  
  --restart="no"             指定容器停止后的重启策略:
				                no：容器退出时不重启  
				                on-failure：容器故障退出（返回值非零）时重启 
				                always：容器退出时总是重启  
  --rm=false                 指定容器停止后自动删除容器(不支持以docker run -d启动的容器)  
  --sig-proxy=true           设置由代理接受并处理信号，但是SIGCHLD、SIGSTOP和SIGKILL不能被代理  
  
  ```
  
 ## 2. docker 常用命令
```
 ### 显示版本信息 (与python, nvcc相比少了两个‘--’）
$ docker version

### 了解当前Docker的使用状态（当前容器，镜像数目信息，存储空间占用信息，
# OS内核版本， 发行版本， 硬件资源等）
$ docker info

### 拉去一个镜像 ( xxxx 表示某个镜像名字，）
$ docker pull xxxx
# e.g.
# docker pull ubuntu

### 查看系统中已有的镜像(images要带‘s')
$ docker images
# e.g.:
# REPOSITORY  TAG    IMAGES ID   CREATED VIRTUAL SIZE
# ubuntu      latest 4ef6axxxxx   5 day ago  84.0M

### 从镜像创建docker容器
$ docker run -i -t ubuntu /bin/bash 
# or
$ docker run -it 4ef /bin/bash
# 其中 -i, 交互模式，让输入输出都在标准控制台进行；-d，则进入后台
# -t, 为新创建的容器分配一个伪终端
# ubuntu, 用于创建容器的镜像名，可用ID来代替（前3位足够）
# /bin/bash， 在新建容器中运行的命令，可以为任意Linux命令

### 离开当前容器,返回宿主机终端，使用组合键 "Ctrl+P" 和 "Ctrl+Q"

### 查看当前活动的容器
$ docker ps
# CONTAINER ID  IMAGE  COMMAND  CREATED   STATUS   PORTS NAME
# 610xxxx  ubuntu:latest  "/bin/bash" 1 minute ago Up 1 minute ago prickly_wilson

### 宿主机终端与某个容器建立连接
$ docker attach 610

### 从容器创建Docker镜像
$ docker commit -m "hhahaha" 610 ubuntu:hhh
# -m, 新镜像说明
# 610， 某个容器的ID
# ubuntu:hhh， 命名最好不要这么随意
# 那么接下来可以查看新生成的镜像，命令 docker images

### 基于新的镜像创建一个新的容器(一样的)
$ docker run -it ubuntu:hhh /bin/bash

### 给镜像重命名(方便记忆)
$ docker tag IMAGEID(image id) REPOSITORY:TAG

### 给容器重命名
$ docker rename old-container-name new-container-name
```


# 参考链接

https://blog.csdn.net/qq_39698985/article/details/109524762

https://blog.csdn.net/mumoDM/article/details/82503022

https://blog.csdn.net/qq_29518275/article/details/107486028