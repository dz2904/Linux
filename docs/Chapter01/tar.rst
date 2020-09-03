tar 归档
####################################

tar 命令可以为多个文件和目录创建一个档案（归档），通常与压缩工具（如 ``gzip`` 或 ``bzip`` ）结合使用。

.. hint ::

    归档的一般用途：

    - 把一大堆的文件和目录打包成一个 tar 包，便于网络传输。
    - 生成 tar 包后，再用其它的程序进行压缩。


命令格式：
************************************

.. highlight:: none

::

    tar {A|c|d|r|t|u|x}[GnSkUWOmpsMBiajJzZhPlRvwo] [ARG...]


常用选项：
************************************

::

    必要选项：

    -A, --catenate, --concatenate
        将归档附加到另一个归档的末尾

    -c, --create
        创建新的归档

    -d, --diff, --compare
        查找归档和文件之间的差异

    --delete
        从归档中删除文件

    -r, --append
        添加文件到归档中

    -t, --list
        列出归档内容

    -t, --list
        测试归档并退出

    -u, --update
        更新归档中的文件

    -x, --extract, --get
        从归档中提取文件


其它选项：

======    ===========================    ============
选项        长选项                           说明
======    ===========================    ============
-a         --auto-compress                使用归档后缀来决定压缩程序
-b         --blocking-factor              设置区块大小
-C         --directory DIR                指定提取路径
-f         --file ARCHIVE                 指定备份文件名(文件名必须紧跟在 -f 参数之后)
-h         --dereference                  跟踪符号链接，将它指向的文件添加到归档中
-i         --ignore-zeros                 忽略归档中的空文件
-j         --bzip2                        通过 bzip2 归档（创建、解压 tar.bz2）
-J         --xz                           通过 xz 归档（创建、解压 tar.xz）
-k         --keep-old-files               提取时不替换已经存在的文件
-m         --touch                        不提取文件的修改时间
-M         --multi-volume                 创建/列出/提取 多卷归档
-n         --seek                         归档可检索
-N         --newer                        只归档比日期或文件更新的文件
-O         --to-stdout                    提取文件到标准输出
-p         --preserve-permissions         提取文件的权限信息(默认为超级用户)
-U         --unlink-first                 在提取归档之前先删除文件
-v         --verbose                      显示详细的执行过程
-w         --interactive                  每次操作都要求确认
-W         --verify                       在完成操作后尝试校验归档
-z         --gzip <br /> --gunzip         通过 gzip 归档（创建、解压 tar.gz）
-Z         --compress                     通过 Z 归档（创建、解压 tar.Z）
======    ===========================    ============


使用实例：
************************************


**创建一个归档** 将 /etc/ 目录和 /root/anaconda-ks.cfg 文件归档至当前目录下：

::

    [root@linuxtechi ~]# tar -cvf myarchive.tar /etc /root/anaconda-ks.cfg


**查看归档中的内容** 只查看归档中的内容，并不提取文件：

::

    [root@linuxtechi ~]# tar -tvf myarchive.tar


**追加文件到归档中** 将 /media/fstab 添加到 myarchive.tar 中：

::

    [root@linuxtechi ~]# tar -rvf myarchive.tar /media/fstab


**提取所有归档文件** 提取 myarchive.tar 中的内容至当前目录下：

::

    [root@linuxtechi ~]# tar -xvf myarchive.tar


**提取所有归档文件到指定目录** 提取 myarchive.tar 中的内容至 /tmp 目录下：

::

    [root@linuxtechi ~]# tar -xvf myarchive.tar -C /tmp/  


**提取指定归档文件到指定目录** 提取 myarchive.tar 档案中的 root/anaconda-ks.cfg 文件至 /tmp 目录下：

::

    [root@linuxtechi tmp]# tar -xvf /root/myarchive.tar root/anaconda-ks.cfg -C /tmp/ root/anaconda-ks.cfg
    [root@linuxtechi tmp]# ls -l /tmp/root/anaconda-ks.cfg
    -rw-------. 1 root root 953 Aug 24 01:33 /tmp/root/anaconda-ks.cfg

**在目录中，比某个日期新的文件才归档**

::

    [root@linuxtechi ~]# tar -N "2012/11/13" -cvf log17.tar /media/fstab


**创建归档文件，并以 gzip 压缩（.tar.gz 或 .tgz）**

::

    压缩
    [root@linuxtechi ~]# tar -zcvf myarchive.tar.gz /etc/ /opt/

    解压
    [root@linuxtechi ~]# tar -zxvf myarchive.tgz

    查看压缩文件列表
    [root@linuxtechi ~]# tar -tvf myarchive.tgz


**创建归档文件，并以 bz2 压缩（.tar.bz2 或 .tbz2）**

::

    压缩
    [root@linuxtechi ~]# tar -jcvf myarchive.tar.gz /etc/ /opt/

    解压
    [root@linuxtechi ~]# tar -jxvf myarchive.tgz

    查看压缩文件列表
    [root@linuxtechi ~]# tar -tvf myarchive.tgz

