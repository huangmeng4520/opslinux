## 一、git 初始化

```
1.初始化版本库：

git init    

2.添加文件到版本库（只是添加到缓存区），.代表添加文件夹下所有文件 

git add .

3.把添加的文件提交到版本库，并填写提交备注(必不可少)

git commit -m "update readme"

到目前为止，我们完成了代码库的初始化，但代码是在本地，还没有提交到远程服务器，要提交到就远程代码服务器，进行以下两步：

4.把本地库与远程库关联

git remote add origin 你的远程库地址

例如：git remote add origin https://github.com/Lancger/opslinux.git

5.第一次推送（提交）代码时：

git push -u origin master 

第一次推送后，直接使用该命令即可推送修改

git push origin master 
```
参考：http://blog.csdn.net/liang0000zai/article/details/50724632


## 二、初次提交需要执行下面配置

```
git config --global user.email "1151980610@qq.com"
  
git config --global user.name "Lancger"

git add *
  
git commit -m "项目更新"

git push origin master
  
#新增的目录需要新建文件，才可以提交上来
```

## 三、git push 总是要输入账号密码解决办法
```
1、先cd到根目录，执行git config –global credential.helper store命令
[root@linux-node1 ~]# git config --global credential.helper store

2、执行之后打开 .gitconfig 文件可以看到
[root@linux-node1 ~]# cat .gitconfig
[user]
	email = 1151980610@qq.com
	name = Lancger
[credential]
	helper = store
  
3、重新push 提交 输入用户名密码。

4、下次就可以直接提交了
```

## 四、Git更新远程仓库代码到本地 git fetch
```
1、查看远程分支
[root@linux-node1 opslinux]#  git remote -v
origin	https://github.com/Lancger/opslinux.git (fetch)
origin	https://github.com/Lancger/opslinux.git (push)

2、从远程获取最新版本到本地
#使用如下命令可以在本地新建一个temp分支，并将远程origin仓库的master分支代码下载到本地temp分支
[root@linux-node1 opslinux]#  git fetch origin master:temp
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 15 (delta 10), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (15/15), done.
From https://github.com/Lancger/opslinux
 * [new branch]      master     -> temp
 
 
3、比较本地仓库与下载的temp分支
[root@linux-node1 opslinux]# git diff temp
diff --git a/index.md b/index.md
deleted file mode 100644
index ff6da49..0000000
--- a/index.md
+++ /dev/null
@@ -1,109 +0,0 @@
...........

4、合并temp分支到本地的master分支
[root@linux-node1 opslinux]# git merge temp
对比区别之后，如果觉得没有问题，可以使用如下命令进行代码合并：
[root@linux-node1 opslinux]# git merge temp
Already up-to-date.

5、删除temp分支
[root@linux-node1 opslinux]# git branch -d temp
Deleted branch temp (was 7eb97aa).

如果该分支的代码之前没有merge到本地，那么删除该分支会报错，可以使用git branch -D temp强制删除该分支。
 
```
参考：https://blog.csdn.net/liang0000zai/article/details/50724632


