git 服务器
####################################

需要在服务器中安装 git，不在需要其它工具。适合简单的单用户使用。

- 搭建 Git 服务器非常简单，通常几分钟即可完成；
- 要方便管理公钥，用 Gitosis；
- 要变态地控制权限，用 Gitolite。


创建用户
************************************

创建 git 用户组和用户，用户名随便定义。

::

    # 创建 git 用户组
    [Linux]# groupadd git

    # 创建 git 用户，并在 /home 目录下创建用户主目录、添加到 git 组
    [Linux]# useradd -m git -g git

    # 修改用户登录密码，上传仓库时会用到
    [Linux]# passwd git


初始化仓库
************************************

用户家目录作为 Git 仓库，输入命令：

::

    [Linux]$ cd /home/git
    [Linux]$ git init --bare gitrepo.git

    # 修改仓库的用户和用户组（可选，根据实际情况使用）
    [Linux]$ chown git:git gitrepo/


克隆和推送
************************************

克隆仓库时，需要用户名（@ 前的字符）和路径（: 后边的字符）与服务器相符。

::

    [Linux]$ git clone git@192.168.1.2:/home/git/gitrepo.git

推送本地修改到服务器时，需要输入 git 用户的密码。

::

    [Linux]$ git push


禁止 git 登录
************************************

出于安全考虑，要禁用 git 用户登录 shell。编辑 ``/etc/passwd`` 文件，找到类似下面的一行：

::

    git:x:1001:1001:,,,:/home/git:/bin/bash

    # 将最后的 bash 更改为：
    git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell

这样，git 用户可以正常通过 ssh 使用 git，但无法登录shell。


创建证书登录
************************************

将本地用户的公钥（id_rsa.pub）导入到服务器的 /home/git/.ssh/authorized_keys 文件中，一行一个。

如果没有该文件创建它：

::

    [Linux]$ cd /home/git/
    [Linux]$ mkdir .ssh
    [Linux]$ chmod 755 .ssh
    [Linux]$ touch .ssh/authorized_keys
    [Linux]$ chmod 644 .ssh/authorized_keys
