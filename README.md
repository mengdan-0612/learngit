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
