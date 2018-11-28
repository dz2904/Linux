GitHub 本地修改上传
#######################################

GitHub 流程
****************************

GitHub 设计了一个以合并请求为中心的特殊合作流程。 它基于我们在 Git 分支 的 特性分支 中提到的工作流程。 不管你是在一个紧密的团队中使用单独的版本库，或者使用许多的“Fork”来为一个由陌生人组成的国际企业或网络做出贡献，这种合作流程都能应付。

流程通常如下：

1. 从 master 分支中创建一个新分支

2. 提交一些修改来改进项目

3. 将这个分支推送到 GitHub 上

4. 创建一个合并请求

5. 讨论，根据实际情况继续修改

6. 项目的拥有者合并或关闭你的合并请求

这基本和集成管理者工作流中的一体化管理流程差不多，但是团队可以使用 GitHub 提供的网页工具替代电子邮件来交流和审查修改。

现在我们来看一个使用这个流程的例子。

我们将它克隆到本地，创建一个分支，修改代码，最后再将改动推送到 GitHub。


.. highlight:: none

::

    # 将派生出的副本克隆到本地
    $ git clone https://github.com/tonychacon/blink
    Cloning into 'blink'...

    $ cd blink

    # 创建出名称有意义的分支
    $ git checkout -b slow-blink
    Switched to a new branch 'slow-blink'

    # 修改代码
    $ sed -i '' 's/1000/3000/' blink.ino

    # 检查改动
    $ git diff --word-diff
    diff --git a/blink.ino b/blink.ino
    ...

    # 将改动提交到分支中
    $ git commit -a -m 'three seconds is better'××××××××××××××××××××××××××××
    [slow-blink 5ca509d] three seconds is better
    ...

    # 将新分支推送到 GitHub 的副本中
    $ git push origin slow-blink (6)
    Username for 'https://github.com': tonychacon
    Password for 'https://tonychacon@github.com':
    Counting objects: 5, done.
    ...


现在到 GitHub 上查看之前的项目副本，可以看到 GitHub 提示我们有新的分支，并且显示了一个大大的绿色按钮让我们可以检查我们的改动，并给源项目创建合并请求。

你也可以到“Branches”（分支）页面查看分支并创建合并请求。
