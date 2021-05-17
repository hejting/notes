## Git常见命令
阮一峰常见命令清单：https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html
![avatar](picture/git_command.png)
#### 一、新建代码库
```shell
# 在当前页面新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project_name]

# 下载一个项目和他整个代码历史
$ git clone [url]
```
#### 二、配置
```shell
# 显示当前配置
$ git config --list

#编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "username"
$ git config [--global] user.email "email"
```
#### 三、增加删除文件
```shell
# 添加指定文件到暂存区
$ git add [file1] [file2]

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前都会要求确认
# 对于同一个文件的多处变化可以分批提交
$ git add -p

# 删除工作区文件，并将此次删除放入暂存区
$ git rm [file1] [file2]

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改文件名，并将此次修改放入暂存区
$ git mv [file-original] [file-renamed]
```
#### 四、代码提交
```shell
# 提交暂存区到仓库
$ git commit -m [message]

# 提交暂存区的指定文件到仓库
$ git commit [file1] [file2] -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有的diff信息
$ git commit -v

# 使用一次新的commit替代上一次提交
# 如果代码没有新的变化，则用来改写上次的commit信息
$ git commit --amend -m [message]

# 重做上次的commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2]
```
