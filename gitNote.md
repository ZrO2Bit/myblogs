# git学习笔记

### 1. git版本管理

##### 1.1 提交更改到git

  1. `git add <fileName>`，将文件放进暂存区（stage） ，也可以指令目录将目录内文件全部放入暂存区 ， 如`git add .`；
  2. 使用命令`git commit -m <message>`，添加到版本库 ，即把暂存区的所有内容提交到当前分支 ，其中<message>是本次提交的描述；

##### 1.2 查看仓库状态
​		 `git status`命令可以随时查看当前的状态 （显示有变更的文件），此命令的几种结果如下：

  1. `Changes not staged for commit`：文件更改了，但是还未进入暂存区 ，需要add；
  2. `Changes to be committed`：文件已进入暂存区，但还未提交到版本库，需要commit；
  3. `Untracked files`：表示该文件还从来没有被添加进版本库，这是新添加的文件；

##### 1.3 查看修改内容
  如果`git status`显示文件被修改过，用`git diff`可以查看修改内容，语法为：`git diff <fileName>`

1. `git diff`是工作区(work dict)和暂存区(stage)的比较，比较的是工作区文件与暂存区文件的区别（上次git add后的内容）的区别。

2. `git diff --cached`是暂存区(stage)和分支(master)的比较，比较的是暂存区的文件与仓库分支里（上次git commit后的内容）的区别 。

##### 1.4版本控制
 	一旦文件改乱了，或者误删了文件，可以从最近的一个commit恢复，然后继续工作。

  1. `git log` 可以查看提交历史记录（即查看版本库的状态 ），显示从最近到最远的提交日志；git log --pretty=oneline 使每个日志单独成行（简化版输出）。

  2. git中，HEAD指向当前最新的版本。

  3. 回退到上一个版本：`git reset --hard HEAD^`，HEAD^表示回退1个版本，HEAD^^表示回退2个版本，HEAD~100表示回退100个版本。

  4. 返回过去/未来的版本：`git reset --hard <commit_id>`。其中<commit_id>可以只写id的前几位。
  5. `git reflog`查看历史每一次命令，可以查看每一次提交时的commit id，可以找回已经回退的版本或删除的分支中的版本

##### 1.5撤销修改
  1. 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- <file>，这里有两种情况：

     若文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
     若文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
     总之，就是让这个文件回到最近一次git commit或git add时的状态。

 2. 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，把暂存区的修改撤销掉（unstage），重新放回工作区 ，回到了场景1。第二步按场景1操作，丢弃工作区的修改即可。

​		注：git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

 3. 已经提交了不合适的修改到版本库时，想要撤销本次提交，可以回退到上一个版本。

### 2.远程仓库
  Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。 每个人都从“服务器”仓库上克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。
  本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以需要创建SSH Key` ssh-keygen -t rsa -C "***@***.***"` 。此时可以在用户主目录里找到.ssh目录，id_rsa.pub是公钥，添加到远程仓库中

##### 2.1 添加远程库

​		在本地的learnGit仓库下运行如下命令（先有本地库时，将本地库和远程库关联），此时远程库的名字就是origin。

​		` git remote add origin ***@***.***:***/***.git`

##### 2.2 推送到远程库

​		把本地库的所有内容推送到远程库上 ，实际上是把当前分支master推送到远程。 由于远程库是空的，我们第一次推送master分支时，加上-u参数，Git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支的upstream设置为远程仓库的master分支。

`git push -u origin master`   第一次推送

`git push origin master`         之后的推送

`git push -u origin br:br1` 推送本地的br分支到远程仓库的br1分支

`git push -u origin br :br1` 推送本地的br分支到远程仓库的br分支，同时推动空分支到远程的br1分支，即删除br1分支

##### 2.3 从远程库克隆
`git clone ***@***.***:***/***.git`

##### 2.4 设置upstream

`git branch -u origin/***`即分支推送时推送到的分支

##### 2.5 从远程库拉取

`git pull`从远程仓库拉取当前upstream的分支，相当于`git fetch`+`git merge ` 

### 3.分支管理
##### 3.1 分支与管理
每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。

Git鼓励大量使用分支：

    1. 查看分支：`git branch`
    2. 创建分支：`git branch <name> `，切换分支：`git checkout <name>`
    3. 创建+切换分支：`git checkout -b <name>`
    4. 合并指定分支到当前分支：`git merge <name>`
  5. 删除分支：`git branch -d <name>`
    6. 切换分支：`git switch <name>`

##### 3.2 解决冲突

  冲突的本质是：不同的分支修改了代码相同的部分，从而导致合并分支时出现冲突（虽然冲突，但还是会合并，会自动标注出冲突的地方）。所以解决冲突就需要我们把Git合并失败的文件进行手动修复，改为我们想要的内容再提交（add + commit）即可。

  可以用带参数的git log查看分支的合并情况（分支合并图）： 

​		`git log --graph --pretty=oneline --abbrev-commit`