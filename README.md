# learngit
安装、配置用户
-----
    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"

注意:`git config`命令的`--global`参数，用了这个参数，表示这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

创建版本库
-----
###创建仓库
    $ git init
    Initialized empty Git repository in /Users/michael/learngit/.git/

瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

###把文件添加到仓库
    $ git add readme.txt

###把文件提交到仓库
    $ git commit -m "wrote a readme file"
    [master (root-commit) cb926e7] wrote a readme file
    1 file changed, 2 insertions(+)
    create mode 100644 readme.txt
    
简单解释一下`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

`commit`可以一次提交很多文件，所以你可以多次add不同的文件，比如：

    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files."

仓库管理
-----
###查看工作区状态
    $ git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #    modified:   readme.txt
    #
    no changes added to commit (use "git add" and/or "git commit -a")
    
`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令告诉我们，readme.txt被修改过了，但还没有准备提交的修改。

###查看修改
    $ git diff readme.txt 
    diff --git a/readme.txt b/readme.txt
    index 46d49bf..9247db6 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
    -Git is a version control system.
    +Git is a distributed version control system.
    Git is free software.
    
`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个“distributed”单词。

###查看历史记录
    $ git log
    commit 3628164fb26d48395383f8f31179f24e0882e1e0
    Author: Michael Liao <askxuefeng@gmail.com>
    Date:   Tue Aug 20 15:11:49 2013 +0800
    
        append GPL
    
    commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
    Author: Michael Liao <askxuefeng@gmail.com>
    Date:   Tue Aug 20 14:53:12 2013 +0800
    
        add distributed
    
    commit cb926e7ea50ad11b8f9e909c05226233bf755030
    Author: Michael Liao <askxuefeng@gmail.com>
    Date:   Mon Aug 19 17:51:55 2013 +0800
    
        wrote a readme file

`git log`命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是`append GPL`，上一次是`add distributed`，最早的一次是`wrote a readme file`。

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

    $ git log --pretty=oneline
    3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
    ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
    cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file
    
类似`3628164...882e1e0`的是`commit id`（版本号），和SVN不一样，Git的`commit id`不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示

###版本回退
首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`3628164...882e1e0`，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个^比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本“append GPL”回退到上一个版本“add distributed”，就可以使用`git reset`命令：

    $ git reset --hard HEAD^
    HEAD is now at ea34578 add distributed
    
如果 回退错了想再返回原来版本“append GPL”怎么办，现在`git log`里已经没有“append GPL”版本了，办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个“append GPL”的`commit id`是`3628164...`，于是就可以指定回到未来的某个版本：

    $ git reset --hard 3628164
    HEAD is now at 3628164 append GPL

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

###查看命令历史
如果回退到了某个版本并关掉了电脑，然后又后悔了，那怎么去找之前版本的`commit id`呢，可以用`git reflog`,`git reflog`记录了每一次的命令

    $ git reflog
    ea34578 HEAD@{0}: reset: moving to HEAD^
    3628164 HEAD@{1}: commit: append GPL
    ea34578 HEAD@{2}: commit: add distributed
    cb926e7 HEAD@{3}: commit (initial): wrote a readme file
    
这样就可以找到以前提交的`commit id`了

###撤销修改

####丢弃工作区的修改

    $ git checkout -- readme.txt

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：
一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。
`git checkout -- file`命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令

####丢弃暂存区的修改

    $ git reset HEAD readme.txt
    Unstaged changes after reset:
    M       readme.txt
    
`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。
现在暂存区是干净的，工作区有修改，然后用`git checkout -- readme.txt`丢弃修改，就可以恢复文件了。

###删除文件
    $ git rm test.txt
    rm 'test.txt'
    $ git commit -m "remove test.txt"
    [master d17efd8] remove test.txt
     1 file changed, 1 deletion(-)
     delete mode 100644 test.txt

`git rm`删除文件，还需要提交

远程仓库
------
###添加远程仓库github
####关联远程库
在github上创建一个仓库，把本地的仓库与之关联

    $ git remote add origin git@github.com:michaelliao/learngit.git
    
上面的`michaelliao`是自己的GitHub账户名
远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

####把本地库的所有内容推送到远程库上

    $ git push -u origin master
    Counting objects: 19, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (19/19), done.
    Writing objects: 100% (19/19), 13.73 KiB, done.
    Total 23 (delta 6), reused 0 (delta 0)
    To git@github.com:michaelliao/learngit.git
     * [new branch]      master -> master
    Branch master set up to track remote branch master from origin.
    
把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。
由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

    $ git push origin master

把本地`master`分支的最新修改推送至GitHub

####SSH警告
当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：

    The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
    RSA key fingerprint is xx.xx.xx.xx.xx.
    Are you sure you want to continue connecting (yes/no)?

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

    Warning: Permanently added 'github.com' (RSA) to the list of known hosts.

###从远程库克隆

    $ git clone git@github.com:michaelliao/gitskills.git
    Cloning into 'gitskills'...
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (3/3), done.
    
GitHub给出的地址不止一个，还可以用https://github.com/michaelliao/gitskills.git 这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

###从远程抓取

    $ git pull
    Auto-merging hello.py
    CONFLICT (content): Merge conflict in hello.py
    Automatic merge failed; fix conflicts and then commit the result.
    
从远程库抓取更新

分支管理
------
###创建分支

    $ git checkout -b dev
    Switched to a new branch 'dev'

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

    $ git branch dev
    $ git checkout dev
    Switched to branch 'dev'

###查看当前分支

    $ git branch
    * dev
      master
      
`git branch`命令会列出所有分支，当前分支前面会标一个*号。

###合并分支

    $ git merge dev
    Updating d17efd8..fec145a
    Fast-forward
     readme.txt |    1 +
     1 file changed, 1 insertion(+)
     
`git merge`命令用于合并指定分支到当前分支。注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。如果修改的文件有冲突时则不能用`Fast-forward`合并

    $ git merge feature1
    Auto-merging readme.txt
    CONFLICT (content): Merge conflict in readme.txt
    Automatic merge failed; fix conflicts and then commit the result.

readme.txt文件冲突了，查看文件会标示出冲突的内容

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes of files.
    <<<<<<< HEAD
    Creating a new branch is quick & simple.
    =======
    Creating a new branch is quick AND simple.
    >>>>>>> feature1
    
修改后太提交就好

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。
如果要强制禁用`Fast forward`模式，Git就会在`merge`时生成一个新的`commit`，这样，从分支历史上就可以看出分支信息。
注意`--no-ff`参数，表示禁用`Fast forward`，因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

    $ git merge --no-ff -m "merge with no-ff" dev
    Merge made by the 'recursive' strategy.
     readme.txt |    1 +
     1 file changed, 1 insertion(+)
     
合并后，我们用git log看看分支历史：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   7825a50 merge with no-ff
    |\
    | * 6224937 add merge
    |/
    *   59bc1cb conflict fixed
    ...

###查看日志

    $ git log --graph --pretty=oneline --abbrev-commit
    *   59bc1cb conflict fixed
    |\
    | * 75a857c AND simple
    * | 400b400 & simple
    |/
    * fec145a branch test
    
可以看到分支合并的日志

###删除分支

    $ git branch -d dev
    Deleted branch dev (was fec145a).
    
如果这个分支还没有被合并就删除的话会提醒

    $ git branch -d feature-vulcan
    error: The branch 'feature-vulcan' is not fully merged.
    If you are sure you want to delete it, run 'git branch -D feature-vulcan'.

可又想删除它，就用强制删除

    $ git branch -D feature-vulcan
    Deleted branch feature-vulcan (was 756d4af).

###暂存工作区
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

    $ git stash
    Saved working directory and index state WIP on dev: 6224937 add merge
    HEAD is now at 6224937 add merge
    
查看暂存工作区
 
    $ git stash list
    stash@{0}: WIP on dev: 6224937 add merge
    
恢复工作区有两个办法：
一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；
另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

    $ git stash pop
    # On branch dev
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       new file:   hello.py
    #
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #       modified:   readme.txt
    #
    Dropped refs/stash@{0} (f624f8e5f082f2df2bed8a4e09c12fd2943bdd40)
    
再用`git stash list`查看，就看不到任何stash内容了。

可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

    $ git stash apply stash@{0}

自定义git
-------
###忽略特殊文件
在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。
不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

###配置别名

    $ git config --global alias.st status

敲`git st`就表示`git status`

配置一个git last，让其显示最后一次提交信息：

    $ git config --global alias.last 'log -1'

这样，用git last就能显示最近一次的提交：

    $ git last
    commit adca45d317e6d8a4b23f9811c3d7b7f0f180bfe2
    Merge: bd6ae48 291bea8
    Author: Michael Liao <askxuefeng@gmail.com>
    Date:   Thu Aug 22 22:49:22 2013 +0800

    merge & fix hello.py


相关资料
-------
http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
