# .gitignore不生效

每次修改.gitignore时，需要清楚git本地缓存，此时提交，gitignore才会生效。

```
git rm -r --cached .
git add
git commit -m "update .gitignore"
```



# git新建本地仓库——关联远程仓库——新建分支——拉取仓库

```
git init                # 新建本地仓库
git checkout -b dev     # 新建分支，并切换到新的分支
（相当于
git branch dev
git checkout dev
）
git remote add origin url  # 关联远程仓库
git remote -v              # 查看远程仓库分支
git pull origin dev        # 拉去远程分支
```



**或者直接使用clone命令：**

```
git clone -b 分支名 url（git地址）
```



# 本地分支关联远程分支

```
git branch --set-upstream-to=origin/远程分支名称  本地分支名称
```

