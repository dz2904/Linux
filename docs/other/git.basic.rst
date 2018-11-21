常用 Git 命令清单
#######################################

我每天使用 Git ，但是很多命令记不住。

一般来说，日常使用只要记住下图的几个命令，就可以了。但是要熟练使用，恐怕要记住60～100个命令。

.. image:: ../images/git.01.jpg
    :width: 600 px

下面是整理的常用 Git 专用名词的译名。

:Workspace: 工作区

:Index / Stage: 暂存区

:Repository: 仓库区（或本地仓库）

:Remote: 远程仓库


一、配置
*************************************

Git 自带一个 ``git config`` 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

1. **/etc/gitconfig 文件:** 包含系统上每一个用户及他们仓库的通用配置。如果使用带有 --system 选项的 git config 时，它会从此文件读写配置变量。

2. **~/.gitconfig 或 ~/.config/git/config 文件：** 只针对当前用户。可以传递 --global 选项让 Git 读写此文件。

3. **当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：** 针对该仓库。

每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 ``/etc/gitconfig`` 中的配置变量。

.. highlight:: none

::

    # 设置提交代码时的基本信息
    # 如果使用了 --global 选项，那么在以后的修改、提交中，Git 都会使用这些配置
    $ git config [--global] user.name "[name]"
    $ git config [--global] user.email "[email address]"

    # 显示当前的 Git 配置
    $ git config --list

    # 显示 user.name 的配置
    $ git config user.name

    # 编辑 Git 配置文件
    $ git config -e [--global]

    # 设置默认的文本编辑器
    $ git config --global core.editor vim


二、Git 仓库
*************************************

有两种获得 Git 项目仓库的方法。 第一种是在现有项目或目录下导入文件到 Git 中； 第二种是从一个服务器克隆一个现有的 Git 仓库。

::

    # 在当前目录新建一个 Git 代码库
    # 会创建一个 .git 的子目录，这个子目录包含 Git 仓库所有的文件。
    $ git init

    # 新建一个目录，将其初始化为Git代码库
    $ git init [project-name]

    # 克隆一个项目
    $ git clone [url]

    # 克隆一个项目并重命名
    $ git clone [url] [git-name]


三、跟踪/忽略文件
*************************************

::

    # 添加当前目录的所有文件到暂存区
    $ git add .

    # 将文件修改添加到暂存区
    $ git add [file1] [file2] ...

    # 删除文件，并更新到暂存区
    $ git rm [file1] [file2] ...

    # 删除文件（忽略文件），但该文件会保留在工作区
    $ git rm --cached [file]

    # 重命名文件，并更新到暂存区
    $ git mv [file-original] [file-renamed]

    # 显示被跟踪的文件状态
    $ git status


四、忽略文件
*************************************

通常软件都会自动生成一些文件，比如日志文件，或者编译过程中产生的临时文件等，这些文件一般无需纳入 Git 的管理。可以在项目的根目录下创建一个名为 ``.gitignore`` 的文件使 Git 忽略指定的文件，``.gitignore`` 的格式规范如下：

+ 所有空行或者以 ``#`` 开头的行都会被 Git 忽略。

+ 可以使用标准的 glob 模式匹配。

+ 匹配模式可以以 ``/`` 开头防止递归。

+ 匹配模式可以以 ``/`` 结尾指定目录。

+ 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号 ``!`` 取反。

::

    $ cat .gitignore

    # 忽略所有以 .o 或 .a 结尾的文件
    *.[oa]

    # 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件都用这样的文件名保存副本。
    *~

    # 跟踪 lib.a 文件, 即使已经忽略了所以的 .a 结尾的文件
    !lib.a

    # 只忽略当前目录下的 TODO 文件
    /TODO

    # 忽略 build 目录中的所有文件
    build/

    # 忽略 doc 目录下的以 .txt 结尾的文件（不递归处理）
    doc/*.txt

    # 忽略 doc 目录下的所有以 .pdf 结尾的文件（递归处理）
    doc/**/*.pdf


五、代码提交
*************************************

::

    # 提交暂存区的文件到仓库区，并输入简单说明
    $ git commit -m [message]

    # 提交暂存区的指定文件到仓库区，并输入简单说明
    $ git commit [file1] [file2] ... -m [message]

    # 提交工作区的所以文件到仓库区
    $ git commit -a

    # 使用新的commit，代替上一次提交
    # 如果代码没有任何变化，只改写上一次commit的提交信息
    $ git commit --amend -m [message]

    # 显示commit历史
    $ git log


六、查看信息
*************************************

::

    # 显示被跟踪的文件状态
    $ git status

    # 显示简洁的信息
    # ?? 表示未跟踪的文件、A 表示新添加到暂存区的文件
    # M 表示文件已被修改，在左边是已更新到暂存区，在右边是还没有更新到暂存区
    $ git status -s

    # 显示commit历史
    $ git log

    # 显示每次提交的内容差异，也可以指定文件名，还可以指定提交的次数
    $ git log -p [file]
    $ git log -p -2

    # 显示commit历史，以及提交时变更过的文件
    $ git log --stat

    # 搜索提交历史，根据关键词
    $ git log -S [keyword]

    # 显示指定文件的详细修改信息
    $ git blame [file]

    # 显示暂存区和工作区的差异
    $ git diff [file]

    # 显示暂存区和仓库区的差异
    $ git diff --cached [file]

    # 显示工作区和仓库区的差异
    $ git diff HEAD

    # 显示最近的几次提交
    $ git reflog

    # 列出所有本地分支
    $ git branch

    # 列出所有远程分支
    $ git branch -r

    # 列出所有本地分支和远程分支
    $ git branch -a

    # 显示所有远程仓库
    $ git remote -v

    # 显示某个远程仓库的信息
    $ git remote show [remote]


七、撤销
*************************************

.. note::

    Git 有些撤消操作是不可逆的。在使用 Git 的撤销时，可能会因为操作失误而导致之前的工作丢失。

::

    # 恢复暂存区的所有文件到工作区，可指定文件名
    $ git checkout .

    # 恢复某个commit的指定文件到暂存区和工作区
    $ git checkout [commit] [file]

    # 回退到上一次提交，可指定哈希值
    $ git reset --hard [Hash]

    # 重置暂存区，与上一次提交保持一致，但工作区内容不变，可指定哈希值或文件名
    $ git reset [file]

    # 新建一个commit，用来撤销指定commit
    # 后者的所有变化都将被前者抵消，并且应用到当前分支
    $ git revert [commit]


八、分支
*************************************

::

    # 列出所有本地分支
    $ git branch

    # 列出所有远程分支
    $ git branch -r

    # 列出所有本地分支和远程分支
    $ git branch -a

    # 新建分支，停留在当前分支
    $ git branch [branch-name]

    # 新建分支，并切换到该分支
    $ git checkout -b [branch]

    # 根据指定提交版本，新建分支
    $ git branch [branch] [commit]

    # 切换到指定分支，并更新工作区
    $ git checkout [branch-name]

    # 建立追踪关系，在现有分支与指定的远程分支之间
    $ git branch --set-upstream [branch] [remote-branch]

    # 合并指定分支到当前分支
    $ git merge [branch]

    # 选择一个commit，合并进当前分支
    $ git cherry-pick [commit]

    # 删除分支
    $ git branch -d [branch-name]

    # 删除远程分支
    $ git push origin --delete [branch-name]
    $ git branch -dr [remote/branch]


九、远程仓库
*************************************

远程仓库是指托管在因特网或其他网络中的项目的版本库。你可以有好几个远程仓库，通常有些仓库对你只读，有些则可以读写。与他人协作涉及管理远程仓库以及根据需要推送或拉取数据。

::

    # 拉取远程仓库的变化，并与本地分支合并
    $ git pull [remote] [branch]

    # 拉取远程仓库的所有变动
    $ git fetch [remote]

    # 显示所有远程仓库
    $ git remote -v

    # 显示某个远程仓库的信息
    $ git remote show [remote]

    # 增加一个新的远程仓库，并命名
    $ git remote add [shortname] [url]

    # 推送本地指定分支到远程仓库
    $ git push [remote] [branch]

    # 强行推送当前分支到远程仓库，即使有冲突
    $ git push [remote] --force

    # 推送所有分支到远程仓库
    $ git push [remote] --all

    # 重命名远程仓库
    $ git remote rename [original] [renamed]

    # 删除远程仓库
    $ git remote rm paul

    # 生成一个可供发布的压缩包
    $ git archive


十、标签
*************************************

Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。

一个轻量标签很像一个不会改变的分支，它只是一个特定提交的引用。

然而，附注标签是存储在 Git 数据库中的一个完整对象。它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间，还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息。

::

    # 创建一个附注标签，并设定标签信息
    $ git tag -a v1.4 -m 'my version 1.4'

    # 创建一个轻量标签
    $ git tag v1.4-lw

    # 删除本地tag
    $ git tag -d [tag]

    # 列出所有tag
    $ git tag

    # 查看标签信息
    $ git show v1.4

    # 删除远程tag
    $ git push origin :refs/tags/[tagName]

    # 新建一个分支，指向某个tag
    $ git checkout -b [branch] [tag]


十一、Git 别名
*************************************

别名可以使 Git 输入命令更简单、容易。Git 并没有命令自动补全的功能，如果不想每次都输入完整的 Git 命令，可以通过 git config 文件来轻松地为每一个命令设置一个别名。

::

    # 设置 git commit 的别名为 git ci
    $ git config --global alias.ci commit

    # 设置 git status 的别名为 git st
    $ git config --global alias.st status


十二、获得帮助
*************************************

::

    # 有三种方法可以找到 Git 命令的使用手册：

    $ git help <verb>
    $ git <verb> --help
    $ man git-<verb>
