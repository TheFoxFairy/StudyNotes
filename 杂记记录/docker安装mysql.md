# docker安装mysql

## 安装mysql5.7版本

```
docker pull mysql:5.7
```

## 启动容器

```
docker run --name mysql5.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

* MYSQL\_ROOT\_PASSWORD为设置的初始密码
* -p 3306:3306:主机端口映射到容器端口

一般情况下，已经可以远程连接数据库了，如果不行，往下看看

## 开放端口

```
# firewall-cmd --zone=public --add-port=3306/tcp --permanent
# firewall-cmd --reload // 重启防火墙
```

## 修改密码

* MySQL 5.7.6 以及后续版本：

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

* MySQL 5.7.5 以及早期版本：

```
SET PASSWORD FOR 'root'@'localhost' = PASSWORD( '123456');
```

## 远程访问

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; 
FLUSH PRIVILEGES;
```



