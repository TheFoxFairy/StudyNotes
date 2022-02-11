# docker对web容器管理

## 简介

为了方便管理docker，利用docker搭建docker的web管理

## 安装

### shipyard

* #### 概述

Shipyard也是完全基于Docker API，支持container管理、engine管理（一个engine就是监听tcp端口的docker daemon）。

优点：

```
支持多主机；
支持container及engine资源限制及图形展示；
支持container实例横向扩展；
支持批量创建，支持images，container，node节点管理；
支持创建时自动调度,动态集群，可以扩展节点规模；
在线console终端；
```

缺点：

```
不支持container批量操作。
```

* #### 相关组件

> shipyard依赖的容器

* Rethinkdb容器：作为数据存储工具（用来存放账号（account）、引擎（engine）、服务密钥（service key）、 扩展元数据（extensionmetadata）等信息，但不会存储任何有关容器或镜像的内容。）
* etcd容器：作为服务发现工具
* docker-proxy：docker容器网络代理工具
* swarm：swarm管理器

* #### 安装部署

```
docker pull rethinkdb
docker pull microbox/etcd
docker pull shipyard/docker-proxy
docker pull swarm
docker pull dockerclub/shipyard
```

* #### 修改安装脚本\(修改为中文镜像\) {#2修改安装脚本修改为中文镜像}

```
 wget https://shipyard-project.com/deploy
 grep -n shipyard:latest deploy
 sed -i 's#shipyard/shipyard:latest#dockerclub/shipyard:latest#g' deploy

#若8080端口被使用,需修改端口，改为8090试列
#grep -n 'PORT:-8080' deploy
#sed -i 's/PORT:-8080/PORT:-8090/g' deploy
```

* ### 安装

```
sh deploy #安装
#cat deploy | ACTION=remove bash  #删除
```

* ### 添加node节点

```
curl -sSL https://shipyard-project.com/deploy | ACTION=node DISCOVERY=etcd://hostname:port bash -s
```

* ### web管理

##### Container管理

可以对Container进行重启、停止、暂停、删除、扩展、重命名、提交、查看状态监控/日志、及console登录等。

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225231)

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225232)

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225233)

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225234)

* ##### images管理

可以pull为images打tags

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225235)

* ###### **Nodes管理**

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225236)

