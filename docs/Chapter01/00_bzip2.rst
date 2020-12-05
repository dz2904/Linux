bzip2 压缩文件
####################################

bzip2 用于压缩文件，文件经压缩后以 ``.bz2`` 的扩展名结尾。bzip2 只能针对一个文件进行压缩，当要压缩多个文件时，需要将文件 :doc:`../Chapter01/00_tar` 。

bunzip2 命令用来解压 bz2 文件，事实上 bunzip2 就是 bzip2 的硬连接（ ``bzip2 –d`` 等价于 ``bunzip2``  ），命令选项基本相同。


命令格式：
************************************

.. highlight:: none

::

    bzip2 [ -cdfkqstvzVL123456789 ] [ filenames ...  ]
    bzip2 [ -h|--help ]
    bunzip2 [ -fkvsVL ] [ filenames ...  ]
    bunzip2 [ -h|--help ]


常用选项：
************************************

::

    -c --stdout
        将压缩或解压的数据传至标准输出

    -d --decompress
        解压压缩包

    -z --compress
        -d 选项的补充（强制执行压缩）

    -t --test
        检验压缩包

    -f --force
        压缩或解压时，强制覆盖同名文件（默认不覆盖已存在的文件）

    -k --keep
        在压缩或解压缩时保留源文件（不删除 .bz2 文件）

    -q --quiet
        只显示重要的警告信息

    -v --verbose
        显示详细的执行过程


使用实例：
************************************

::

    # 压缩文件，默认会删除源文件
    [Linux]$ bzip2 test.tar 
    [Linux]$ ls
    test.tar.bz2

    # 压缩文件并保留源文件
    [Linux]$ bzip2 -k test.tar 
    [Linux]$ ls
    test.tar  test.tar.bz2

    # 解压压缩包，解压后的文件是 tar 归档文件
    [Linux]$ bzip2 -d test.tar.bz2
    
    # 直接解压出压缩包内的文件
    [Linux]$ tar -jxvf test.tar.bz2
    
    # 压缩多个文件
    [Linux]$ tar -jcvf text.tar.bz2 /etc/ /opt/
