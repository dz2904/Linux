重新认识 Shell
####################################

从刚刚开始使用 Linux 时，就会有很多 Shell 的话题，那么 Shell
到底是什么呢？

Shell 是一个应用程序，充当用户界面和脚本解释器，是用户使用 Linux
系统的桥梁。它即是一个程序也是一种脚本语言。

因为 Shell 是一个程序，所以在 Linux 环境中有众多的 shell 程序，但是它们主要分为两大家族（Shell 家族之间的差异会比较大，同一家族的差异相对较小）：

- Bourne Shell 家族： sh、ksh、bash
- C-Shell 家族： csh、tcsh

因为 Linux 系统中大部分默认使用 Bash，所以们主要以它主，介绍一下几个方面。


- 环境变量
- shell 变量
- shell 选项
- 元字符
- 引用
- 内置命令
- 搜索路径
- 命令替换
- 历史列表
- 自动补全
- 命令行编辑
- 别名
- 初始化文件
- 注释

.. attention::
    
    attention 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. caution::
    
    caution 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. danger::
    
    精danger:通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. error::
    
    error 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. hint::
    
    hint 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. warning::
    
    warning 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. important::
    
    important 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。

.. tip::
    
    精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。



环境变量
************************************

shell脚本（shell script），是一种为 shell 编写的脚本程序。

