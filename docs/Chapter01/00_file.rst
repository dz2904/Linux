.. _cmd_file:

file 识别文件类型
####################################

file 命令用来识别文件类型，也可用来识别文件的编码格式。它通过文件的头部信息来获取文件类型，而不是简单通过扩展名来识别文件类型。


命令格式：
************************************

.. highlight:: none

::

    file [-bcdEhiklLNnprsSvzZ0] [--apple] [--extension] [--mime-encoding] [--mime-type]
         [-e testname] [-F separator] [-f namefile] [-m magicfiles] [-P name=value]
         file ...
    file -C [-m magicfiles]
    file [--help]

    
常用选项：
************************************

::

-b, --brief
    不显示文件名称（简要模式）

-c, --checking-printout
    显示详细执行过程，便于排错或分析程序执行的情形

-f, --files-from namefile
    指定名称文件，识别文件中列出的所有文件

-i, --mime
    显示 MIME 类别

-L, --dereference
    直接显示符号连接所指向的文件类别

-z, --uncompress
    尝试去解读压缩文件的内容


使用实例：
************************************

::

    [Linux]$  file file.rst 
    file.rst: UTF-8 Unicode text, with very long lines

    # 显示 MIME 类别
    [Linux]$  file -i file.rst 
    file.rst: text/plain; charset=utf-8
    
    # 识别目录文件
    [Linux]$  file docs/
    docs/: directory

    # 识别压缩文件
    [Linux]$  file abc.zip 
    abc.zip: Zip archive data, at least v1.0 to extract
