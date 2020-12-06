.. _cmd_less:

less 交互式查看文件内容
####################################

less 是对文件或其它输出进行分页查看的工具，支持前后翻页和搜索等功能。

命令格式：
************************************

.. highlight:: none

::

    less -?
    less [-[+]aABcCdeEfFgGiIJKLmMnNqQrRsSuUVwWX~]
        [-b space] [-h lines] [-j line] [-k keyfile]
        [-{oO} logfile] [-p pattern] [-P prompt] [-t tag]
        [-T tagsfile] [-x tab,...] [-y lines] [-[z] lines]
        [-# shift] [+[+]cmd] [--] [filename]...
    
    
常用选项：
************************************

::

    -e, --quit-at-eof
    当文件显示结束后，自动退出

    -i, --ignore-case
    搜索时忽略大小写

    -N or --LINE-NUMBERS
    显示行号


使用 less 查看文件时，常用操作命令：

===============     ===============
命令                 说明
===============     ===============
h                    显示帮助
/                    向下搜索
?                    向上搜索
n                    下一个搜索（与 / 或 ? 有关）
N                    上一个搜索（与 / 或 ? 有关）
f                    向下翻一页
b                    向上翻一页
d                    向下翻半页
u                    向上翻半页
j                    向下翻一行
k                    向上翻一行
q                    退出
===============     ===============
