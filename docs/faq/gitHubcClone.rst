克隆 GitHub 到本地修改
##########################

GitHub 是一个面向开源及私有软件项目的托管平台，因为只支持 git 作为唯一的版本库格式进行托管，故名 GitHub。

基本的使用流程为：克隆项目到本地 > 创建新分支 > 修改代码 > 将改动推送回 GitHub > 到 GitHub 中合并分支。

.. highlight:: none

::

    # 将项目克隆到本地
    $ git clone https://github.com/tonychacon/blink
    Cloning into 'blink'...

    $ cd blink

    # 创建新分支
    $ git checkout -b slow-blink
    Switched to a new branch 'slow-blink'

    # 修改代码
    $ sed -i '' 's/1000/3000/' blink.ino

    # 将修改提交到分支中
    $ git commit -a -m 'three seconds is better'
    [slow-blink 5ca509d] three seconds is better
    ...

    # 将新分支推送回 GitHub
    $ git push origin slow-blink
    Username for 'https://github.com': tonychacon
    Password for 'https://tonychacon@github.com':
    Counting objects: 5, done.
    ...
    
    # 上传所有标签
    git push origin --tags


最后到 GitHub 上查看分支并创建合并请求。
