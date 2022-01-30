git

[toc]



# 1.git账号

```c
//github账号
//Mqingyuli
//密码
//1314520.lss
/**************/
//gitee账号
//2458745242@qq.com
//密码
//1314520lss
//sourcetree账号
//qyli2
//初始密码 ：Bwk81000970
//新密码
```

```c
/*
经测试接入节点的sx1280芯片1的接收频率为2424.43MHz~2424.82MHz范围内时接收数据正常，sx1280芯片2的接收频率为2424.40MHz~2424.79MHz范围内时接收数据正常,芯片1和芯片2可以接收的频率的最大最小之差都是0.39Mhz
*/
```

```c
SC7A20驱动 master分支： https://spidersens.ddns.net:17990/scm/em/drivers_sc7a20.git
SX1268驱动 master分支： https://spidersens.ddns.net:17990/scm/em/drivers_sx126x.git
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
//github资料库  https://github.com/Mqingyuli/data.git
//gitee资料库   https://gitee.com/qing-yuxiao/li_demo.git 
//git上传到云端
/*
//github资料库
git remote add origin https://github.com/Mqingyuli/data.git
//gitee资料库
git remote add origin https://gitee.com/qing-yuxiao/li_demo.git
git branch -M main 
git push -u origin main
*/
/*
1.添加到本地仓库 git add .
2.添加提交描述 git commi -m "提交"
//拉取远程分支
3.git pull origin master   //master 分支名
//上传远程分支
4.git push -u origin master //master 分支名
*/
```

# 4.git删除库

```c
/*
方法1：更换git远程仓库地址
 1.查看当前remotes
      git remote -v
 2.修改remotes   
      git remote set-url origin https://github.com/test/test.git 

方法2：重置git远程仓库地址
 1.删除当前地址
      git remote rm origin
 2.新增地址
      git remote add origin https://github.com/test/test.git 
*/
```

