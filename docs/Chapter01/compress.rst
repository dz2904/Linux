压缩与解压
####################

首先要弄清两个概念：**打包和压缩**。打包是指将一大堆文件或目录变成一个总的文件；压缩则是将一个大的文件通过一些压缩算法变成一个小文件。

为什么要区分这两个概念呢？这源于Linux中很多压缩程序只能针对一个文件进行压缩，这样当你想要压缩一大堆文件时，你得先将这一大堆文件先打成一个包（tar命令），然后再用压缩程序进行压缩（gzip bzip2命令），就是说压缩会分为两步，先打包再压缩，各干个的事。

* tar_ （tar.gz 和 tar.bz2）
* gzip_
* bz2_
* zip_

.. _tar:

tar
****************************

tar 命令可以为多个文件和目录创建一个档案（归档），tar 最初是用来在磁带上创建档案；tar 命令也可以修改档案中的文件，或者加入新的文件；使用 tar 程序打出来的包我们常称为 tar 包，tar 包通常以 .tar 结尾。

**归档的一般用途：**

* 把一大堆的文件和目录打包成一个 tar 包，便于网络传输。
* 生成tar包后，再用其它的程序进行压缩。

tar 命令详解
================================

命令格式：

.. highlight:: none

::

    tar [-] A --catenate --concatenate | c --create | d --diff --compare | --delete | r --append | t --list | --test-label | u --update | x --extract --get [options] [pathname ...]

    tar [必要选项] [其它选项] [文件]


必要选项：

======    ===============    ============
选项        长选项              说明
======    ===============    ============
-A         --catenate          添加 tar 文件到归档中
-c         --create            创建新的归档
-d         --diff              查找归档和文件之间的差异
           --delete            从归档中删除文件
-r         --append            添加文件到归档中
-t         --list              列出归档内容
           --test-label        测试归档并退出
-u         --update            更新归档中的文件
-x         --extract           从归档中提取文件
======    ===============    ============

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

实例
=================

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

.. _gzip:

gzip
***************************
gzip是GNUzip的缩写，它是一个GNU自由软件的文件压缩程序，文件经它压缩过后以 ``.gz`` 为扩展名，据统计，gzip 命令对文本文件有60%～70%的压缩率。**gzip 不能用来压缩目录** ，需要先归档目录，然后在压缩。gzip 和 tar 一起构成了 Linux 操作系统中流行的文件压缩格式（.tar.gz）。

gzip 压缩命令详解
==========================

命令格式：

::

    gzip [ -acdfhklLnNrtvV19 ] [--rsyncable] [-S suffix] [ name ...  ]


选项：

=====    =============    =============
选项       长选项            说明
=====    =============    =============
-a        --ascii          使用ASCII文字模式（多用于非 Unix 系统）
-c        --stdout         将解压后的文件输出到标准输出
-d        --decompress     解压文件
-f        --force          强制压缩或解压。不理会重名文件和文件是否为链接文件
-h        --help           显示帮助信息
-l        --list           列出压缩包中的内容
-q        --quiet          不显示警告信息
-r        --recursive      递归处理，将指定目录下的所有文件及子目录下可以压缩的文件都压缩
-t        --test           检验压缩文件的完整性
-v        --verbose        显示详细的执行过程
-V        --version        显示版本信息
=====    =============    =============

实例
===========================

**压缩一个归档** 压缩前需要先归档：

::

    [root@linuxtechi ~]# gzip test.tar


**查看压缩包中的内容** ：

::

    [root@linuxtechi ~]# gzip -l test.tar.gz


**解压压缩包** 解压后的文件是 tar 归档文件：

::

    [root@linuxtechi ~]# gzip -dv test.tar.gz


**递归的压缩目录** ：

::

    [root@linuxtechi ~]# ls -R
    .:
    Code   Desktop

    ./Code:
    fu.txt  wang

    ./Code/wang:
    gvim.md

    [root@linuxtechi ~]# gzip -rv Code/
    [root@linuxtechi ~]# ls -R
    .:
    Code   Desktop

    ./Code:
    fu.txt.gz  wang

    ./Code/wang:
    gvim.md.gz


**递归的解压目录** ：

::

    [root@linuxtechi ~]# gzip -dr Code/


gunzip 解压命令详解
=============================

gunzip 命令用来解压缩 gz 文件，**事实上 gunzip 就是 gzip 的硬连接**，因此不论是压缩或解压缩，都可以通过 gzip 指令单独完成。

命令格式：

::

    gunzip [ -acfhklLnNrtvV ] [-S suffix] [ name ...  ]


选项：详见 gzip 选项

实例
==============================

**解压压缩包** gunzip 命令等价于 gzip –d ：

::

    [root@mylinux ~]# gunzip /opt/etc.zip.gz

.. _bz2:

bz2
******************************

bzip2是一款自由软件，以开源软件协议发布的数据压缩算法及程序。Linux 下常见的压缩文件格式，以后缀名 .bz2 结尾。

bzip2 压缩命令详解
=============================

命令格式：

::

    bzip2 [ -cdfkqstvzVL123456789 ] [ filenames ...  ]
    bzip2 [ -h|--help ]
    bunzip2 [ -fkvsVL ] [ filenames ...  ]
    bunzip2 [ -h|--help ]
    bzcat [ -s ] [ filenames ...  ]
    bzcat [ -h|--help ]
    bzip2recover filename


选项：

=====    ===============    ================
选项       长选项               说明
=====    ===============    ================
-c        --stdout            将压缩或解压的数据至标准输出的标准输出
-d        --decompress        解压压缩包
-z        --compress          -d 选项的补充（强制执行压缩）
-t        --test              检验压缩包
-f        --force             压缩或解压时，强制覆盖同名文件（默认情况下 bzip2 不会覆盖已经存在的文件）
-k        --keep              在压缩或解压缩时保留源文件（不删除 .bx2 文件）
-q        --quiet             只显示重要的警告信息
-v        --verbose           显示详细的执行过程
-L        --license           显示版本信息
-V        --version           显示版本信息
-h        --help              显示帮助信息
=====    ===============    ================

实例
==============================

**压缩文件** 默认会删除源文件 ：

::

    [root@mylinux ~]# bzip2 filename.txt


**压缩文件并保留源文件**：

::

    [root@mylinux ~]# bzip2 -k filename.txt


**压缩文件并显示详细的过程**：

::

    [root@mylinux ~]# bzip2 -k filename.txt


**解压文件**：

::

    [root@mylinux ~]# bzip2 -d filename.bz2


bunzip2 解压命令详解
================================

bunzip2 命令用来解压缩 bz2 文件，**事实上 bunzip2 就是 bzip2 的硬连接**，因此不论是压缩或解压缩，都可以通过 bzip2 指令单独完成。

命令格式：

::

    bzip2 [ -cdfkqstvzVL123456789 ] [ filenames ...  ]
    bzip2 [ -h|--help ]
    bunzip2 [ -fkvsVL ] [ filenames ...  ]
    bunzip2 [ -h|--help ]
    bzcat [ -s ] [ filenames ...  ]
    bzcat [ -h|--help ]
    bzip2recover filename


选项：详见 bzip2 选项

实例
===============================

**解压压缩包** bunzip2 命令等价于 bzip2 –d ：

::

    [root@mylinux ~]# bunzip2 filename.bz2

.. _zip:

zip
*******************************

zip 是一个应用广泛的跨平台的压缩和打包工具，压缩文件的后缀为 .zip 文件。

zip 压缩命令详解
==============================

命令格式：

::

    zip  [-aABcdDeEfFghjklLmoqrRSTuvVwXyz!@$] [--longoption ...]  [-b path] [-n suffixes] [-t date] [-tt date] [zipfile [file ...]]  [-xi list]

    zipcloak (see separate man page)

    zipnote (see separate man page)

    zipsplit (see separate man page)


选项：

=================    ==========================    ========================
选项                   长选项                          说明
=================    ==========================    ========================
-A                    --adjust-sfx                  调整可执行的自动解压文件
-b path               --temp-path path              指定暂时存放文件的目录（可用于修改压缩包内容）
-c                    --entry-comments              为每个文件添加一行注释
-d                    --delete                      从压缩包内删除指定的文件
-D                    --no-dir-entries              不在压缩包中为目录创建条目
-e                    --encrypt                     以在终端上输入的密码对 zip 压缩包进行加密
-f                    --freshen                     只更新压缩包里的文件（注意与 -u 的区别）
-F                    --fix                         修复 zip 压缩包
-g                    --grow                        添加文件到压缩包中
-h                    --help                        显示帮助信息
-i files              --include files               只压缩指定的文件
-j                    --junk-paths                  压缩目录中的所有文件，但不保存路径关系
-J                    --junk-sfx                    从压缩包中删除所有预先处理的数据
-k                    --DOS-names                   尝试使用与 MS-DOS 兼容的文件名
-l                    --to-crlf                     将 Unix 行末字符 LF 转换为 MSDOS 约定的 CR LF
-ll                   --from-crlf                   将 MSDOS 行末字符 CR LF 转换为 Unix 约定的 LF 
-m                    --move                        将文件移动到压缩包中（此选项会删除源文件）
-n suffixes           --suffixes suffixes           不压缩指定后缀名的文件
-o                    --latest-time                 以压缩包内拥有最新更改时间的文件为准，将压缩文件的更改时间设成和该文件相同
-p                    --paths                       压缩时保留文件的路径关系（默认设置，与 -j 相反）
-P password           --password password           使用密码加密 zip 压缩包
-q                    --quiet                       安静模式（不显示任何提示信息）
-r                    --recurse-paths               递归处理，将指定目录下的所有文件和子目录一起压缩
-s splitsize          --split-size splitsize        如果压缩包超出指定大小，则拆分为多个压缩包
-S                    --system-hidden               压缩时包含系统文件和隐藏文件
-t mmddyyyy           --from-date mmddyyyy          指定压缩包的日期
-T                    --test                        检测压缩包的完整性
-u                    --update                      更新压缩包里的文件，如果某些文件不在压缩包内，那将会把文件加入到压缩包中
-v                    --verbose                     显示详细的执行过程
-x files              --exclude files               压缩时排除符合条件的文件
-X                    --no-extra                    不保留额外的文件属性（如 uid/gid）
-y                    --symlinks                    直接保存符号连接，而不是链接所指向的文件，本参数仅在UNIX之类的系统下有效
-z                    --archive-comment             对压缩包中的文件进行注释
-0~9                                                指定压缩效率，其中 -0 表示没有压缩，速度最快，默认的压缩级别为 -6
=================    ==========================    ========================

实例
=====================

**压缩文件** ：

::

    [root@linuxtechi ~]# zip code.zip code.txt


**压缩文件夹** 也可以偷懒一点只指定文件名，不加 .zip 后缀名：

::

    [root@linuxtechi ~]# zip -r code /home/code


**压缩多个文件夹**

::

    [root@linuxtechi ~]# zip -r code.zip /home/code var/www/


**追加文件到压缩包**

::

    [root@linuxtechi ~]# zip -m myfile.zip ./rpm_info.txt


**指定压缩速率压缩多个文件夹**

::

    [root@linuxtechi ~]# zip -9 -r code.zip /home/code var/www/


**从压缩包中删除文件**

::

    [root@linuxtechi ~]# zip -dv cp.zip a.c


unzip 解压命令详解
===========================

命令格式：

::

    unzip [-Z] [-cflptTuvz[abjnoqsCDKLMUVWX$/:^]] file[.zip] [file(s) ...] [-x xfile(s) ...] [-d exdir]


选项：

=====    ========
选项       说明
=====    ========
-Z        unzip -Z 等于执行 zipinfo 命令
-f        对比文件，只解压磁盘上已经有的文件，并且这些文件比磁盘副本要新
-l        列出压缩包中的所有文件
-t        检验压缩包中的文件
-u        与 -f 参数类似，但是除了更新现有的文件外，也会将压缩文件中的其他文件解压缩到目录中
-v        显示详细的执行过程
-z        只显示压缩文件的备注文字
-C        解压时不区分大小写文件名
-j        不处理压缩文件中原有的目录路径
-L        将压缩包中的全部文件名改为小写
-M        将输出结果送到more程序处理
-n        解压缩时不覆盖原有的文件
-o        解压时不提示用户，直接覆盖原有的文件
-P        使用 zip 的密码选项
-q        安静模式（不显示任何提示信息）
-s        将文件名中的空格转换为下划线
-d        指定解压的文件路径
-x        解压时排除符合条件的文件
=====    ========

实例
=======================

**解压文件** 默认解压到当前目录：

::

    [root@linuxtechi ~]# unzip ../test.zip


**解压文件到指定目录** ：

::

    [root@linuxtechi ~]# unzip ../test.zip -d /media


**查看压缩包中的内容** ：

::

    [root@linuxtechi ~]# unzip -v ../test.zip


**解压文件到指定目录** 如果已有相同的文件存在，要求unzip命令覆盖原先的文件：

::

    [root@linuxtechi ~]# unzip -o ../test.zip -d /media


rar
******************************

待添加
