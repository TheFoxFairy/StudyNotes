# CentOS修改yum源为阿里源

1、安装wget，如果安装了就无需安装

```
yum install -y wget
```

2、备份原yum源

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

3、下载阿里云的CentOS-Base.repo 到/etc/yum.repos.d/

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

4、清理缓存

```
yum clean all
```

5、生成新的缓存

```
yum makecache
```

6、更新yum

```
yum -y update
```



