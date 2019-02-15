rm 删除文件或目录
##########################

rm 命令（remove）可以删除文件或目录，也可以将某个目录及其下属的所有文件及其子目录均删除掉。
对于链接文件，只是删除整个链接文件，而原有文件保持不变。

.. note::

    使用 rm 命令要格外小心。因为一旦删除了一个文件，就无法再恢复它。所以，在删除文件之前，最好再看一下文件的内容，确定是否真要删除。

命令格式：
***********************

.. highlight:: none

::

    rm [OPTION]... FILE...

常用选项：
***********************

::

    -d, --dir
      删除空目录

    -f, --force
      强制删除文件或目录，不给出提示

    -i
      删除文件或目录之前先询问用户

    -r, -R, --recursive
      递归处理，将指定目录下的所有文件与子目录一并删除

    --version
      输出版本信息

使用实例：
***********************

::

    # 删除文件
    [root@localhost bin]# ls
    aclocal  alias  apxs  aulastlog  bin  binBk
    [root@localhost bin]# rm aclocal
    [root@localhost bin]# ls
    alias  apxs  aulastlog  bin  binBk

    # 删除文件前先确认
    [root@localhost bin]# rm -i apxs
    rm: remove regular file ‘apxs’? y
    [root@localhost bin]# ls
    alias  aulastlog  bin  binBk

    # 递归删除文件夹
    [root@localhost bin]# ls -F
    alias*  aulastlog*  bin/  binBk/
    [root@localhost bin]# rm -r binBk/
    [root@localhost bin]# ls
    alias  aulastlog  bin

    # 强制递归删除文件夹（非常危险的命令）
    [root@localhost bin]# rm -rf bin/
    [root@localhost bin]# ls
    alias  aulastlog
