GitHub 命令行操作
####################################

GitHub 是一个面向开源及私有软件项目的免费托管平台，作为个人开发者可以很方便的托管项目或为已有项目贡献代码。将 GitHub 仓库克隆到本地，并进行增、删、改、查等基本操作，也将作为一个基本技能必不可少。

基本的使用流程为：

1. 将项目克隆到本地
2. 新建分支
3. 常用的修改
4. 将修改提交到本地仓库
5. 将分支推送回 GitHub，并合并分支

将项目克隆到本地
************************************

一切可以从新开始，直接将远程项目克隆到本地。但如果已有本地仓库，也可以只获取仓库的变化同步到本地。

.. highlight:: none

::

    # 将项目克隆到本地
    $ git clone https://github.com/tonychacon/blink


    # 拉取远程仓库的变化，并与本地分支合并
    $ git pull [remote] [branch]

    # 显示所有远程仓库
    $ git remote -v


新建分支
************************************

::

    # 新建分支，并切换到新分支
    $ git checkout -b [branch]


    # 列出所有分支，包括本地分支和远程分支
    $ git branch -a

    # 切换到指定分支，并更新工作区
    $ git checkout [branch-name]

    # 合并指定分支到当前分支
    $ git merge [branch]

    # 删除本地分支
    $ git branch -d [branch-name]


常用的修改
************************************

::

    # 添加文件，将当前目录下所有文件添加到暂存区
    $ git add .

    # 添加指定文件，将指定文件添加到暂存区
    $ git add [filename]

    # 删除文件，并更新到暂存区
    $ git rm [filename]

    # 重命名文件，并更新到暂存区
    $ git mv [file] [file-renamed]

    # 显示被跟踪的文件状态
    $ git status


将修改提交到本地仓库
************************************

::

    # 将暂存区的文件提交到本地仓库，并输入简单说明
    $ git commit -m [message]

    # 将工作区的文件提交到本地仓库
    $ git commit -a

    # 将所有修改直接提交到本地仓库
    $ git commit -a -m 'three seconds is better'



将分支推送回 GitHub，并合并分支
************************************

::

    # 将本地指定分支推送回远程仓库
    $ git push [remote] [branch]

    # 将新分支推送回 GitHub 时，会询问帐号和密码
    $ git push origin slow-blink
    Username for 'https://github.com': tonychacon
    Password for 'https://tonychacon@github.com':
    Counting objects: 5, done.
    ...


    # 上传所有标签
    git push origin --tags

最后到 GitHub 网站上查看分支并创建合并请求。


标签
************************************

Git 使用两种主要类型的标签：轻量标签与附注标签。通常建议创建附注标签。

轻量标签很像一个不会改变的分支，它只是一个特定提交的引用。然而，附注标签是存储在 Git 数据库中的完整对象。它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间，还有标签信息。 

::

    # 创建附注标签，并设定标签信息
    $ git tag -a v1.4 -m 'my version 1.4'

    # 查看标签信息
    $ git show v1.4

    # 列出所有tag
    $ git tag

    # 删除本地tag
    $ git tag -d [tag]

    # 删除远程tag
    $ git push origin :refs/tags/[tagName]


忽略文件
************************************

通常软件都会自动生成一些文件，比如日志文件，或者编译过程中产生的临时文件等，这些文件无需纳入 Git 的管理。可以在项目的根目录下创建 ``.gitignore`` 文件使 Git 忽略指定的文件，格式规范如下：

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
