---
title: git 学习笔记
date: 2019-06-16 21:25:40
tags:
- git
- github
categories:
- [版本控制]
- [工具使用]
---
Git是一个分布式版本控制系统（CVS），可以记录**文本文件**的**变化**（*修改、增加、删除等操作*），比如 TXT、HTML、程序源代码等，<span style="color:red;">不能记录其他文件（图片、音频等二进制文件）的变化，不能记录word文档的变化。</span>
<!--more-->

@[TOC]

## Git常见操作

### 配置相关

配置分为系统配置、全局配置和仓库配置，全局配置一般对该电脑的使用这都是有效的，也就对所有仓库有效了，仓库配置只对某个仓库有效。命令加`--global`是全局配置。

#### 查看配置

```bash
git config --global -l # 全局配置
git config --local -l # 仓库配置
```
#### 添加配置

```bash
git config --global --add <key> value
```
#### 删除配置

```bash 
git config --global --unset <key> # 删除全局 key 配置
```
#### 编辑配置文件

```bash
git config --global -e # -e 是 --edit 的别名，会在编辑器中打开配置文件。编辑后配置，配置就生效了。
```

###创建版本仓库

#### 创建新仓库

```bash
# 在某个文件下，该文件夹成为一个 git 仓库，生成一个 .git 文件夹
git init
git init <fileName> # 将 fileName 初始化为一个 git 仓库
# 帮助命令
git help <command>
```
```bash
$ ls -a
./  ../  .git/
```
*.git 文件夹内的内容不可轻易改动，里面记录了文件变化*
#### 克隆已有仓库

```bash 
git clone <repo> # 在某个文件内执行，会在该文件里生成一个仓库
git clone <repo> <directory> # 克隆仓库到指定目录
```
clone 做了4件事：
- `repo`的文件夹被创建；
- 仓库的所有文件和`提交`都被下载到`repo`内；
- 在`repo`文件内初始化一个git仓库；
- 一个远程分支`origin`被创建,关联了该URL；
- 本地有一个分支（默认名字为`master`）。
  **四种协议克隆**
```bash
# SSH 协议
git clone git@github.com:fsliurujie/test.git
# HTTPS 协议
git clone https://github.com/fsliurujie/test.git
# GIT协议
git clone git://github.com/fsliurujie/test.git
# HTTP 协议
git clone http:////github.com/fsliurujie/test.git
```
**ssh vs https**
ssh 和 http 是常见的两种克隆方法：
| 协议  | 是否配置SSH key | 速度 |        PUSH        |
| :---: | :-------------: | :--: | :----------------: |
|  ssh  |        √        |  快  | 仓库管理员无需密码 |
| https |        ×        | 较慢 |  需要用户名和密码  |

创建仓库时可能会用到的其他操作：
- **创建文件夹**: mkdir  fileName  创建目录
- **创建文件**: touch  documentName  创建文件
- **删除文件 ：** rm fileName  删除非空文件夹
- **强制删除非空文件 ：** *rm -rf  fileName*   f -force 强制删除文件（夹），没有提示 -r--recursive 递归删除 删除空文件可用这个

### 添加更改

添加跟踪的文件之前，一般需要设置排除跟踪的文件，即创建`.gitignore`文件。
不知道如何编写？复制来，按需修改即可。
[各种项目的gitignore模板](https://github.com/github/gitignore)
```bash
git add <fileName> # 添加某一个文件到暂存区
git add *.js # 增加所有 js 文件的修改
git add -u # git add --update 添加修改（modified)和删除（delete）文件（跟踪的文件），不添加（new）新增文件
git add . # 添加新增和修改，不添加删除
git add -A # git add -all, 合并 git add -u 和 git add . 即添加新增、修改和删除
```

### 重命名已跟踪的文件

```bash
git mv old-name new-name
```
该命令和直接重命名文件，然后 `git rm old-file`,`git add new-old`是一样的。

### 撤销修改

在 commit 提交前，不想添加修改，可`撤掉更改`---`文件修改会消失，这是非常危险的操作` *除非你确实清楚不想要那个文件了，否则不要使用这个命令。*
```bash
# -- 必须有，没有是分支切换命令
git checkout --file
```
- `add` 后修改，`checkout`回到**最近一次**add后的状态，还未 add 的更改消失；
- `commit`后修改，`checkout`回到**最近一次**commit 后的状态，更改消失；
- 总的来说，**checkout 是撤掉修改**。

添加文件也是一种修改，`checkout`的另一个作用是找回删错的文件：
```bash
git rm file # file 不是你想删除的文件，
git checkout --file # 找回误删的文件，但是最近一次 add/commit 后的修改丢失了
```

### 从暂存区撤回

-**从暂存区撤回**：git  rm --cached 

usage: git rm [<options>] [--] <file>...
-n, --dry-run         dry run
-q, --quiet           do not list removed files
--cached              only remove from the index
-f, --force           override the up-to-date check
-r                    allow recursive removal
--ignore-unmatch      exit with a zero status even if nothing matched

**撤回很多文件如何操作？？？**

### commit 相关操作

#### 提交更改到仓库
```bash
git commit -m  提交说明
git commit -a # 在编辑器中写提交说明
```
#### 合并 commit 

```bash
git log # 查看提交历史
```
当前历史：

```bash
commit a00766289ef.......
    待合并commit
commit 22281fca7bf.......
    第三方库和业务代码分开打包
commit 7828194fe69.......
    各页面分开打包
```

需要合并'待合并commit'和'第三方库和业务代码分开打包'

先 rebase 操作：
```bash 
git rebase -i 7828194f # 参数 i 是不需要合并的commit
```
执行完该命令，会进入交互模式：
```bash
p e2d34a8 脚本配置自定义参数
s 004fb27 修改README

# Rebase bd25857..004fb27 onto bd25857 (2 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```
`pick` 会执行 commit
`squash` 该 commit 会合并到前一个commit

把第二条命令改为`s`，保存并退出，会进入commit message 编辑界面，可编辑新的commit message。
```bash
# This is a combination of 2 commits.
# This is the 1st commit message:

脚本配置自定义参数

# This is the commit message #2:

修改README

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Fri Feb 15 17:31:30 2019 +0800
#
# interactive rebase in progress; onto bd25857
# Last commands done (2 commands done):
#    pick e2d34a8 脚本配置自定义参数
#    squash 004fb27 修改README
# No commands remaining.
# You are currently rebasing branch 'master' on 'bd25857'.
#
# Changes to be committed:
#	modified:   .gitignore
#	modified:   README.md
#	new file:   config/default.js
#	new file:   config/dev.js
#	modified:   package.json
#	modified:   webpack.config.js
#
```
### 修改比较 git diff
**git status** 命令是查看仓库状态，文件是否被修改，修改会哪些文件还没提交等，不知道修改的具体内容。
-**查看修改的内容**：git diff  比较的是 暂存区的文件和本地文件。
  **有变化的内会用红色和绿色标记**

### 版本回退 

#### 查看提交记录

```bash
git log 
git log  --pretty=oneline  #在一行显示提交记录
```
#### 版本回退

**HEAD** : 表示当前版本。
我们可回退到其他版本。
*HEAD^* ：上一个版本，*HEAD^^* ： 上上个版本。
*HEAD~n* ：往前第 n 个版本。
```bash
git reset --hard <commitID> # 回退到 commitID 为版本 推荐使用
git reset --hard HEAD~n # 回退到往前第 n 个版本
```
回退我反悔了，又想回到之前的版本呢？

1. 查看 commitID:

```bash
git reflog
```
2. 版本穿梭

```bash
git reset --hard commitID # 根据commitID 回退
```

## 工作区和暂存区

- **工作区（工作目录）** : 本地电脑文件夹，该文件夹里有你需要版本控制的文件。
- **版本仓库Repository**：工作区里有一个隐藏的 .git 文件夹，这不是工作区，这是 Git 仓库。
- **暂存区Stage**：版本仓库里有暂存区区，Git add 先添加到这里。
- **分支** :  仓库里有一个分支，git commit 把修改从暂存区提交到分支，一本版本仓库里可能有多个分支。
  ![Alt text](./工作区---暂存区---版本库.bmp)

### status 和 diff 比较的是哪些？

#### diff 的比较

diff 可比较工作区和暂存区、工作区和HEAD、暂存区和 HEAD、两个分支、两个提交、两个文件、两个 bolb之间的差异。
##### 工作区和暂存区的比较

```bash
git diff # 比较所有文件
git diff -- file1 file2 # 比较个别文件
```
##### 暂存区和 HEAD 比较
```bash
git diff --cached  # 暂存区和HEAD的比较 比较所有文件
git diff --cached -- file1 file2 比较个别文件
git diff --staged  # 暂存区和HEAD的比较
```

##### 工作区和 HEAD 比较

```bash
git diff HEAD # 比较所有文件
git diff HEAD -- file1 file2 # 比较个别文件
```

##### 分支比较

```bash
git diff branch-name # 分支 branch-name 的 HEAD 和当前分支 HEAD 比较
git diff branch-name1 branch-name2 # 两个分支的 HEAD 比较
git diff HEAD HEAD~1 # 当前 HEAD 和上一个 HEAD 比较（两个 commit 的比较）
git diff commitID1 commitID2 # 两个 commit 的比较
```

##### 输出比较结果

```bash
git diff >> diff.txt # 将比较结果输出到 diff.txt 文件中
```

#### status 的比较

实验：
-  **修改→status**：文件被修改，还没提交到暂存区。可以 git add，或者 git checkout --file 放弃更改 ;
-  **修改→add→status**：提示更改没commit，可以git reset回退版本。
-  **修改→add→修改→status**：提示更改没commit，可以git reset回退版本。 提示 文件被修改，还没提交到暂存区。可以 git add，或者 git checkout --file 放弃更改 ；
-  **修改→add→commit→status**：工作区干净。

**结论：** 
1. **提示没有commit，比较的是暂存区和分支，可进行commit 或者 reset ;**
2. **提示没有 add，比较的是工作区和暂存区，可 add 到暂存区和 checkout ，放弃更改；**
3. **status 比较的是工作区和暂存区，暂存区和分支，<span style="color:red;">不比较工作区和分支</span>。**

### 撤销修改

- **撤销修改**:git checkout -- file， 文件回到最后一次 add 的状态。
  两种情况：
  1.**修改后还没add**：回到最近的版本库；
  2.**修改后已add** : 回到 add 之前的状态。
><span style="color:red;"> - - 很重要，没有它就不是撤销修改，而是切换分支</span>

>git checkout -- . 撤销所有修改
>git checkout path/../file 撤销文件下中某个文件的修改

*git reset HEAD file* 可以回退版本，也可从暂存区回到工作区。

### 删除文件

**在工作区删除文件**：rm file
在工作删除文件后，工作区和仓库版本不一致了，可以有两个操作：
1. 误删了，就把版本库里的最新版本，拿到工作区。 **git  checkout -- file**
2. 确认删除，那再把版本库里的文件删除。**git rm file**

><span style="color:red;">注意 git rm file 和 rm 的区别：</span>
>**git rm file**:删除暂存区和工作区，需用用 **git checkeout HEAD -- file** 或者 **git checktout HEAD file**分支恢复。

## 远程仓库

1. 在 Github 上创建仓库并关联本地仓库
  Git 是分布式版本控制系统，同一个 Git 版本仓库，可分布到不同的机器上，这些机器上的版本库都是一样的。现在只有一台机器，如何分布？
  **①：在 Github 上创建仓库，将代码推送到该仓库。**推荐做法
  ②：分布在同一台机器上的不同目录，硬盘坏了所有版本库都没了，不推荐，（也不知道怎么弄）。
  ③：建一台服务器，从该服务器上克隆版本库，提交版本库到该服务器。成本高，适合公司多人协作开发。

### 关联远程仓库

```bash
git remote add origin <repoUrl>
```
**origin** 是默认的**远程仓库**的名字，可取其他名。远程仓库可有多个分支。

>
>	“origin” 并无特殊含义
>	远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。 同时 “master” 是当你运行 git init 时默认的**起始分支名字**，原因仅仅是它的广泛使用，“origin” 是当你运行 git clone 时默认的**远程仓库名字**。 如果你运行 git clone -o booyah，那么你默认的远程分支名字将会是 booyah/master

### 查看远程仓库

```bash
git remote -v # 显示所有远程仓库
```

### 删除本地分支和远程分支的关联

```bash 
git remote remove origin
```

### 推送本地修改到远程仓库
**第一次推送本地仓库到远程仓库**
```bash
git push -u origin master  # master 是分支名字
```
**非第一次推送**

开始工作前，远程仓库可能有更新,需要先**合并**。

### 分支合并

#### 合并分支方法一（推荐）
1. 获取远程仓库`某个分支的更改`：

```bash
git fetch origin <remote-branch>:<temp-branch> 
```
该命令做了两件事情：
    ①. 创建本地分支 temp-branch;
    ②. 将`remote-branch`上的更新保存在 temp-branch 中

2. 与当前分支比较不同：

```
git diff <temp-branch> 
```
3. 将更改`合并`到`当前分支`：

```bash
git merge <temp-branch> # <temp-branch> 是合并进`当前分支`的分支
```
合并成功会没提示，并且在`当前分支`上有一个合并进来的分支的`提交记录`。

#### 合并分支方法二

1. 获取更改

```bash 
git fetch <remote> # 获取远程仓库的更改，是哪个分支呢？remote 远程仓库名字，一般是 origin
```

2. 合并

```bash
git merge <remote>/<local-branch> # 将更改合并进入local-branch
```
#### 分支合并方法三

以上两个操作合并为一个操作：
```bash
git pull <remote> <local-branch>
```
出现`Already up to date.`,则合并成功。

`pull`会自动合并，执行该命令前，需要确保当前分支的修改都提交了，需要提交的修改，可能会使你的修改丢失。执行成功后，有冲突，会显示冲突的文件。
如果你不想自动合并，可执行`git merge --abort`撤销`pull`。

以上参考：[fetching-a-remote][1]

执行`git merge --abort`可能会出现`fatal: There is no merge to abort (MERGE_HEAD missing).`的提示，说明`撤销合并`没成功。（因为合并成功，合并分支被删除了？）

可用 `git reset --merge`来`撤销合并`。

注意：当`MERGE_HEAD`存在，`git reset --merge`和`git merge --abort`是相同的。

参考：[undoing git merge after merge.renameLimit warning plus conflicts][2]

合并完后，进行编辑，提交修改，最后`推送`：
```bash
git push <remote> <remote-branch> # <remote-branch> 是你工作的远程分支名字，可能别人和你在该分支是上工作
```

### 强制覆盖合并
```bash
git fetch --all  
git reset --hard origin/master 
git pull
```
## 如何避免每次输入密码?

执行某些操作，Git 服务器会询问用户名与密码。可在本地设置保存密码，提高效率。

1. 短期凭证存储
  凭证保存在**内存中**，几分钟内有效。
```bash
git config --global credential.helper cache
```
2. 长期凭证存储
  凭证保存在硬盘内，长期有效。
```bash
git config --global credential.helper store
```

##分支管理

### 查看分支

git branch [-r] [-a] [-v]
```bash
git branch -av # 推荐使用，可参看本地分支和远程分支，并且可看最近的提交
```
```bash
git branch  # 查看本地分支
```
```bash
git branch -r # 查看远程分支
```
```bash
git branch -a # 查看所有分支
```

### 新建分支

```bash
git branch [-f] new-branch  # 新建分支 -f 可选
```
```bash
git checkout new-branch  # 切换分支
```
```bash
git checkout -b new-branch # 新建并切换
```

### 删除本地分支

```bash
git branch -D need-delete-local-branch  # 在当前分支下删除自身，不可删除
```
删除错了，恢复：
```bash
git reflog  # 查看操作日志，找到需要恢复的commit id
```
```bash
git branch need-recover-branch commitId  # 恢复分支
```
本地分支和远程分支都删除了，怎么恢复？
先恢复本地分支，再 **push** 一次，即恢复了本地和远程分支

### 删除远程分支

远程以` remote/branch` 形式命名。 例如，如果你想要看你最后一次与远程仓库 origin 通信时 master 分支的状态，你可以查看 origin/master 分支。
```bash
git branch origin -d remote-branch
```
- git branch -r -d origin/remote-branch-name
- git branch -d -r origin/remote-branch-name

```bash
git push origin --delete origin/<remote-branch>
git push origin :<remote-bracnh>
# 给出本地分支 是推送远程分支
git push origin <local-branch>:<remote-bracnh>
```
>删除了，但是产生一个问题：其他成员在pull代码时，不会把删除本地的分支
>fetch 时 加 -p
>git fetch -p

### 重命名分支

删除远程分支→重命名本地分支→ push 分支到远程仓库
本地分支重命名：
```bash
git branch -m  old-branch-name new-branch-name # 重命名当前分支，可省略 old-branch-name
```
push 新分支分支到远程仓库：
```bash
git push origin new-branch-name 
```

## 已跟踪的文件取消跟踪

有的文件忘记加入到 `.gitignore`，`git add .`后Git 就会默认追踪该文件，追踪后取消追踪的操作：
1. 从追踪列表中删除：
```bash
# 删除某个文件
git rm --cached <file>
# 可加 -r 选项 表示递归删除 带目录时加 -r
git rm -r --cached <directory>
# 删除整个目录
git rm --cached . 
# 删除某个目录下的所有文件
# * 加 \ 是为了从 git 命令中转译
git rm --cached src/\*
```
该操作 从仓库索引中**取消文件跟踪**，文件**仍然保留在工作目录中**。
2. 增加刚才的文件或者目录到 `.gitignore`
3. 提交更改
```bash
git add .
git commit -m '文件取消跟踪'
```
3. 推送至远程
  推送至远程后，远程仓库不再用取消忽略的文件。
>[Tell Git not to track a file any more (remove from repo)](https://alvinalexander.com/git/git-rm-file-git-repository-dont-track-accidentally-added)
>
>[How to make Git “forget” about a file that was tracked but is now in .gitignore?](https://stackoverflow.com/questions/1274057/how-to-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore)

## 删除已跟踪的文件

希望从工作目录中**删除**已跟踪的文件，必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。
0. 修改`.gitingnore`
1. 删除文件跟踪
```bash
git rm <file>
git rm -r <dir>\*.md
```
执行该操作，项目仓库中不再有该文件。
2. 提交更改
```bash
git add .
```

## .gitignore 文件相关

### 忽略规则

`.gitignore` 列出的`目录`或者`文件`是不被 git 跟踪的。常常需要忽略的文件如下：
1. 临时资源，比如缓存、日志、编译后的源码等；
2. 开发环境配置文件，不同的开发人员有不同的开发习惯，环境配置文件不该被跟踪；
3. 敏感信息，比如数据库密码、秘钥等。

匹配规则：会以 `.gitignore` 所在目录为顶级目录或者当前目录，**递归**地匹配路径或文件，后面的规则会覆盖前面的规则。

一个文件或者目录被忽略，**不会**有以下行为：
1. 被 git 跟踪；
2. 反映到 `git status` 或 `git diff`等命令中；
3. 被暂存，比如 `git add -A` 不会暂存被忽略的文件。

#### 常见的规则模式

`#` 是注释，注释不能和规则同行，否则规则失效。规则含有 `#`，使用反斜线 `/` 转义。
1. 常用规则

```bash
# 某类型文件，使用扩展名
*.js
*.log
# 排除特定文件
!index.js
# 特定文件
index.css
test.json

# 目录及其内容
node_modules/
# 当前目录下的某个目录 .gitignore 所在目录为顶级目录
/node_modules/

# 任意目录或文件
node_modules
# 当前目录下的任意目录或文件
/node_modules
```
2. 高级规则

```bash
# 子目录 两层关系
target/logs/
# 孙子目录 三层关系
target/*/logs/
# 后代目录 任意层关系
target/**/logs/

# 可选字符匹配 
# 匹配 build/Build 文件或目录 Build 不会匹配 uild
[bB]uild 
# 匹配 .pyc 或 .pyo
.py[co]

# 特定目录下文件
target/*/.bin/*.js
# 目录匹配通配符
# 匹配 .deploy_git .deployB
.deploy*/
```

#### 其他忽略方式

`.gitingore` 文件应该被提交到仓库里，但是会暴露你的忽略规则，如果不想暴露，可使用以下方式设置：
- .git/info/exclude 文件配置；
- 配置全局忽略文件。

### 常见项目的 .gitignore 文件

- [常见gitignore模板](https://github.com/github/gitignore)
- [在线生成gitignore文件](https://www.gitignore.io/)

### 列出忽略的文件

1. git status

```bash
git status --ignored
```
2. git ls-files

```bash
git ls-files --others -i --exclude-standard
```

### 删除忽略文件

`git clean -x` 可删除忽略文件,和 `git reset --hard` 一起使用可完全回到某个 commit 状态。

```bash
git clean -n # clean 演练，告知哪些文件会被删除
# 删除当前目录下所有没有 track 的文件，不删除 .gitignore 文件里的文件
git clean -f  <path> # 指定路径 path

# 删除当前目录下没有被 track 的文件和目录
git clean -df

# 所有没有被 track 的目录和文件，不管 .gitignore 是否匹配
git clean -xf
# 
```


### 列出被跟踪的文件
```bash
git ls-tree -r master --name-only
```

## 常见问题

### 本地仓库版本落后于远程仓库版本

当push到远程仓库时，出现以下错误：

```bash
 Updates were rejected because the tip of your current branch is behind
```
解决办法：
- 强推（远程修改会丢失）

```bash
git push origin -f master
```
- 先合并，再推送（待验证）

```bash
git pull origin master
git push origin master
```
- 不想合并，先建分支，再推送（待验证）

```bash
git branch <branch-name>
git push origin <branch-name> # 待验证
```

### Git推送提示：remote origin already exists

1. 删除远程仓库 (删除报错，可在配置文件中删除仓库信息)
> git  remote rm origin

2. 在添加远程仓库
> git remote add origin repo-url

3. 再次推送
> git push -u origin master

## todo

>[git relog VS git log](https://wjp2013.github.io/tool/git-reflog-git-log-git-cherry-pick/) 
>[Undo changes in Git - Cheat sheet for git checkout, stash, reset, clean, revert, rebase -i, amend](https://dev.to/mzanggl/undo-changes-in-git-cheat-sheet-for-git-checkout-stash-reset-clean-revert-rebase-i-amend-2h1h)
### git 进阶
>[A Hacker's Guide to Git](https://wildlyinaccurate.com/a-hackers-guide-to-git/)

_______________________________________________
> [Git – Fast Forward 和 no fast foward](https://my.oschina.net/yuzn/blog/82297)


[1]: https://help.github.com/articles/fetching-a-remote/
[2]: https://stackoverflow.com/questions/10737966/undoing-git-merge-after-merge-renamelimit-warning-plus-conflicts/10738171