
# Docke 镜像加速

国内从 DockerHub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。  
Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

    Docker官方提供的中国镜像库：https://registry.docker-cn.com
    七牛云加速器：https://reg-mirror.qiniu.com  //速度较快

当配置某一个加速器地址之后，若发现拉取不到镜像，请切换到另一个加速器地址。  
国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker   
的云平台选择对应的镜像加速服务。

我们以 Docker 官方加速器 https://registry.docker-cn.com 为例进行介绍。

# Ubuntu16.04+、Debian8+、CentOS7

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）：

    {"registry-mirrors":["https://registry.docker-cn.com"]}

之后重新启动服务：  
      $ sudo systemctl daemon-reload  
      $ sudo systemctl restart docker  

# 检查加速器是否生效

> 检查加速器是否生效配置加速器之后，如果拉取镜像仍然十分缓慢，请手动检查加速器配置是否生效，在命令行执行 docker info，如果从结果中看到了如下内容，说明配置成功。  
> $ docker info  
> Registry Mirrors:  
> >  https://registry.docker-cn.com/  
