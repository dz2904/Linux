通过 Git 方式管理 FTP 服务
####################################

现在有很多网站依然使用 FTP 上传文件到服务器，但传统的 FTP 客户端在使用过程中，都需要留意本地文件和远程文件之间的差异，很容易忘记更新部分文件或上传到错误的路径等。总之，需要人工干预上传过程，容易出错而且工作效率低。

所以就有了 `git-ftp <https://github.com/git-ftp/git-ftp/>`_ 工具（其实就是一个 shell 脚本）完美的解决这个问题。git-ftp 通过存储在服务器上的 ``.git-ftp.log`` 文件来跟踪每次传输文件之间的变化，用一条命令实现只上传修改的文件，省心省力。


快速开始
************************************

安装
====================================

git-ftp 已经进入了大部分的软件仓库，可以直接通过系统的安装包工具安装。因为 git-ftp 的有些命令（git-ftp download 和 git-ftp pull）需要用到 lftp，所以建议一并安装 lftp。

.. highlight:: none

::

    [Linux]# apt install git-ftp
    [Linux]# apt install lftp


配置
====================================

在配置文件之前，需要新建一个空白的 Git 仓库。

::

    # 新建仓库
    [Linux]$ git init new
    [Linux]$ cd new

    # 添加配置
    [Linux]$ git config git-ftp.url ftp.example.net/new/
    [Linux]$ git config git-ftp.user ftp-user
    [Linux]$ git config git-ftp.password secr3t

    [Linux]$ cat .git/config
    [core]
        bare = false
        repositoryformatversion = 0
        filemode = false
        symlinks = false
        ignorecase = true
        logallrefupdates = true
    [git-ftp]
        url = ftp.example.net/new/
        user = ftp-user
        password = secr3t


.. attention::

    因为服务器地址和密码都是以明文保存的，所以本地仓库不要发布到网上公开仓库。


基本命令
====================================

- git-ftp init 新建服务器仓库。用于 FTP 服务器没有文件，上传所有本地提交到服务器仓库，并创建 ``.git-ftp.log`` 文件。
- git-ftp catchup 初始服务器仓库。用于 FTP 服务器有文件，只在服务器上创建 ``.git-ftp.log`` 文件。
- git-ftp download 下载服务器仓库中的文件。在服务器上需要有 ``.git-ftp.log`` 文件，并且本地仓库为空。
- git-ftp pull 拉取服务器仓库的提交。
- git-ftp push 推送本地提交到服务器仓库。
