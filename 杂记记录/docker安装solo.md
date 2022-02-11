# docker安装solo.md

## 介绍

[solo博客](https://github.com/88250/solo) 类似于hexo，一款很漂亮的博客

## 搭建

### 安装docker

[点我进行学习](./安装docker.md)

### 安装solo

```
docker pull b3log/solo
```

### 启动容器

需要提前在数据库里创建名称为solo的数据库

```
docker run --detach --name solo --network=host \
    --env RUNTIME_DB="MYSQL" \
    --env JDBC_USERNAME="solo" \
    --env JDBC_PASSWORD="数据库密码" \
    --env JDBC_DRIVER="com.mysql.cj.jdbc.Driver" \
    --env JDBC_URL="jdbc:mysql://hostname :port/solo?useUnicode=yes&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true" \
    b3log/solo --listen_port=8001 --server_scheme=http --server_host=hostname  --server_port=4000
```

```
docker run --detach --name solo --network=host \
    --env RUNTIME_DB="MYSQL" \
    --env JDBC_USERNAME="root" \
    --env JDBC_PASSWORD="xxx" \
    --env JDBC_DRIVER="com.mysql.cj.jdbc.Driver" \
    --env JDBC_URL="jdbc:mysql://hostname:port/solo?useUnicode=yes&characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC" \
    --volume /home/solo/skins:/opt/solo/skins   \
    --volume /home/solo/images:/opt/solo/images \
    b3log/solo --listen_port=4000 --server_scheme=http --server_host=hostname --server_port=4001
```

* JDBC\_USERNAME: 用户名
* JDBC\_PASSWORD: 密码
* hostname: 主机
* port: 数据库端口
* --listen\_port: 进程监听端口,一般默认访问端口
* --server\_scheme: 最终访问协议，如果反代服务启用了 HTTPS 这里也需要改为https
* --server\_host: 最终访问域名或公网 IP，不要带端口
* --server\_port: 最终访问端口，使用浏览器默认的 80 或者 443 的话值留空即可

#### 目录配置

* 如果要使用其他皮肤，可以挂载目录 skins（里面需要包含所需使用的所有皮肤）：

```
--volume /home/solo/skins:/opt/solo/skins
```

* 挂载md目录

```
 --volume  /home/solo/notebook:/opt/solo/markdowns \
```

* 其他

```
--volume /home/solo/images:/opt/solo/images
```

### 其他

如果想将github项目部署到solo中

请看搜索本笔记 jenkins 相关文章

这是部署好jenkins后，在构建中所填写的执行命令

```
mysql -h 服务器IP或者服务器网址 -u数据库用户名 -p数据库密码 -P 端口-D数据库名<sql文件所在位置 &&
rm -rf 挂载的markdowns位置 &&
cp -rf 自动化部署项目所在位置 挂载的markdowns位置 &&
rm -rf 挂载的markdowns位置/SUMMARY.md && docker restart solo容器
```

```
为什么这样写命令?因为想自动化部署，自动化部署后，由于所在项目没有在理想目录，需要用cp命令进行文件夹复制过去，进行覆盖


为什么要进行删除?因为solo下markdowns md格式，已经被solo更改了，用cp覆盖后，还会存在。

为什么要执行sql?因为solo已经将文章存入数据库了，我需要将数据库中文章该表清除，这样在后面操作，就不会有残留、

最后重启solo容器，solo会马上读取markdowns目录
```

### 美化

* 添加音乐

![image-20200922225051559](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922225159.png)

> 分别所在位置填写如下信息

HTML head：

```
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.css">
<script src="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.js"></script>
```

公告：

```
<!-- 公告栏可使用 HTML、JavaScript，比如可以在此加入第三方统计 js -->

<div class="aplayer" data-id="4899337473" data-server="netease" data-type="playlist" data-list-folded="true"></div>
```

页脚：

```
<script src="https://cdn.jsdelivr.net/npm/meting@1.1.0/dist/Meting.min.js"></script>
```

### 额外

由于是从github拉去的项目，图片是相对路径，导致显示不出来，因此，将如下代码，放入上方的页脚处即可

```
<script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script>
<script>
$("#articlePage img").each(function(){
    var url = $(this).attr("src");
    url = "https://raw.githubusercontent.com/CoderAngle/notebook/master"+url
    $(this).attr("src",url );
    if(url.indexOf("assets") >= 0)
    console.log(url)
});
</script>
```



