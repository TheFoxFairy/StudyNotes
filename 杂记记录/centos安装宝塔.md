# centos安装宝塔

## 安装

```
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

**Linux面板7.1.0升级命令：**

```
curl http://download.bt.cn/install/update6.sh|bash
```

## 访问

```
端口默认为8888
```

## 宝塔配置mysql

安装完，发现无法远程连接，操作如下

* 如果用的是阿里云之类的，去打开相关端口配置
* 然后放行宝塔端口
* 进入mysql中，输入如下即可

```
grant all on *.* to root@'%' identified by '这是你mysql密码' with grant option;

flush privileges;
```



