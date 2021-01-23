
### 创建并切换到分支  tag类似  
git branch dev  
git checkout dev  
或  
git checkout -b dev  
或  
git checkout -b dev(本地分支名称) origin/dev(远程分支名称)  
### 这条命令表示把本地dev分支提交到远程仓库，即创建了远程分支dev。
git push origin dev，
### 查看本地分支与远程分支的映射关系使用以下命令（注意是双v）：  
git branch -vv
### 建立当前分支与远程分支的映射关系:
git branch -u origin/dev  
或  
git branch --set-upstream-to origin/dev  
或  
git branch –set-upstream dev origin/dev  
### 撤销本地分支与远程分支的映射关系
git branch --unset-upstream
### 为远程仓库起了一个别名，那么这里就可以使用别名origin调用。这里的含义是将dev这个分支提交到远程仓库上面。如果远程仓库没有这个分支，那么也会新建一个该分支。
git push origin dev  
### 合并分支
git checkout master  
git merge yourbranch  
**注：本地新建分支， push到远程服务器上之后，使用git pull或者git pull 拉取或提交数据时会报错，必须使用命令：git pull origin dev（指定远程分支）；如果想直接使用git pull或git push拉去提交数据就必须创建本地分支与远程分支的关联。**
### 删除本地分支
git branch -d dev  
### 删除远程分支  
git push origin --delete dev

### 撤回commit  后面数字撤回几次提交  代码仍然会保留
git reset --soft HEAD~1

### 查看分支的父分支
git reflog show dev

### git储藏 save表示版本  不会产生commit记录  用于切换分支使用  
git stash save "test-cmd-stash"   
git stash list
### 这个指令将缓存堆栈中的第一个stash删除，并将对应修改应用到当前的工作目录下
git stash pop   
### 将缓存堆栈中的stash多次应用到工作目录中，但并不删除stash拷贝
git stash apply   
#### 删除stash
git stash drop "test-cmd-stash"
### 清除所有stash
git stash clear  

### [rebase](http://jartto.wang/2018/12/11/git-rebase/)
Git rebase 不会取回代码 要用git fetch先取回， git rebase 是合并代码。  
（1）首先用git fetch返回服务器上的代码  
（2）首先用git rebase origin/master 合并  
（3）如果发生冲突了会提示， 然后可以使用git diff查看冲突， 在手工改掉冲突， 在用git add ‘文件名’ 添加修改后文件，最后用git rebase --continue继续没完成的合并  
（4）最后就可以用git push 更新到服务器上去。  


### 大文件上传下载 git-lfs
https://zzz.buzz/zh/2016/04/19/the-guide-to-git-lfs/

### Git global setup  
git config --global user.name "林武华"  
git config --global user.email "linwuhua@megvii.com"  

### Create a new repository
git clone git@git-pd.megvii-inc.com:linwuhua/yapi-diff.git
cd yapi-diff
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master

### Push an existing folder
cd existing_folder  
git init  
git remote add origin git@git-pd.megvii-inc.com:linwuhua/yapi-diff.git  
git add .  
git commit -m "Initial commit"  
### 将会关联到远程的master 分支上，以后就可以git push不指定参数了
git push -u origin master  

### Push an existing Git repository
cd existing_repo  
git remote rename origin old-origin  
git remote add origin git@git-pd.megvii-inc.com:linwuhua/yapi-diff.git  
git push -u origin --all  
git push -u origin --tags  
