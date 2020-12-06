.. _cmd_gzip:

gzip 压缩文件
####################################

gzip 是使用广泛的压缩程序，文件经压缩过后以 ``.gz`` 的扩展名结尾。gzip 只能针对一个文件进行压缩，当要压缩多个文件时，需要将文件 :doc:`00_tar` 。

gunzip 命令用来解压 gz 文件，事实上 gunzip 就是 gzip 的硬连接（ ``gzip –d`` 等价于 ``gunzip``  ），命令选项基本相同。


命令格式：
************************************

.. highlight:: none

::

    gzip [ -acdfhklLnNrtvV19 ] [-S suffix] [ name ...  ]
    gunzip [ -acfhklLnNrtvV ] [-S suffix] [ name ...  ]


常用选项：
************************************

::

    -a --ascii
        使用ASCII文字模式（多用于非 Unix 系统）

    -c --stdout --to-stdout
        将解压后的文件输出到标准输出

    -d --decompress --uncompress
        解压文件

    -f --force
        强制压缩或解压。不理会重名文件和文件是否为链接文件

    -h --help
        显示帮助信息

    -l --list
        列出压缩包中的内容

    -q --quiet        
        不显示警告信息

    -r --recursive   
        递归处理，将指定目录下的所有文件及子目录下可以压缩的文件都压缩

    -t --test
        检验压缩文件的完整性

    -v --verbose
        显示详细的执行过程

    -V --version
        显示版本信息


使用实例：
************************************

::

    # 压缩一个归档
    [Linux]$ gzip test.tar
    [Linux]$ ls
    test.tar.gz

    # 查看压缩包中的内容
    [Linux]$ gzip -l test.tar.gz
             compressed        uncompressed  ratio uncompressed_name
                 879277             2908160  69.8% test.tar

    # 解压压缩包，解压后的文件是 tar 归档文件
    [Linux]$ gzip -dv test.tar.gz
    test.tar.gz:	 69.8% -- replaced with test.tar

    # 直接解压出压缩包内的文件
    [Linux]$ tar -zxf test.tar.gz
    
    # 压缩多个文件
    [Linux]$ tar -zcvf test.tar.gz /etc/ /opt/
