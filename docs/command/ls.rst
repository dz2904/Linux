ls 命令
##########################

ls 命令用来显示目标列表，在 Linux 中是使用率较高的命令。

根据 Eric Fischer 在 Linux 文档项目中关于 ls 命令的文章，该命令的起源可以追溯到 1961 年 MIT 的相容分时系统上的 listf 命令。根据维基百科，ls 出现在 AT&T Unix 的原始版本中。我们今天在 Linux 系统上使用的 ls 命令来自 GNU Core Utilities。

命令格式：
***********************

.. highlight:: none

::

    ls [OPTION]... [FILE]...

常用选项：
***********************

::

    -a, --all
      显示所有的文件，包括隐藏文件（以 ``.`` 开头的文件）

    -A, --almost-all
      显示所有的文件，包括隐藏文件，但不包括表示当前目录 . 和上级目录 .. 这两个文件

    -C
      多列显示输出结果。这是默认选项

    --color[=WHEN]
      是否根据文件类型显示颜色，WHEN 可以为 never、always 或者 auto

    -F
      在每个输出项后追加文件的类型标识符
      具体含义：“*”表示具有可执行权限的普通文件
              “/”表示目录
              “@”表示符号链接
              “|”表示命令管道 FIFO
              “=”表示 sockets 套接字
              当文件为普通文件时，不输出任何标识符

    -i, --inode
      列出文件的索引节点号（inode）

    -l
      以长格式显示目录下的内容列表。
      输出的信息从左到右依次包括：文件类型、权限模式、硬连接数、所有者、组、文件大小、最后修改时间、文件名

    -m
      用逗号 ``,`` 区隔每个文件和目录的名称

    -R
      递归处理，将指定目录下的所有文件及子目录一并处理

    -t
      用文件和目录的更改时间排序

使用实例：
***********************

::

    # 显示长格式列表
    [root@localhost /]# ls -l
    total 20
    lrwxrwxrwx.   1 root root    7 Mar 27  2018 bin -> usr/bin
    dr-xr-xr-x.   5 root root 4096 Mar 27  2018 boot
    ...
    drwxr-xr-x.  20 root root  278 Feb 12 18:17 var


    # 显示所有的文件，包括隐藏文件
    [root@localhost ~]# ls -a
    .   .bash_history  .bash_profile  .cshrc          .pki
    ..  .bash_logout   .bashrc        .mysql_history  .tcshrc

    # 显示长格式列表并且显示文件的索引节点号
    [root@localhost /]# ls -li
    total 20
          120 lrwxrwxrwx.   1 root root    7 Mar 27  2018 bin -> usr/bin
           64 dr-xr-xr-x.   5 root root 4096 Mar 27  2018 boot
    ...
    100663361 drwxr-xr-x.  20 root root  278 Feb 12 18:17 var

    # 用逗号区隔每个文件和目录的名称
    [root@localhost /]# ls -m
    bin, boot, dev, etc, home, lib, lib64, media, mnt, opt, proc, root, run, sbin,
    srv, sys, tmp, usr, var

    # 在每个输出项后追加文件的类型标识符
    [root@localhost /]# ls -F
    bin@   dev/  home/  lib64@  mnt/  proc/  run/   srv/  tmp/  var/
    boot/  etc/  lib@   media/  opt/  root/  sbin@  sys/  usr/

    # 显示目录下所有的 php 文件
    [root@localhost owncloud]# ls -l *.php
    -rwxrwxrwx 1 root root 4371 Feb 12 19:38 console.php
    -rwxrwxrwx 1 root root 5033 Feb 12 19:38 cron.php
    -rwxrwxrwx 1 root root 3678 Feb 12 19:38 index.php

    # 递归显示目录下文件
    [root@localhost updater]# ls -FR
    .:
    app/              COPYING-AGPL*  pub/        src/
    application.php*  index.php*     README.md*  vendor/

    ./app:
    bootstrap.php*  config/

    ./app/config:
    container.php*

    ./pub:
    css/  img/  js/

    ./pub/css:
    main.css*
