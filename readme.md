# git 

## 1.简介

* git是目前最先进的分布式版本控制系统，不必联网、强大分支管理等
* CVS、SVN是免费的集中式的版本控制系统，但其速度慢、需联网。

### 1.1安装

* `linux:`

  `sudo apt-get install git`

* `mac:`

  安装Xcode->"Preferences"->"Download"->"Command Line Tools"

* `windows:`

  [下载安装](https://git-scm.com/downloads)->在开始菜单里找到“Git”->“Git Bash”，蹦出命令行窗口->安装成功->设置：

  ```cmd
  $ git config --global user.name "Your Name"
  $ git config --global user.email "email@example.com"
  ```

### 1.2创建版本库

​	版本库又名仓库repository，可理解为一个目录，该目录下的所有文件都能被git管理跟踪，以便任何时候都可以追踪历史。

#### 创建版本库

* 创建一个空目录
* 到其目录下，通过 `git init`命令变成git可以管理的仓库，可看到`.git`目录

#### 把文件添加到版本库

所有的版本控制系统只能跟踪文本文件的改动，二进制格式文件只能跟踪大小变换

* 编写一个文件readme.md
* 使用命令`git add readme.md`把文件**添加**到仓库
* 使用命令`git commit -m "readme"`提交**提交**到仓库，`-m`后面输入的是本次的提交说明

为什么添加问价需要`add、commit`共两步？因为`commit`可以一次提交很多文件，所有可以多次`add`再`commit`

## 2.时光穿梭

### 2.1版本回退

* `git status`查看仓库当前状态
* `git diff filename`查看具体修改什么内容

每当觉得文件修改到一定程度的时候，就可以保存一个“快照”，这个快照在Git中被称为`commit`

* `git log`显示从最近到最远的提交日志，加上`--pretty=online`简化输出
  * `1094adb...`是`commit id`版本号，和SVN不同，其不是递增的数字，SHA1计算出来的一个非常大的数字，用十六进制表示。
  * 为什么`commit id`需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。

Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

* `git reset --hard commit_id`回退版本：`git reset --hard HEAD^`

* `git resert --hard commit _id`回到未来：`git reset --hard 1094a`版本号没必要写全
* `git reflog`用来记录每一次命令，可用来确定要回到未来哪个版本

### 2.2工作区和暂存区

git个其他版本控制系统SVN的一个不同指出就是有暂存区的概念。

#### 工作区

就是在电脑能看到的目录

#### 版本库

​	工作区有一个隐藏目录`.git`这个不算工作区，而是git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

​	前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：

* 第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

* 第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

* `git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

### 2.3管理修改

为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。

* 第一次修改 -> `git add` -> 第二次修改 -> `git commit`

Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

* 第一次修改 -> `git add` -> 第二次修改 -> `git add` -> `git commit`

第二次修改提交了。

### 2.5 撤销修改

#### ` git checkout -- file`

可以丢弃工作区的修改，这里有两种情况：

* 一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

* 一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

#### `git reset HEAD <file>`

修改只是添加到了暂存区，还没有提交.用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区

#### summary

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 2.6删除文件

从版本库中删除文件，那就用命令`git rm`删掉，并且`git commit`

若删错了，因为版本库里还有呢，所以可以`git checkout -- file`很轻松地把误删的文件恢复到最新版本

## 3.远程仓库

### 3.1添加远程库

​	在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作。

* 本地git仓库关联远程仓库

```cmd
git remote add origin git@server-name:path/repo-name.git
```

* 本地初次推送若已经有ssh-key，并添加到GitHub账户SSH Keys中，则直接进行推送

```cmd
git push -u origin master
```

* 若本地无ssh，向远程仓库推送会报错，提示无权限，此时本地cmd指令

```cmd
ssh-keygen -t rsa -C "邮箱地址"
```

完成操作可生成ssh，打开生成的`id_ras.pub`文件，将生成ssh密匙存入远程仓库帐号，即可向远程仓库推送。

* 验证`ssh -T git@github.com`,显示`Hi ...! You've successfully authenticated, but GitHub does not provide shell access.`

### 3.2从远程库克隆

* 用命令`git clone`克隆一个本地库，例如：

```cmd
$ git clone git@github.com:userName/projectName.git
```

* github允许你本地仓库有的东西，远程仓库里没有，但不允许远程仓库有的东西，你本地仓库没有。同步命令：

```cmd
$ git pull --rebase origin master
```

### 3.3summary

* 克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

* Git支持多种协议，包括`https`，但`ssh`协议速度最快。

## 4.分支管理

​	假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

​	SVN的分支管理速度很慢，Git的分支管理速度极快。

### 4.1创建与合并分支

