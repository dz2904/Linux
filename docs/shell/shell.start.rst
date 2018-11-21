shell 脚本之始
############################

世界上对 shell 脚本最好的概念性介绍来自一个老的 AT&T 培训视频 。在视频中，Brian W. Kernighan（awk 中的“k”），Lorinda L. Cherry（bc 作者之一）论证了 UNIX 的基础原则之一是让用户利用现有的实用程序来定制和创建复杂的工具。

POSIX 操作系统本身就像是一个 API。如果你能弄清楚如何在 POSIX 的 shell 中完成一个任务，那么你可以自动化这个任务。这就是编程，这种日常 POSIX 编程方法的主要方式就是 shell 脚本。

像它的名字那样，shell 脚本就是一行一行的你想让计算机执行的语句，就像你手动的一条条输入一样。

当您打开终端窗口时，就是打开了 shell 。shell 有好几种，本教程适用于 bash、tcsh、ksh、zsh 和其它几个。如果你是新手，只需使用 bash 。它是一个很好的 shell，有许多友好的功能，它是 Linux、Cygwin、WSL、Mac 默认的 shell，并且在 BSD 上也支持。

Hello world
***************************

您可以从终端窗口生成您自己的 hello world 脚本 。注意引号，单引号和双引号会有不同的效果（译注：想必你不会在这里使用中文引号吧）。

::

    $ echo "#!/bin/bash" > hello.sh
    $ echo "echo 'hello world' " >> hello.sh


像应用程序一样运行脚本：

::

    $ chmod +x hello.sh
    $ ./hello.sh
    hello world


不要惊讶，这就是一个 shell 脚本了。
