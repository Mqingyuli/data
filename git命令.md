git

[toc]



# 1.git账号

```c
//账号
//Mqingyuli
//密码
//1314520.lss
```



# 2.git命令



```c

git init 创建仓库
git add .   把文件添加到仓库
git commit -m ."\文件名"  添加到暂存区 文件名及保存的
git status 查看是否还有文件未提交
git reset --hard 版本号   去到某个版本
git log 可以查看提交历史
git reflog  查看版本号
git branch 查看分支
git reset --hard HEAD^ 版本回退
git reflog   查看版本号
git branch -d test 删除分支
git rm file 删除文件
git branch test 创建分支
git checkout test 更改当前分支
git checkout -b test 创建并更改分支
git switch -c test 创建并切换到新分支
git switch test 切换到分支
git merge <name> 合并某分支到当前分支
git log --graph   查看分支合并图

```

# 3.git链接库

```c
//资料库  https://github.com/Mqingyuli/data.git
//git上传到云端
/*
git remote add origin https://github.com/Mqingyuli/data.git
git branch -M main 
git push -u origin main
*/
/*
1.添加到本地仓库 git add .
2.添加提交描述 git commi -m "提交"
3.git pull origin master   //master 分支名
4.git push -u origin master //master 分支名
*/
```

