# git学习记录

本篇为廖雪峰老师官网中Git教程的学习笔记，由于临近考试周，各种ddl，所以内容较少~~（暑假就可以认真学习了)~~

[Git教程-廖雪峰]([Git教程 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600))



## Git 简介

Git是目前世界上最先进的**分布式**版本控制系统

每个开发都可以从master上克隆一个本地版本库，就算没有网络，也可以提交代码到本地仓库、查看log、创建项目分支等



## 创建版本库

版本库(repository)也叫仓库，可以看做一个目录，该目录内的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪

1. 选择一个合适的地方，创建一个空目录(不含中文)：

   ```bash
   $ mkdir learngit //创建一个名为learngit的空目录
   $ cd learngit    //跳转至learngit目录中
   $ pwd           //用于显示当前目录
   /d/git/Git/learngit
   ```

2. 把创建好的目录变成Git可以管理的仓库：

   ```bash
   $ git init   //将当前目录转换成一个Git仓库
   $ git init <directory>  //运行这个命令回创建一个名为directory只包含.git子目录的空目录
   $ git init --bare <directory.git> //初始化一个裸的Git仓库，但是忽略工作目录。共享仓库应该总是用--bare标记创建
   $ ls -ah  //查看当前目录下的文件，包含隐藏文件(不带-ah看不了隐藏文件)
   ```

3. 把一个文件放到Git仓库

   ```bash
   $ touch readme.txt  //创建一个readme.txt文件
   $ vim readme.txt   //编辑readme.txt文件
   $ git add readme.txt  //把文件添加到缓存区
   $ git commit -m "wrote a readme file" //把文件提交到仓库，""内的内容为此次commit的说明
   ```

   - 可以多次`add`不同的文件，`commmit`可以一次提交多个文件

     ```bash
     $ git add file1.txt
     $ git add file2.txt file3.txt
     $ git commit -m "add 3 files."
     ```

   - git命令将工作目录中的变化添加到缓存区，但实际上并不会改变你的仓库，直到你运行`git commit`。

   - 使用这些命令时，要随时掌握工作区的状态，使用`git status`命令，`git diff`可以查看修改的内容

     - `git status`命令显示工作目录和缓存区的状态。可以列出已缓存、未缓存、未追踪的文件。
     - 若想查看已提交到仓库的项目信息，可以使用`git log`命令
     - `git  diff`或`git diff <file>`查看工作区 和 暂存区的区别
     - `git diff --cached`查看暂存区和分支(master)的区别
     - `git diff HEAD -- <file>`查看 工作区和版本库里面最新版本的区别

   - 若有add许多同种类型的文件，可以使用通配符* （加引号） ，例如：git add "*.txt" 命令就是添加所有.txt文件至缓存区



## 常见问题解决

1. 

   ```bash
   warning: LF will be replaced by CRLF in readme.txt.
   The file will have its original line endings in your working directory
   ```

   - 问题原因：git在window下，默认是CRLF作为换行符，git add提交时，检查文本中有LF换行符(Linux系统里面的)，则会给出warning，

   - 解决办法：让git忽略该检查即可

     ```bash
     $ git config --global core.autocrlf false
     ```

2. 

Q：输入`git add readme.txt`，得到错误：`fatal: not a git repository (or any of the parent directories)`。

A：Git命令必须在Git仓库目录内执行（`git init`除外），在仓库目录外执行是没有意义的。

Q：输入`git add readme.txt`，得到错误`fatal: pathspec 'readme.txt' did not match any files`。

A：添加某个文件时，该文件必须在当前目录下存在，用`ls`或者`dir`命令查看当前目录的文件，看看文件是否存在，或者是否写错了文件名
