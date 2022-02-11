# 安装docker

## 卸载旧版本

```
sudo yum remove docker \
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

## 使用yum安装

```
sudo yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2

sudo yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

sudo sed -i 's/download.docker.com/mirrors.ustc.edu.cn\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo
```

## 安装Docker

```
sudo yum makecache fast
sudo yum install docker-ce
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
```

## 启动 Docker CE

```
sudo systemctl enable docker
sudo systemctl start docker
```

### 配置docker加速器

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
        ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

或者

```
vim /etc/docker/daemon.json

{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
        ]
}
```

## Docker Hub 镜像加速器列表

### 卸载docker

```
rpm -qa|grep docker

yum -y remove  containerd.io.x86_64
```

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200816022833.png)

```
镜像加速器地址
```

| 镜像加速器 | 镜像加速器地址 | 专属加速 | 其他加速 |
| :--- | :--- | :--- | :--- |
| [Docker 中国官方镜像](https://docker-cn.com/registry-mirror) | `https://registry.docker-cn.com` |  | Docker Hub |
| [DaoCloud 镜像站](https://daocloud.io/mirror) | `http://f1361db2.m.daocloud.io` | 可登录，系统分配 | Docker Hub |
| [Azure 中国镜像](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy) | `https://dockerhub.azk8s.cn` |  | Docker Hub、GCR、Quay |
| [科大镜像站](https://mirrors.ustc.edu.cn/help/dockerhub.html) | `https://docker.mirrors.ustc.edu.cn` |  | Docker Hub、[GCR](https://github.com/ustclug/mirrorrequest/issues/91)、[Quay](https://github.com/ustclug/mirrorrequest/issues/135) |
| [阿里云](https://cr.console.aliyun.com) | `https://<your_code>.mirror.aliyuncs.com` | 需登录，系统分配 | Docker Hub |
| [七牛云](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror) | `https://reg-mirror.qiniu.com` |  | Docker Hub、GCR、Quay |
| [网易云](https://c.163yun.com/hub) | `https://hub-mirror.c.163.com` |  | Docker Hub |
| [腾讯云](https://cloud.tencent.com/document/product/457/9113) | `https://mirror.ccs.tencentyun.com` |  | Docker Hub |

## 检查加速器是否生效

命令行执行`docker info`，如果从结果中看到了如下内容，说明配置成功。

```
Registry Mirrors:
 [...]
 https://registry.docker-cn.com/
```

### 查看容器

```
docker ps -a
```

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200816022834.png)

### 停止容器

```
docker stop 容器ID ## 上图的有一串字符串的就是容器ID
```

### 删除容器

```
docker rm 容器ID ##  需要先停止容器，才能进行删除
```

### 查看镜像

```
docker images
```

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200816022835.png)

### 删除镜像

```
docker rmi 镜像ID ##　和删除容器操作差不多
```



