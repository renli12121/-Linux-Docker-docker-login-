虚拟机Linux Docker 代理配置—解决docker login 失败
  
    本文详细阐述如何配置Docker代理，以解决无法登录docker官网上传镜像到仓库的问题！

问题描述：

    使用 docker login 出现网络问题，导致无法登录 （Client.Timeout exceeded while awaiting headers ），如下图所示：
  
![image](https://github.com/user-attachments/assets/9c2e49c4-5b38-452a-9836-32e2ad9757b7)

解决方案：

步骤一：配置国内镜像加速器

    sudo mkdir -p /etc/docker
    sudo vim /etc/docker/daemon.json

将下面的内容粘贴进去：

    {
        "registry-mirrors": [
        "https://docker.1panelproxy.com",
        "https://2a6bf1988cb6428c877f723ec7530dbc.mirror.swr.myhuaweicloud.com",
        "https://docker.m.daocloud.io",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://your_preferred_mirror",
        "https://dockerhub.icu",
        "https://docker.registry.cyou",
        "https://docker-cf.registry.cyou",
        "https://dockercf.jsdelivr.fyi",
        "https://docker.jsdelivr.fyi",
        "https://dockertest.jsdelivr.fyi",
        "https://mirror.aliyuncs.com",
        "https://dockerproxy.com",
        "https://mirror.baidubce.com",
        "https://docker.m.daocloud.io",
        "https://docker.nju.edu.cn",
        "https://docker.mirrors.sjtug.sjtu.edu.cn",
        "https://docker.mirrors.ustc.edu.cn",
        "https://mirror.iscas.ac.cn",
        "https://docker.rainbond.cc"
        ]
    }
    
保存退出

    :wq
    
步骤二：配置代理

前置条件：①宿主机ip     ②宿主机中的代理软件的端口号

    vim /etc/systemd/system/docker.service.d/http-proxy.conf

将如下内容粘贴进去：

    [Service]
    Environment="HTTP_PROXY=http://192.168.176.41:7890/"
    Environment="HTTPS_PROXY=http://192.168.176.41:7890/"
    Environment="NO_PROXY=localhost,127.0.0.1,::1,192.168.176.0/23,192.168.196.0/24"

    替换ip和端口：
    ①192.168.176.41替换为宿主机的ip地址
    ②7890替换为你在宿主机中所使用的的代理软件的端口号，Clash默认7890

![image](https://github.com/user-attachments/assets/aca46789-b09f-448d-9f5f-334d40707171)

保存退出：

    :wq




  
