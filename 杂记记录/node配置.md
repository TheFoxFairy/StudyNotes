## 安装nodejs

```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -

sudo yum -y install nodejs
```

### 更新npm ：

```
npm install -g npm
```

### 更新node版本：

```
先清除npm缓存：
    npm cache clean -f

然后安装n模块：
    npm install -g n

升级node.js到最新稳定版：
    n stable
```



