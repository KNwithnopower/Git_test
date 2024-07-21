# 1.创建版本库
* 初始化一个Git库：使用```git init```命令。
* 添加文件到Git仓库，分两步：
    * 使用命令```git add <file>```，注意，可反复多次使用，添加多个文件；
    * 使用命令git ```commit -m <message>```，完成。
# 2. 时光穿梭机
* ```git status```命令可以让我们时刻掌握仓库当前的状态
* ```git diff```顾名思义就是查看difference，能看看具体修改了什么内容
## 版本回退
* 用```git log```命令查看从最近到最远的提交日志
  * 如果嫌输出信息太多，看得眼花缭乱的，可以试试加上```--pretty=oneline```参数：
  * 一大串类似```1094adb...```的是```commit id```（版本号）
  * 用```HEAD```表示当前版本,上一个版本就是```HEAD^```，上上一个版本就是```HEAD^^```，当然往上100个版本写100个^比较容易数不过来，所以写成```HEAD~100```。
* 使用```git reset```命令回退
  * ```git reset --hard HEAD^```
  * ```git reset --hard 1094a```
  * Git提供了一个命令```git reflog```用来记录你的每一次命令
## 工作区和暂存区
* 工作区
  * 就是你在电脑里能看到的目录，比如我的learngit文件夹就是一个工作区
* 版本库
  * 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库(```ls -ah```可以查看到)
  * Git的版本库里存了很多东西：
    * 最重要的就是称为stage（或者叫index）的暂存区
    * 还有Git为我们自动创建的第一个分支master
    * 以及指向master的一个指针叫HEAD。
  * 把文件往Git版本库里添加
    * 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
    * 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
## 管理和修改
* Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件
  * 第一次修改 -> git add -> 第二次修改 -> git commit：git commit只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。
## 撤销修改
* ```git checkout -- file```可以丢弃工作区的修改：
  * 命令```git checkout -- readme.txt```意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
    * 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
    * 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
    * 总之，就是让这个文件回到最近一次git commit或git add时的状态。
* ```git reset HEAD <file>```可以把暂存区的修改撤销掉（unstage），重新放回工作区
## 删除文件
* rm文件后，```git status```命令会立刻告诉你哪些文件被删除了：
  * 选择一是确实要从版本库中删除该文件:
    * 那就用命令```git rm```删掉，并且```git commit```
  * 另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
    * ```git checkout -- test.txt```
## 远程仓库
* 创建远程仓库
  * 第1步：创建SSH Key。```ssh-keygen -t rsa -C "youremail@example.com"```*
    * 在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
    * 登陆GitHub，打开“Account settings”，“SSH Keys”页面,点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：
* 现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作
  * ```git remote add origin git@github.com:michaelliao/learngit.git```
  * ```git branch -M main```
  * ```git push -u origin main```
  * 若不成功，首先输入```ssh -T -p 443 git@ssh.github.com```若显示not provide shell access，则在config文件中添加以下内容,没有config的话自己新建一个(就在你的.ssh文件夹内)，把后缀名删掉即可：
        ```
        Host github.com

        Hostname ssh.github.com
        
        Port 443
        
        User git
    再运行就没问题了
* 如果添加的时候地址写错了，或者就是想删除远程库，可以用```git remote rm <name>```命令。使用前，建议先用```git remote -v```查看远程库信息：
## 从远程库克隆
* 假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。
    * 创建一个新的仓库
    * git clone git@github.com:michaelliao/gitskills.git
# 分支管理
## 创建与合并分支
* 截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。
  * 一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点
  * 每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长。
  * 当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变
  * 在dev上的工作完成了，就可以把dev合并到master上。最简单的方法，就是直接把master指向dev的当前提交，就完成了合并
  * 合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支。
* 实操
  * 首先，我们创建dev分支，然后切换到dev分支：```git checkout -b dev```或```git switch -c <name>```git checkout命令加上-b参数表示创建并切换,相当于```git branch dev```,```git checkout dev```或```git switch <name>```
  * 然后，用```git branch```命令查看当前分支,git branch命令会列出所有分支，当前分支前面会标一个*号。
  * 然后，在修改后，我们就可以在dev分支上正常提交
      ```
      git add readme.txt 
      git commit -m "branch test"
  * dev分支的工作完成，我们就可以切换回master分支
  ```git checkout master```
  * 现在，我们把dev分支的工作成果合并到master分支```git merge dev```
  * 合并完成后，就可以放心地删除dev分支了```git branch -d dev```
