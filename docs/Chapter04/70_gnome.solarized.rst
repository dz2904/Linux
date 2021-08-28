GNOME 终端使用 solarized 配色
####################################

首先，下载配色文件，配色文件分为两种一个亮色主题一个暗色主题，选择喜欢的安装。也可以使用 ``./set_light.sh`` 进行预览。

.. highlight:: none

::

    [Linux]$ git clone git://github.com/sigurdga/gnome-terminal-colors-solarized.git
    [Linux]$ cd gnome-terminal-colors-solarized
    [Linux]$ ./install.sh

安装成功后进入终端设置主题颜色。

.. hint::

    主题在使用 ls 命令时，权限为 777 的文件夹会看不清楚。可以在 ``~/.bashrc`` 文件中加入以下配置禁用文件夹背景色。

    ::

        LS_COLORS='ex=0;35:ow=1;32'
