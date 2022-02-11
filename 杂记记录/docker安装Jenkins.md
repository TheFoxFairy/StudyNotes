# docker安装Jenkins

## 介绍

## 安装

### docker 安装

```
docker run --name devops-jenkins --user=root \
    -p 8080:8080 -p 50000:50000 \
    -v /opt/data/jenkins_home:/var/jenkins_home \
     -d jenkins/jenkins:lts
```

## jenkins配置

### 初始化jenkins及安装插件

* 启动完jenkins后通过浏览器输入地址[http://部署jenkins主机IP:端口](http://部署jenkins主机IP:端口)

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922224932.png)

* 根据提示从输入administrator password 或者可以通过启动日志

```
docker logs devops-jenkins
```

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922224933.png)

* 选择安装插件方式，这里我是默认第一个

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922224934.png)

* 进入插件安装界面，连网等待插件安装

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922224935.png)

* 安装完插件后，进入创建管理员界面，按照要求创建账号

### 操作

```
http://ip:port/restart/ # 重启
http://ip:port/exit/ # 关闭
```



