# docker安装nginx 并个性化配置

## 查询nginx

```
docker search nginx
```

## 下载nginx

```
docker pull nginx
```

### 运行 nginx

```
docker run -p 80:80 --name mynginx -v $PWD/www:/www -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf -v $PWD/logs:/wwwlogs  -d nginx
```

命令说明：

* -v 主机path:容器path

  * 主机目录映射到容器目录

```
-v xxx/www:/www：将主机中当前目录下的xxx挂载到容器的/www
-v xxx/conf/nginx.conf:/etc/nginx/nginx.conf # 同理
```

* -p 主机port:容器port：

  * 将容器的端口映射到主机的端口

* --name xxx：将容器命名为xxx

## 查看运行情况

```
docker ps
```

### 更好的文档

[点击](https://zj-network-guide.readthedocs.io/zh_CN/latest/nginx/docker%E5%AE%89%E8%A3%85nginx/)



