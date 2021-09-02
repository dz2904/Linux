GNOME 终端使用 solarized 配色
####################################

首先，下载配色文件，配色文件分为两种一个亮色主题一个暗色主题，选择喜欢的安装。也可以使用 ``./set_light.sh`` 进行预览。

.. highlight:: none

::

    [Linux]$ git clone git://github.com/sigurdga/gnome-terminal-colors-solarized.git
    [Linux]$ cd gnome-terminal-colors-solarized
    [Linux]$ ls -l
    total 36
    drwxr-xr-x 5 glenn glenn 4096 Aug 28 10:31 colors
    -rwxr-xr-x 1 glenn glenn 5285 Aug 28 10:31 install.sh
    -rw-r--r-- 1 glenn glenn 1077 Aug 28 10:31 LICENSE.mkd
    -rw-r--r-- 1 glenn glenn 4376 Aug 28 10:31 README.mkd
    -rwxr-xr-x 1 glenn glenn   93 Aug 28 10:31 set_dark.sh
    -rwxr-xr-x 1 glenn glenn   94 Aug 28 10:31 set_light.sh
    drwxr-xr-x 2 glenn glenn 4096 Aug 28 10:31 src
    [Linux]$ ./install.sh

安装成功后进入终端设置主题颜色。

.. hint::

    主题在使用 ls 命令时，权限为 777 的文件夹会看不清楚。可以在 ``~/.bashrc`` 文件中加入以下配置禁用文件夹背景色。

    ::

        LS_COLORS='ex=0;35:ow=1;32'
