.. _cmd_ssh-keygen:

ssh-keygen 生成密钥
####################################

ssh-keygen 命令是 :doc:`OpenSSH <../Chapter02/00_openssh>` 程序提供的一个密钥生成、管理和转换工具，支持 RSA 和 DSA 两种认证密钥。生成的密钥文件默认存储在 ``~/.ssh/`` 目录下，id_rsa 为私钥文件，id_rsa.pub 为公钥文件。 

.. hint:: RSA 和 DSA 加密算法

    RSA 和 DSA 都是非对称加密算法。其中 RSA 的安全性是基于极其困难的大整数的分解（两个素数的乘积）；DSA 的安全性是基于整数有限域离散对数难题。基本上相同密钥长度的 RSA 算法与 DSA 算法安全性相当。

    DSA 只能用于数字签名，无法用于加密（某些扩展可以支持加密）；RSA 即可作为数字签名，也可以作为加密算法。不过作为加密使用的 RSA 有着随密钥长度增加，性能急剧下降的问题。

    相同密钥长度下，DSA （在扩展支持下）解密密文更快，而加密更慢；RSA 正好反过来，一般来说解密次数多于加密次数。不过由于非对称加密算法的先天性能问题，两者都不是加密的好选择。

    通常情况下 RSA 是首选，它具有更为广泛的部署与支持。


.. attention::

    生成密钥以后，建议修改私钥的权限，防止其它用户读取。

    ::

        [Linux]$ chmod 600 ~/.ssh/id_rsa


命令格式：
************************************

.. highlight:: none

::

    ssh-keygen [OPTION]... 

    
常用选项：
************************************

::

    -b
        指定密钥的二进制位数，建议设为 2048 或者更高

    -C
        为密钥文件指定新的注释，格式为 username@host

    -f
        指定生成的文件名

    -F
        检查某个主机名是否在 known_hosts 文件里面。

    -N
        指定私钥的密码

    -p
        重新指定私钥的密码。它与 -N 不同，新密码不在执行后再输入

    -R
        将指定的主机公钥指纹移出 known_hosts 文件。

    -t
        参数用于指定生成密钥的加密算法，一般为dsa或rsa


使用实例：
************************************

::

    # 默认问题
    [Linux]$ ssh-keygen -t dsa
    Generating public/private dsa key pair.

    # 可以指定文件路径，直接回车即可
    Enter file in which to save the key (/home/username/.ssh/id_dsa):  press ENTER

    # 设置密码，该密码是每次使用密钥时需要的密码，直接回车可以不设密码
    Enter passphrase (empty for no passphrase): ********
    Enter same passphrase again: ********

    # 公钥和私钥的存放路径和密钥指纹
    Your identification has been saved in /home/username/.ssh/id_dsa.
    Your public key has been saved in /home/username/.ssh/id_dsa.pub.
    The key fingerprint is:
    14:ba:06:98:a8:98:ad:27:b5:ce:55:85:ec:64:37:19 username@shell.isp.com


    # 指定加密算法、位数和注释
    [Linux]$ ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
