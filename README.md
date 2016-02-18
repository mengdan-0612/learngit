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
    $ git add test.txt

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




