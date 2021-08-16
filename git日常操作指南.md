# git日常操作指南

# git 配置
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

## git提交本地修改到远程仓库

### git add -- 将改动添加到本地仓库

* `git add [file1][file2]...`**添加指定文件到暂存区**
* `git add [dir]` **添加目录到暂存区**
* `git add -A || git add .` **添加当前目录所有文件到暂存区**
* `git add -p` **添加每个变化钱, 都会要求确认, 对于同一个文件的多处变化, 可以实现分次提交**

### git rm xxx -- 从本地仓库中删除指定文件
* `git rm -r xxx`**从本地仓库中删除指定文夹**
* `git rm --cached [file]`**删除已缓存的文件**
* `git mv [file_original][file_renamed]`**改名文件并且将这个改名文件放入暂存区**

### 
* `git commit -m"注释"`**把本机缓存中的内容提交到本机的HEAD里面**
* `git commit -a`**提交暂存区内容到仓库区**
* `git commit -v`**提交时显示所有diff信息**

  

 