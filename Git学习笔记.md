# Git学习笔记


## 创建版本库 ##
使用前需要使用`git config --global` 设置用户名和邮箱
1.`git init`:把当前目录变成git可以管理的仓库，.git目录是Git用来跟踪管理版本库的。
 - `git add <fileName>`:把文件添加到仓库的暂存区。
 - `git commit -m "更改说明"`: 文件提交到仓库。
 - `git status`:查看当前状态
 - add可以多次使用，commit会把之前add的内容一次性提交上去
## 版本回溯 ##
 - `git log`:查看提交历史记录。
 - `git reflog`:查看使用过的git命令
 - `git reset --hard HEAD^`:回溯到上一个版本,^个数决定回溯到上几个版本。
 - `git reset --hard partOfId`:回溯到指定版本（主要用于回到未来版本）
 - 
## 暂存区与工作区 ##
 - 仓库中非隐藏目录就是工作区
 - .git隐藏目录是Git版本库
 
版本库

 - 版本库中存在暂存区和自动创建的分支
 - `git add` 会将文件修改添加到暂存区
 - `git commit` 将暂存区中的内容提交到当前分支
 - `git diff HEAD -- <file>` 查看工作区和版本库的最新内容有什么不同
 

`git checkout -- <file>`:

 - 工作区的修改未放入暂存区：撤回到和版本库一样的状态
 - 工作区的修改已经添加到暂存区:撤回到和暂存区一样的状态
 - 总之，就是让这个文件回到最近一次git commit或git add时的状态。
 - 可以使用`git reset` 回到最新版本，就能清空暂存区的内容；然后使用`git checkout --` 撤销掉工作区的修改

## 删除 ##

 - `git rm + git commit`
 - `rm + git add + git commit`
## 远程仓库 ##
    
 - `git remote add origin git@server-name:path/repo-name.git`：关联一个远程库，origin是默认的远程库名字
 - `git push -u origin master`：-u不但会把本地master的内容推送到远程，而且还会把远程的master分支和本地master分支关联起来，以后推送和拉取能够简化
 - `git clone https://github.com/path/repo-name.git`：从远程仓库克隆

 
    

