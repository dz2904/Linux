修改用户目录默认文件名
#############################################

通常，为了方便我会安装 Linux 中文环境，但是此时用户目录也会变成了中文。
``cd 下载/`` 用起来很难受，所以我希望可以将目录名修改为英文或者自定义的名称。

xdg-user-dirs 就是帮助管理用户目录的工具。``xdg-user-dirs-update`` 命令会读取配置文件 /etc/xdg/user-dirs.defaults ，并在 $HOME 下创建一整套默认的经本地化的用户目录。

.. highlight:: none

::

    $ xdg-user-dirs-update

运行该命令后会自动地：

* 创建一个本地的 ~/.config/user-dirs.dirs 配置文件：应用程序通过它来查找使用特定帐号指定的用户目录。

* 创建一个本地的 ~/.config/user-dirs.locale 配置文件：根据使用的 locale 指定语言。

本地的 ~/.config/user-dirs.dirs 和全局的 /etc/xdg/user-dirs.defaults 配置文件都使用如下的环境变量格式： XDG_DIRNAME_DIR="$HOME/目录名"。

下边是一个例子：

::

    ~/.config/user-dirs.dirs
    ------------------------------------------

    XDG_DESKTOP_DIR="$HOME/桌面"
    XDG_DOCUMENTS_DIR="$HOME/文档"
    XDG_DOWNLOAD_DIR="$HOME/下载"
    XDG_MUSIC_DIR="$HOME/音乐"
    XDG_PICTURES_DIR="$HOME/图片"
    XDG_PUBLICSHARE_DIR="$HOME/公共"
    XDG_TEMPLATES_DIR="$HOME/模板"
    XDG_VIDEOS_DIR="$HOME/视频"


因为 xdg-user-dirs 会参照本地配置文件来了解正确的用户目录，所以可以自定义。若将 ~/.config/user-dirs.dirs 下 XDG_DOWNLOAD_DIR 变量设为了 $HOME/Internet，那么任何参照了该变量的程序都会改用这个目录。

注意: 和其他的配置文件一样，本地设定覆盖全局设定。另外自定义的目录要自己创建。

或者也可以用命令行修改默认目录。下列命令会产生和上面一样的效果：

::

    $ xdg-user-dirs-update --set DOWNLOAD ~/Internet

查询配置好的目录

可以用 xdg-user-dirs 来查询配置好的目录路径。例如，下列命令可以查询模板目录的位置，对应于本地配置文件中 XDG_TEMPLATES_DIR 变量的值：

::

    $ xdg-user-dir TEMPLATES


**参考链接：**

* https://wiki.archlinux.org/index.php/XDG_user_directories
* https://www.freedesktop.org/wiki/Software/xdg-user-dirs/
