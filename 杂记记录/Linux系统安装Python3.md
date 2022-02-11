# Linux系统安装Python3

## 终端下载Python包：

```
wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tar.xz    # 下载
tar xJf Python-3.6.8.tar.xz    # 解压
```

## 终端安装

```
cd Python-3.6.8/
/configure --with-ssl    # 配置openssl
make
make install
```



