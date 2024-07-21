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
## 分支管理策略
* 如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
*  --no-ff方式的git merge
```git merge --no-ff -m "merge with no-ff" dev```
*  分支策略
   *  首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
   *  干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
   *  你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
## bug分支
* 工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug：Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作
  * ```git stash```
  * 首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支
  
        git checkout master
        git checkout -b issue-101
   * 修复完成后，切换到master分支，并完成合并，最后删除issue-101分支:

         git switch master
         git merge --no-ff -m "merged bug fix 101" issue-101
    * 接着回到dev分支干活:
          
          git switch dev
          git status
    * 用git stash list命令看刚才的工作现场,Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
      * 一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
      ```  git stash apply stash@{0}```指定stash恢复
      * 另一种方式是用git stash pop，恢复的同时把stash内容也删了：
* 同样的bug，要在dev上修复，我们只需要把4c805e2 fix bug 101这个提交所做的修改“复制”到dev分支。注意：我们只想复制4c805e2 fix bug 101这个提交所做的修改，并不是把整个master分支merge过来。
  * 为了方便操作，Git专门提供了一个cherry-pick命令，让我们能复制一个特定的提交到当前分支：

## Feature分支
* 添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。
* 开发代号为Vulcan的新功能:
  * ```git switch -c feature-vulcan```
  * ```git add vulcan.py```
  * ```git status```
  * ```git commit -m "add feature vulcan"```
  * 切回dev,```git switch dev```
  * feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的-D参数。。```git branch -D feature-vulcan```
## 多人协作
* 当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。
* 要查看远程库的信息，用```git remote```
  * 或者，用```git remote -v```显示更详细的信息
    * 上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。
* 推送分支
  * ```git push origin master```
  * 推送其他分支```git push origin dev```
    * master分支是主分支，因此要时刻与远程同步；

    * dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

    * bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

    * feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
* 抓取分支
  * 多人协作时，大家都会往master和dev分支上推送各自的修改。
  * 可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆```git clone git@github.com:michaelliao/learngit.git```
  * 默认情况下，你的小伙伴只能看到本地的master分支。可以用git branch命令看
  * 创建远程origin的dev分支到本地```git checkout -b dev origin/dev```
  * 可以在dev上继续修改，然后，时不时地把dev分支push到远程
        ```
        git add env.txt
        git commit -m "add env"
        git push origin dev
  * 小伙伴的最新提交和你试图推送的提交有冲突,先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送:
    ```git pull```git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：```git branch --set-upstream-to=origin/dev dev```再pull,解决后，提交，再push：```git commit -m "fix env conflict"```,```git push origin dev```
## Rebase
* rebase操作可以把本地未push的分叉提交历史整理成直线；
* rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
# 标签管理
* Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
  * commit号是6a5819e...tag v1.2查找commit就行!tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起
## 创建标签
* 实操：
  * 首先，切换到需要打标签的分支上
  * 然后，敲命令git tag <name>就可以打一个新标签：```git tag v1.0```
  * 可以用命令```git tag```查看所有标签：
  * 找到历史提交的commit id，然后打上tag
    * ```git log --pretty=oneline --abbrev-commit```
    * ```git tag v0.9 f52c633```
* 标签不是按时间顺序列出，而是按字母排序的。可以用```git show <tagname>```查看标签信息：
* 还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：```git tag -a v0.1 -m "version 0.1 released" 1094adb```
## 操作标签
* 标签打错了，也可以删除```git tag -d v0.1```
* 如果要推送某个标签到远程，使用命令```git push origin <tagname>```
* 或者，一次性推送全部尚未推送到远程的本地标签：```git push origin --tags```
* 如果标签已经推送到远程，要删除远程标签就麻烦一点
  * 先从本地删除：```git tag -d v0.9```
  * 然后，从远程删除。删除命令也是push```git push origin :refs/tags/v0.9```
