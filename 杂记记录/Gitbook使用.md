## Gitbook使用

### 如何使用

#### **说明**

Gitbook 是一个很好的做笔记的软件

#### **下载编辑器**

[官网](https://legacy.gitbook.com/editor)

#### **配置gitbook环境**

##### **安装nodejs**

- [官网](https://nodejs.org/en/)
- 下载安装
- 验证: `node --version`，验证是否安装成功

##### **配置环境**

- 打开管理员 cmd

- 输入命令，全局安装 gitbook

  ```
  npm install gitbook-cli -g
  
  这样可能会安装很慢，安装淘宝源提高下载速度
  ```

- 三种换源方法

  ```
  1.通过config命令
  npm config set registry https://registry.npm.taobao.org –global 
  npm config set disturl https://npm.taobao.org/dist –global
  
  2.命令行指定
  npm –registry https://registry.npm.taobao.org info underscore
  
  3.编辑 ~/.npmrc 加入下面内容
  registry = https://registry.npm.taobao.org
  ```

- 更换源后，使用cnpm安装

  ```
  cnpm install gitbook-cli -g
  ```

##### **初始化目录文件**

```
gitbook init
```

##### **编译书籍**

```
gitbook build
```

##### **运行服务**

```
gitbook serve
```

有时候出现问题，运行如下语句

```
gitbook serve ./ --log=debug --debug
```

