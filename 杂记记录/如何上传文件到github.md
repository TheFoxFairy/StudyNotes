## 上传笔记

### 注册&创建

- **需要在github里注册一个账号**
- 新建一个仓库

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202090555938.png)

![img](../../../../../Pictures/assets/如何上传文件到github/20200922224806.png)

新建好后，可以看到一些教你如何上传的方法

### 上传

```
git init 初始化文件目录
git add * 添加所有目录文件 git add RENAME.md
git add --all 添加所有文件
git status 查看添加的状态
git commit -m "提交的相关说明"
git remote add origin xxxx.git  提交到哪里
git push origin master -u 推送/上传内容
git pull origin master -u 拉取该文件内容
-f 强行推送
```

* 首先在github上创建仓库
* 然后下载，就可以进行拉去或者上传文件

```
git add --all
git status
git commit -m "说明"
git push
```

可能会让输入账号与密码。

