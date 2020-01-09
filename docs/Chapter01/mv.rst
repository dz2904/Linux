mv 移动文件或重命名
##########################

mv 命令（move）用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录中。

mv命令可以用来将源文件移至一个目标文件中，或将一组文件移至一个目标目录中。源文件被移至目标文件有多种不同的结果：

================   ===========
命令                 说明
================   ===========
mv 文件名 文件名	    重命名，将源文件名改为目标文件名
mv 文件名 目录名	    移动文件，将文件移动到目标目录
mv 目录名 目录名	    目标目录存在，则将源目录移动到目标目录；目标目录不存在则重命名
mv 目录名 文件名	    报错
================   ===========

命令格式：
***********************

.. highlight:: none

::

    mv [OPTION]... [-T] SOURCE DEST
    mv [OPTION]... SOURCE... DIRECTORY
    mv [OPTION]... -t DIRECTORY SOURCE...

常用选项：
***********************

::

    --backup[=CONTROL]
      若需覆盖文件，则覆盖前先前的备份

    -f, --force
      强制移动，若目标文件（或目录）与现有的文件重复，则直接覆盖现有的文件

    -i
      交互式操作，如果源文件与目标文件同名，覆盖前先行询问用户

    --version
      输出版本信息

使用实例：
***********************

::
    
    # 重命名文件
    [root@localhost bin]# ls
    alias  aulastlog
    [root@localhost bin]# mv aulastlog aaa
    [root@localhost bin]# ls
    aaa  alias

    # 移动文件到指定目录
    [root@localhost bin]# ls
    alias  aulastlog bin
    [root@localhost bin]# mv alias bin/
    [root@localhost bin]# ls
    aaa  bin

    # 移动文件夹
    [root@localhost bin]# ls -F
    aaa*  bin/  usr/
    [root@localhost bin]# mv bin/ usr/
    [root@localhost bin]# ls -l usr/
    total 0
    drwxr-xr-x 2 root root 19 Feb 15 15:33 bin
