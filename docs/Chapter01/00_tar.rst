.. _cmd_tar:

tar 归档
###################################

tar 命令可以为多个文件和目录创建一个档案（归档），通常与压缩工具（如 ``gzip`` 或 ``bzip`` ）结合使用。

.. hint:: 归档的一般用途

    - 把一大堆的文件和目录归档成一个 tar 包，便于网络传输。
    - 生成 tar 包后，再用其它的程序进行压缩。


命令格式：
************************************

tar 命令有三种模式，一种是传统的命令模式，一种是 Unix 命令模式，一种是 GNU 的命令模式。使用中常以传统命名为主，下边主要介绍传统命令模式。

.. highlight:: nong

::

    # 传统模式
        tar {A|c|d|r|t|u|x}[GnSkUWOmpsMBiajJzZhPlRvwo] [ARG...]

    # UNIX 模式
        tar -A [OPTIONS] ARCHIVE ARCHIVE
        tar -c [-f ARCHIVE] [OPTIONS] [FILE...]
        tar -d [-f ARCHIVE] [OPTIONS] [FILE...]
        tar -t [-f ARCHIVE] [OPTIONS] [MEMBER...]
        tar -r [-f ARCHIVE] [OPTIONS] [FILE...]
        tar -u [-f ARCHIVE] [OPTIONS] [FILE...]
        tar -x [-f ARCHIVE] [OPTIONS] [MEMBER...]

    # GNU 模式
        tar {--catenate|--concatenate} [OPTIONS] ARCHIVE ARCHIVE
        tar --create [--file ARCHIVE] [OPTIONS] [FILE...]
        tar {--diff|--compare} [--file ARCHIVE] [OPTIONS] [FILE...]
        tar --delete [--file ARCHIVE] [OPTIONS] [MEMBER...]
        tar --append [-f ARCHIVE] [OPTIONS] [FILE...]
        tar --list [-f ARCHIVE] [OPTIONS] [MEMBER...]
        tar --test-label [--file ARCHIVE] [OPTIONS] [LABEL...]
        tar --update [--file ARCHIVE] [OPTIONS] [FILE...]
        tar --update [-f ARCHIVE] [OPTIONS] [FILE...]
        tar {--extract|--get} [-f ARCHIVE] [OPTIONS] [MEMBER...]


常用选项：
************************************

必要选项：
====================================

在参数中仅能使用一个，不同时使用，如： ``tar -cd`` 。

::

    -A, --catenate, --concatenate
        将归档附加到另一个归档的末尾

    -c, --create
        创建新的归档

    -d, --diff, --compare
        查找归档和文件之间的差异

    -t, --list
        列出归档内容

    -r, --append
        添加文件到归档中

    -u, --update
        更新归档中的文件

    -x, --extract, --get
        从归档中提取文件


其它选项：
====================================

::

    -a, --auto-compress
        使用归档后缀来决定压缩程序

    -b, --blocking-factor=BLOCKS
        设置区块大小

    -C, --directory=DIR
        指定提取路径

    --delete
        从归档中删除文件

    -exclude=PATTERN
        归档时排除指定的文件

    -f, --file=ARCHIVE
        指定归档文件名(文件名必须紧跟在 -f 参数之后)

    -h, --dereference
        跟踪符号链接，将它指向的文件添加到归档中

    -i, --ignore-zeros
        忽略归档中的空文件

    -j, --bzip2
        通过 bzip2 归档（创建、解压 tar.bz2）

    -J, --xz
        通过 xz 归档（创建、解压 tar.xz）

    -k, --keep-old-files
        提取时不替换已经存在的文件

    -m, --touch
        不提取文件的修改时间

    -M, --multi-volume
        创建/列出/提取 多卷归档

    -n, --seek
        归档可检索

    -N, --newer=DATE, --after-date=DATE
        只归档比日期或文件更新的文件

    -O, --to-stdout
        提取文件到标准输出

    -p, --preserve-permissions, --same-permissions
        提取文件的权限信息(默认为超级用户)

    -U, --unlink-first
        在提取归档之前先删除文件

    -v, --verbose
        显示详细的执行过程

    -w, --interactive, --confirmation
        每次操作都要求确认

    -W, --verify
        在完成操作后尝试校验归档

    -z, --gzip, --gunzip, --ungzip
        通过 gzip 归档（创建、解压 tar.gz）

    -Z, --compress, --uncompress
        通过 Z 归档（创建、解压 tar.Z）


使用实例：
************************************

归档
====================================

::

    # 将 /etc/ 和 /root/anaconda-ks.cfg 归档到当前目录
    [Linux]$ tar -cvf myarchive.tar /etc /root/anaconda-ks.cfg

    # 查看归档中的内容，并不提取文件
    [Linux]$ tar -tvf myarchive.tar

    # 追加文件到归档中
    [Linux]$ tar -rvf myarchive.tar /media/fstab

    # 将 scf/ 归档，但不包括 service 文件
    [Linux]$ tar --exclude scf/service -zcvf scf.tar.gz scf/* 


解包
====================================

::

    # 提取 tar 包中的文件到当前目录下
    [Linux]$ tar -xvf myarchive.tar

    # 提取 tar 包中的文件到指定目录
    [Linux]$ tar -xvf myarchive.tar -C /tmp/  

    # 提取指定归档文件到指定目录，先指定目录否则会出现意外结果
    [Linux]$ tar -C /tmp/ root/anaconda-ks.cfg -xvf /root/myarchive.tar root/anaconda-ks.cfg 
    [Linux]$ ls -l /tmp/root/anaconda-ks.cfg
    -rw-------. 1 root root 953 Aug 24 01:33 /tmp/root/anaconda-ks.cfg

    # 比指定日期新的文件才归档
    [Linux]$ tar -N "2012/11/13" -cvf log17.tar /media/fstab


归档并压缩
====================================

创建归档文件，并以 gzip 压缩（.tar.gz 或 .tgz）

::

    # 压缩
    [Linux]$ tar -zcvf myarchive.tar.gz /etc/ /opt/

    # 解压
    [Linux]$ tar -zxvf myarchive.tgz

    # 查看压缩文件列表
    [Linux]$ tar -tvf myarchive.tgz


创建归档文件，并以 bz2 压缩（.tar.bz2 或 .tbz2）

::

    # 压缩
    [Linux]$ tar -jcvf myarchive.tar.bz2 /etc/ /opt/

    # 解压
    [Linux]$ tar -jxvf myarchive.tbz2
