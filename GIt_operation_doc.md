# Git日常操作指南


推荐一个[在线练习Git命令网站](https://learngitbranching.js.org)

## Git工作流程图
![git流程图](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg2020.cnblogs.com%2Fblog%2F932856%2F202004%2F932856-20200423143251346-796113044.jpg&refer=http%3A%2F%2Fimg2020.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1631689272&t=94c1d3733deea9a199cef462fa43deae "git流程图")

## Git配置
* `git config -l`查看配置列表
* `git config --golbal user.name xxxx` 配置全局用户名
* `git config --golbal user.email xxxx` 配置全局邮箱
*  若给每个仓库配置用户名和邮箱, 则进入每个仓库使用`git config user.name xxxx`, `git config user.email xxxx`
*  创建本地sshkey: `ssh-keygen -t rsa -C "Github注册邮箱"`复制id_rsa.hub的内容, 登录GitHub --> Account Setting  --> SSH-KEY --> Add SSH-KEY --> 粘贴id_rsa.pub中的内容.
*  验证: ssh -T git@github.com
        出现 Hi xxx! You've successfully authenticated, but GitHub does not provide shell access. 说明配置成功，可以连接上 Github

## 建立本地仓库

1. `git int` 创建本地仓库 `git init project_name`
2. `git remote add origin git@github.com:your_username/your_project_name`把本地仓库和远程仓库关联起来, 若不使用这个命令, 则每次`push`都需要指定远程服务地址
3. 修改本地远程地址仓库地址方法有三种
   1. 直接Modify:`git remote set-url origin new_url`
   2. 先Delete后Add:`git remote rm origin`,`git remote add origin new_url`
   3. 找到.git文件夹中config文件, 把旧的项目地址替换成新的地址
   4. `git remote -v`查看远程仓库地址

## Git提交本地修改到远程仓库

### git add
* `git add [file1][file2]...`**添加指定文件到暂存区**
* `git add [dir]` **添加目录到暂存区**
* `git add -A || git add .` **添加当前目录所有文件到暂存区**
* `git add -p` **添加每个变化钱, 都会要求确认, 对于同一个文件的多处变化, 可以实现分次提交**

### git rm
* `git rm xxx`**从本地仓库中删除指定文件**
* `git rm -r xxx`**从本地仓库中删除指定文夹**
* `git rm --cached [file]`**删除已缓存的文件**
* `git mv [file_original][file_renamed]`**改名文件并且将这个改名文件放入暂存区**

### git commit
* `git commit -m"注释"`**把本机缓存中的内容提交到本机的HEAD里面**
* `git commit -a`**提交暂存区内容到仓库区**
* `git commit -v`**提交时显示所有diff信息**
* `git commit --amend -m"注释"`**使用一次新的commit替代上一次提交,若代码没有任何新变化,则用来改写上一次commit提交信息**
* `git commit --amend[file1][file2]...`**重做上一次commit, 并包括指定文件的新变化**

### git push
* 在项目目录下使用`git push`推送本地仓库代码到远程仓库, 多人合作开发使用命令行建议先`git pull`, 若代码有冲突, 先解决冲突再`git push`, 若无冲突, 直接`git push`
* `git push [remote_branch_name] [tag_name]`**提交指定tag***
* `git push [remote_branch_name] [--tags]`**提交所有tag***
  
## Git状态查看
* `git status`**查看分支状态**
* `git branch`**查看本地所有分支**
   * `git branch -r`**查看远程所有分支**
   * `git branch -a`**查看本地和远程所有分支**
* `git tag`**列出所有tag**
* `git show [tag_name]`**查看tag信息**
* `git log [--stat]`**显示commit历史, 以及每次commit发生变更的文件**
* `git reflog`**显示所有操作的日志, 基本上包括对分支的任何更改, 若操作错了则可以根据此命令提供的信息通过充值HEAD来撤销改动**



## 操作Branch
* `git fetch`**获取远程仓库改动, 不会影响本地**
* `git pull`**是`git fetch`和`git merge`合体,先拉取后自动合并到本地分支**
* `git checkout [branch_name]`**切换到指定分支并更新工作区**
* `git merge [branch_name]`**合并指定分支到当前分支**
   1. **fast-forward(--ff)**
      ![](https://s1.ax1x.com/2020/04/08/GRTiT0.gif)
      [From大道至简](https://www.cnblogs.com/lzkwin/p/12658029.html)
   2. **no-fast-forward(--no--ff)**
      ![](https://s1.ax1x.com/2020/04/08/GRTZpF.gif)
      [From大道至简](https://www.cnblogs.com/lzkwin/p/12658029.html)
* `git checkout -b [branch_name]`**新建并切换至新分支**
* `git branch [new_branch_name]`**新建一个分支, 但依然停留在当前分支**
* `git branch --trach [branch_name] [remote_branch_name]`**新建一个分支与指定的远程的分支建立追踪关系**
* `git branch -D [branch_name]`**删除目标分支**
* `git branch -d -r [branch_name]`**删除远程分支**
* `git branch -m [old_branch_name] [new_branch_name]`**重命名分支,使用-M表示强制重命名**
   * 重命名远程分支
     * `git branch -m [old_branch_name][new_branch_name]`**重命名分支**
     * `git checkout -b [branch_name]`**新建并切换至新分支**
     * `git push origin -delete [old_branch_name]`**删除远程分支**
     * `git push --set-upstream origin [new_branch_name] 或者 git push -u origin [new_branch_name] `**推送本地分支到远程**

## Git分支与主干合并操作
* `git merge [branch_name][--squash]`**提交合并后的代码**
* `git commit -m"注释"`**把暂存区内容提交到本地仓库**
* `git push`**推送到远程仓库**

## Git强制覆盖本地代码与远程仓库保持一致
* `git fetch`
* `git reset --hard origin/master`
* `git pull`
* `git fetch --all && git reset --hard origin/master && git pull`**强制覆盖本地命令单条执行**


