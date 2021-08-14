Shell 脚本之始
############################

Shell 脚本是一种以 Shell 程序为解释器的脚本程序。由于习惯的原因，业界所说的“Shell 编程”都是指编写 Shell 脚本，不是指 Shell 程序扩展开发。

Linux 操作系统本身就像是一个 API，所以 Shell 编程跟常见的解释型语言（Python、PHP、Ruby）一样，只要有一个文本编辑器和一个脚本解释器就可以了。


Hello world
***************************

在终端窗口新建一个 ``hello.sh`` 脚本文件（扩展名并不影响脚本执行）。输入以下内容：

::

    [Linux]$ echo "#!/bin/bash" > hello.sh
    [Linux]$ echo "echo 'hello world' " >> hello.sh


运行脚本：

::

    [Linux]$ chmod +x hello.sh
    [Linux]$ ./hello.sh
    hello world


不要惊讶，这就是一个 shell 脚本了。

.. attention::

    在执行脚本时一定要写成 ``./hello.sh`` ，直接写 ``hello.sh`` ，Linux 系统会去系统变量 PATH 里寻找有没有 test.sh 的命令，要用 ``./test.sh`` 告诉系统，就在当前目录找。
