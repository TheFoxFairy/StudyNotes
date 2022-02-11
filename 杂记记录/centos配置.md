# centos配置

## 修改主机名

```
hostname <hostname> # hostname 是主机名 # 这个方法重启系统后就会失效
```

或者

```
vim /etc/profile
```

添加如下:

```
export HOSTNAME='XXX'
```

然后刷新

```
source /etc/profile
```

重启即可或者打开新会话

## 上传和下载

* 首先安装lrzsz

```
yum -y install lrzsz
```

* 上传文件，执行命令rz，会跳出文件选择窗口，选择好文件，点击确认即可。
* 下载文件，执行命令sz



