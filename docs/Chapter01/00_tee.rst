.. _cmd_tee:

tee 输出分流
####################################

tee 命令会从标准输入读取数据，将数据输出到标准输出，同时保存到文件中。如果指定的文件不存在，那么 tee 命令会创建这个文件；如果文件存在则覆盖原有文件中的内容。


命令格式：
************************************

.. highlight:: none

::

    tee [OPTION]... [FILE]...


常用选项：
************************************

::

    -a, --append
      追加到原有文件的后面，而非覆盖它

    -i, --ignore-interrupts
      忽略中断信号


使用实例：
************************************

::

    # 将输出保存到多个文件中
    [Linux]$ ls | tee a.txt b.txt
    a.txt
    b.txt
    Desktop
    Documents
    Downloads
    Music
    Pictures
    Public
    Templates

    # 将命令输出保存到文件中
    [Linux]$ who | tee w.txt
    glenn    :1           2020-08-23 20:52 (:1)
    glenn@glenn-ThinkPad:~$ cat w.txt 
    glenn    :1           2020-08-23 20:52 (:1)

    # 查看多个文件中的内容，并保存
    [Linux]$ cat a.txt b.txt | tee all.txt | less
