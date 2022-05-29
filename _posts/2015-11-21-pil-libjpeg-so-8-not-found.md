---
layout: post
title: "安装libjpeg库后提示 libjpeg.so.8 不存在（linux环境）"
tags: [Linux, Pillow, Libjpeg.so.8]
excerpt_separator: <!--more-->
---

作为一个 Python3 初入门者，我的第一个练手项目是写一个爬虫，需要处理验证码，所以用了 Pillow 模块。

程序写好后，在本地的 Windows 上用得好好的，但移植到云服务器 （CentOS）却出了问题，先是提示 `libjpeg` 和 `libpng` 相关库不存在，搜索网上的教程安装后，在python的命令行输入：from PIL import Image，却又抛出错误：
**libjpeg.so.8: cannot open shared object file: No such file or directory**

<!--more-->

研究了很久，才发现原来是还没有添加相关的环境变量。

在StackOverflow上有一个非常贴切的问答：[http://stackoverflow.com/questions/5545580/pil-libjpeg-so-8-cannot-open-shared-object-file-no-such-file-or-directory](http://stackoverflow.com/questions/5545580/pil-libjpeg-so-8-cannot-open-shared-object-file-no-such-file-or-directory)  

但我按照最高票回答：
> A quick fix is to add the directory that contains libjpeg.so.8 to your /etc/ld.so.conf file, and then run ldconfig

将libjpeg.so.8的路径`/usr/local/lib`添加到`/etc/ld.so.conf` 文件：
```bash
include /usr/local/lib
```

但却不起作用。

参考第二个回答，命令行输入：
```bash
export LD_LIBRARY_PATH=/usr/local/lib
```

就可以了。但这个方法是一次性的，这个设置只保存在内存，关闭终端再开启就没了。

网上搜索后发现`~/.bashrc`文件在每次登陆和每次打开 shell 都读取一次，`~/.bash_profile` 在登录时读取一次，所以可以把配置语句写进其中一个：
```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```

可以直接命令行输入：
```bash
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib' >> ~/.bashrc
```

也可以用vim编辑器打开编辑。
这样每次打开终端，系统就实现自动配置了。


顺便记一下vim编辑器最简单的几个操作：

1. 打开文件： vim /文件目录/文件名  
2. 编辑文件： 按键盘上的字母键i  
3. 保存退出： 按Esc键，再输入:wq，Enter搞掂

记住以上几步就可以应付简单的修改配置文件任务了。  
  

  
  
