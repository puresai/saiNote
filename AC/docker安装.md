# docker安装

---

### 环境

> centos版本： 7.5

Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持Docker 。

通过 uname -r 命令查看你当前的内核版本
> uname -r 3.10.0-693.2.2.el7.x86_64


### 安装

如果安装过docker

```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```


安装一些必要的系统工具：

> yum install -y yum-utils device-mapper-persistent-data lvm2

添加软件源信息：

> yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

更新 yum 缓存：

> yum makecache fast

安装 Docker-ce：

> yum -y install docker-ce

### 测试

启动 Docker 后台服务

> systemctl start docker

测试运行 hello-world

> docker run hello-world


由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。



### 删除 Docker CE

> yum remove docker-ce && rm -rf /var/lib/docker