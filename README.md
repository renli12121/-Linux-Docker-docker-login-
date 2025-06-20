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

加载并重启

    sudo systemctl daemon-reload
    sudo systemctl restart docker
    
至此完成了虚拟机中的操作。


步骤三：代理软件的相关配置

    本文使用的是Clash，不需要修改配置文件，直接在软件界面中设置，简单快捷，其他代理软件可以自行在网络中搜索方法进行相同配置。

![image](https://github.com/user-attachments/assets/14e4fdb6-0027-40b6-bc13-49e982df62b5)

①确保 Clash 允许局域网连接：Allow LAN 开启

重启Clash

cmd中输入如下命令检查：


    netstat -ano | findstr 7890
    
出现如下内容则正确：

![image](https://github.com/user-attachments/assets/860a793e-f498-4adc-b191-ad6cebca4bdf)

②确保启用原始 TCP 转发：TUN Mode 开启

重启Clash

步骤四：Windows和虚拟机防火墙设置

Windows：

    开放7890端口

Linux：

    开放7890 / 443端口
    sudo firewall-cmd --add-port=7890/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent

保存

    sudo firewall-cmd --reload

检查

    sudo firewall-cmd --list-ports

步骤五：验证

    docker login -u liming264
    Password: 

liming264替换为你的用户名即可。成功状态如下：

![image](https://github.com/user-attachments/assets/b170237e-1b36-4191-a737-edb4add0c8e8)

至此完成整个配置流程，登录之后你可以将自己本地的镜像上传至DockerHub中。


  
