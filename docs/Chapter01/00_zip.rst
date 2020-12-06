.. _cmd_zip:

zip 归档和压缩文件
####################################

zip 不同于 gzip 压缩命令，它可直接用于压缩文件夹，压缩文件的扩展名为 ``.zip`` 。解压命令为  :doc:` unzip <../Chapter01/00_unzip>`


命令格式：
************************************

.. highlight:: none

::

    zip  [-aABcdDeEfFghjklLmoqrRSTuvVwXyz!@$] [--longoption ...]  [-b path] [-n suffixes] [-t date] [-tt date] [zipfile [file ...]]  [-xi list]

    zipcloak (see separate man page)

    zipnote (see separate man page)

    zipsplit (see separate man page)



常用选项：
************************************

::

    -d --delete
        从压缩包内删除指定的文件

    -e --encrypt
        以在终端上输入的密码对 zip 压缩包进行加密

    -f --freshen
        只更新压缩包里的文件（注意与 -u 的区别）

    -F --fix
        修复 zip 压缩包

    -g --grow
        添加文件到压缩包中

    -i files
    --include files
        只压缩指定的文件

    -j --junk-paths
        压缩目录中的所有文件，但不保存路径关系

    -k --DOS-names
        尝试使用与 MS-DOS 兼容的文件名

    -m --move
        将文件移动到压缩包中

    -n suffixes
    --suffixes suffixes
        不压缩指定后缀名的文件

    -o --latest-time
        更新压缩时间，以压缩包内最新更改时间的文件为准

    -p --paths
        压缩时保留文件的路径关系（默认设置，与 -j 相反）

    -P password
    --password password
        使用密码加密 zip 压缩包（注意与 -e 的区别）

    -q --quiet
        安静模式（不显示任何提示信息）

    -r --recurse-paths
        递归处理，将目录下的所有文件和子目录一起压缩

    -s splitsize
    --split-size splitsize
        如果压缩包超出指定大小，则拆分为多个压缩包

    -S --system-hidden
        压缩时包含系统文件和隐藏文件

    -t mmddyyyy
    --from-date mmddyyyy
        指定压缩包的日期

    -T --test
        检测压缩包的完整性

    -u --update
        更新压缩包里的文件，如果文件不在压缩包内，将加入到压缩包中

    -v --verbose
        显示详细的执行过程

    -x files
    --exclude files
        压缩时排除符合条件的文件

    -X --no-extra
        不保留额外的文件属性（如 uid/gid）

    -z --archive-comment
        对压缩包中的文件进行注释

    -0~9
        指定压缩效率，其中 -0 表示没有压缩（相当于 tar），默认的压缩级别为 -6


使用实例：
************************************

::

    # 压缩单个文件，要压缩的文件必须在末尾
    [Linux]$ zip code.zip code.txt

    # 压缩多个文件夹
    [Linux]$ zip -r code.zip /home/code var/www/

    # 指定压缩速率压缩多个文件夹
    [Linux]$ zip -9 -r code.zip /home/code var/www/
