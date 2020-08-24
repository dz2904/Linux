env 查看环境变量
####################################

env 命令可以查看环境变量，或在修改过的环境中运行程序。

命令格式：
************************************

.. highlight:: none

::

    env [OPTION]... [-] [NAME=VALUE]... [COMMAND [ARG]...]


常用选项：
************************************

::

    -i, --ignore-environment
        从一个空环境启动


使用实例：
************************************

::

    
    SHELL=/bin/bash
    SESSION_MANAGER=local/glenn-ThinkPad:@/tmp/.ICE-unix/2450,unix/glenn-ThinkPad:/tmp/.ICE-unix/2450
    QT_ACCESSIBILITY=1
    COLORTERM=truecolor
    XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
    XDG_MENU_PREFIX=gnome-
    GNOME_DESKTOP_SESSION_ID=this-is-deprecated
    GTK_IM_MODULE=ibus
    LANGUAGE=en_US:en
    QT4_IM_MODULE=ibus
    LC_ADDRESS=en_US.UTF-8
    GNOME_SHELL_SESSION_MODE=ubuntu
    LC_NAME=en_US.UTF-8
    SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
    XMODIFIERS=@im=ibus

