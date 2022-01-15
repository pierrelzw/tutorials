# python环境配置简易教程

[TOC]



本文主要介绍 anaconda + jupyterlab，以及 git（版本管理工具） 的安装，这是满足阅读&学习《自学是门手艺》这本书所需的 “最少必要环境”。

- Anaconda ： 最方便的 python 包管理工具，也是用来安装 python 最方便的工具
- jupyter lab：用来运行 python notebook文件（以.ipynb结尾）的环境

## Git 安装

### mac

参考 [git简介](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/T-appendix.git-introduction.ipynb) 安装就行，应该没难度

### windows

1. 前往 [https://gitforwindows.org](https://gitforwindows.org/) 下载并安装 Git for Windows
2. 一路点 “确认” 就行。当然，下图这个环节最好换成 rebase，不过等你了解了 rebase，到时候再换也是可以的。

![image-20220113223527860](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/image-20220113223527860.png)

3. 测试 git 安装是否成功：和上一节一样，启动菜单栏，选择 Anaconda Powershell Prompt 打开命令行，输入 git 然后回车

![image-20220113223712129](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/image-20220113223712129.png)



## Anconda + jupyterlab 安装

### MacBook
参考 [Jupyterlab 的安装与配置](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/T-appendix.jupyter-installation-and-setup.ipynb) 即可，最少必要要求是配置到 “第一次启动 Jupyter lab” 部分


### Windows


1. 下载 [Anaconda3-2020.02-Windows-x86_64.exe](https://repo.anaconda.com/archive/Anaconda3-2020.02-Windows-x86_64.exe)  对应的是python3.7，和李骏老师编程课使用的python版本一致

2. 点击安装，默认选项，一路点 “确定” 就行

3. 确认安装是否成功：

   - 点击桌面菜单，点击 Anaconda Powershell Prompt（也可以在左下角直接搜索这个内容），打开terminal

   

   <img src="https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/conda_tutorial.png" alt="image-20220113222347932" style="zoom:33%;" />

   - 如果有terminal开头有下图中` (base)` ——表示 Anaconda 模型环境，说明安装 & 启动成功

   <img src="https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/image-20220113222537775.png" alt="image-20220113222537775" style="zoom:33%;" />

   - 你还可以跑一下下面的这些命令来，看看都会输出什么，还可以顺便搜一下这些命令是什么意思

     ```
     which python
     python --version
     which node
     node -v
     which jupyter
     jupyter lab --version
     jupyter notebook --version
     which pip
     pip --version
     ```

   有实践、有阅读（+理解）—— 一次锻炼 “自学能力” 的好机会

   以上是最简单的使用方法。关于 conda 、jupyterlab的使用，你还可以继续搜索，全面探索。比如向 google 询问：“how to use conda”， “conda tutorial”， “conda cheatsheet” …… （把 conda 换为jupyterlab，这些问题对学习后者的使用也同样适用）



## fork代码、用 git clone 下载自己书籍

1. 注册 github 账号（再一次，你最好有个科学上网工具，迟早都要做不如尽快把它搞定）

2. 登录自己的 github 账户

3. 浏览器打开 [《自学是门手艺》](https://github.com/selfteaching/the-craft-of-selfteaching) github 网页，fork 代码到自己的仓库

   ![image-20220115234939850](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/fork.png)

4. 确认 fork 成功 -> 点击 code -> 点击复制(github repo 地址) 

![image-20220115235055660](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/fork_finished.png)

5. 用 git clone 下载书籍：`git clone [上一步复制的地址]`

可能的错误

![image-20220113230416650](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/error_1.png)

![image-20220113224158058](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/image-20220113224158058.png)

最好是能按照 [共读学编程活动说明](https://s7wabnxfe5.feishu.cn/docs/doccnzs4dwITfISraEbOSbS8FyQ#hVSvEw) 所说，安装科学上网工具后运行。否则，就只能多次重复运行同样的命令看运气了。

>  小技巧：多次运行同样命令并不需要每次都手动输入：在命令里通过键盘“上”、“下” 箭头，可以找到之前运行过的命令

## 参考资料

- [《自学是门手艺》](https://github.com/selfteaching/the-craft-of-selfteaching)