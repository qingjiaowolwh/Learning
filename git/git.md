
### 本地分支名x origin/远程分支名x  拉取远程分支并创建本地分支 tag类似  
git checkout -b 
### 查看本地分支与远程分支的映射关系使用以下命令（注意是双v）：  
git branch -vv
### 建立当前分支与远程分支的映射关系:
git branch -u origin/addFile
### 或者使用命令：
git branch --set-upstream-to 
### 撤销本地分支与远程分支的映射关系
git branch --unset-upstream
### 为远程仓库起了一个别名，那么这里就可以使用别名origin调用。这里的含义是将hello_git_branch这个分支提交到远程仓库上面。如果远程仓库没有这个分支，那么也会新建一个该分支。
git push origin hello_git_branch 

### 撤回commit  后面数字撤回几次提交  代码仍然会保留
git reset --soft HEAD~1
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

### 这条命令是基于当前分支创建的本地分支，假设当前分支是master(远程分支)，则是基于master分支创建的本地分支dev。
git branch dev，
### 这条命令表示从当前master分支切换到dev分支
git checkout dev，
### 这条命令把创建本地分支和切换到该分支的功能结合起来了，即基于当前分支master创建本地分支dev并切换到该分支下
git checkout -b dev，
### 这条命令表示把本地dev分支提交到远程仓库，即创建了远程分支dev。
git push origin dev，
### 把本地dev分支和远程dev分支相关联。
git branch –set-upstream dev origin/dev，  
**注：本地新建分支， push到远程服务器上之后，使用git pull或者git pull 拉取或提交数据时会报错，必须使用命令：git pull origin dev（指定远程分支）；如果想直接使用git pull或git push拉去提交数据就必须创建本地分支与远程分支的关联。**
### 合并分支（先切换到主分支）
git merge  dev  


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
