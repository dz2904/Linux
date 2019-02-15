cp 复制文件或目录
##########################

cp 命令用来将文件（或目录）复制到指定的目的文件（或目录）。
cp 命令还支持同时复制多个文件，当一次复制多个文件时，目标文件指定的目录路径必须是己经存在的，cp命令不能创建目录。

命令格式：
***********************

.. highlight:: none

::

    cp [OPTION]... [-T] SOURCE DEST
    cp [OPTION]... SOURCE... DIRECTORY
    cp [OPTION]... -t DIRECTORY SOURCE...

常用选项：
***********************

::

    -a, --archive
      通常在复制目录时使用，它会保留链接、文件属性，并复制目录下的所有内容。其作用等于 dpR 参数组合

    -b
      覆盖已存在的文件目标前将目标文件备份

    -d
      当复制符号连接时，把目标文件或目录也建立为符号连接，并指向与源文件或目录连接的原始文件或目录

    -f
      强制复制，覆盖已经存在的目标文件时不给出提示

    -i, --interactive
      在覆盖目标文件之前给出提示，要求用户确认是否覆盖

    -l, --link
      对源文件建立硬连接，而非复制文件

    -n, --no-clobber
      不要覆盖现有文件

    -p
      除复制文件的内容外，同时复制修改时间和访问权限等

    -R, -r, --recursive
      递归处理，将指定目录下的文件与子目录一并复制

    -s, --symbolic-link
      对源文件建立符号连接，而非复制文件

    --version
      输出版本信息

使用实例：
***********************

::

    # 复制文件并重命名
    [root@localhost bin]# ls
    aclocal  alias  apxs  aulastlog  bin
    [root@localhost bin]# cp alias bin/aaa
    [root@localhost bin]# ls -l bin/
    total 4
    -rwxr-xr-x 1 root root 29 Feb 15 13:57 aaa

    # 复制多个文件到文件夹
    [root@localhost bin]# cp apxs aulastlog bin/
    [root@localhost bin]# ls bin/
    aaa  apxs  aulastlog

    # 递归复制整个文件目录
    [root@localhost bin]# ls -R
    .:
    aclocal  alias  apxs  aulastlog  bin  binBk

    ./bin:
    aaa  apxs  aulastlog  cp

    ./bin/cp:
    cp

    ./binBk:

    [root@localhost bin]# cp -r bin binBk/
    [root@localhost bin]# ls -R
    .:
    aclocal  alias  apxs  aulastlog  bin  binBk

    ...

    ./binBk:
    bin

    ./binBk/bin:
    aaa  apxs  aulastlog  cp

    ./binBk/bin/cp:
    cp

    # 交互式地复制文件
    [root@localhost bin]# ls bin/
    aaa  apxs  aulastlog  cp
    [root@localhost bin]# cp -i apxs bin/
    cp: overwrite ‘bin/apxs’? y
    [root@localhost bin]#
