---
layout: index
title: "Git Usage"
category: devops
date: 2018-11-21 15:17:55
---

# git handbook

- [Usage link](http://rogerdudler.github.io/git-guide/)

> View current branch was created based which branch


```
 git reflog --date=local | grep <branch-name>
```


> Cherry pick

```
dd2e86 - 946992	- 9143a9 - a6fd86 - 5a6057 [master]
           \
            76cada - 62ecb3	- b886a0 [feature]

git checkout master
git cherry-pick 62ecb3

#That’s all. 62ecb3 is now applied to the master branch and commited (as a new commit) in master.
```

> 一台电脑配置多个git账户

```bash
# 如果执行ssh-add命令报错： Could not open a connection to your authentication agent
ssh-agent bash

# 删除现在的agent列表
ssh-add -D

# 向agent添加认证
ssh-add id_rsa

# 查看agent列表
ssh-add -l

# 测试账号是否连接到仓库
ssh -T git@github.com

# 查看现在的remote信息
git remote -v

git@github.com:Evliess/evliess.github.io.git
# 删除现在的remote信息
git remote rm origin

# 再次添加remote信息，将hostName替换为.ssh/config中对应的值
git remote add origin git@jiajia126.github.com:Evliess/evliess.github.io.git


# config template

#### start #### 
## Host的名字可以自己定义

#jiajia_yuwei@126.com
Host jiajia126.github.com
HostName github.com
User jiajia_yuwei@126.com
IdentityFile C:\\Users\\JiaJiaGui\\.ssh\\jiajia126

#jiajia_yuwei@126.com - github.com
Host github.com
HostName github.com
User jiajia_yuwei@126.com
IdentityFile C:\\Users\\JiaJiaGui\\.ssh\\id_rsa

#### end #### 

```

> Git proxy setting

```bash
# 设置ss
git config --global http.proxy 'socks5://127.0.0.1:1080'

git config --global https.proxy 'socks5://127.0.0.1:1080'

# 设置代理
git config --global https.proxy http://127.0.0.1:1080

git config --global https.proxy https://127.0.0.1:1080

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

```


> git 比较本地修改的文件与master差异

```bash
git diff --cached file
git diff --staged file

```

> git 撤销未push的commit

```bash
git log

# 完成撤销, 同时将代码恢复到前一个commit_id 对应的版本
git reset --hard commit_id

# 完成Commit命令的撤销，但是不对代码修改进行撤销
git reset commit_id

```
> Migrate reposityA to reposityB

```bash
git clone --bare reposityA
git remote rm origin <!--optional -->
git remote add origin reposityB
git push --mirror origin

```

> [Git revert](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert%E7%9A%84%E9%80%89%E6%8B%A9)  

```bash
git revert 47900454c18cca36f66f7832fbb1617797b52708
git commit
 
```

> Switch branch

```bash
git branch branch-name
```

> Create branch from commit

```bash
git branch branchName <sha1-of-commit>
or
git checkout -b branchName <sha1-of-commit>

```

> Create branch from tag

```bash
git tag v1.0
git checkout -b branch1 v1.0   <!-- branchName:branch1 tagName:v1.0 -->
git push origin branch1

```
> Replace the master branch with another branch

```bash
git checkout another_branch
git merge -s ours --no-commit master
git commit
git checkout master
git merge another_branch
git push origin master

```

> Jenkins + Ant + Java8
ant version 1.10.X 否则会出现javac1.8 error 或者 ant script找不到系统配置的java_home  

> Generate ssh-key
- **C:\Users\XXX\.ssh\id_rsa.pub**

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" 
git add 
git commit -m "comment"
git pull
git push origin master 

```