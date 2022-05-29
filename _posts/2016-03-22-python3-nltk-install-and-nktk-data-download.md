---
layout: post
title: "Python3 下的 NLTK 及 nltk_data 安装问题（Ubuntu环境）"
tags: [Linux, Python, NLTK]
excerpt_separator: <!--more-->
---

> 测试环境：Ubuntu Kylin 14.04  
> Python版本：3.4.3

在使用 `Python` 强大的第三方库nltk进行一些自然语言处理工作的时候遇到了一些困难，折腾一番总算解决。现在在这里记录一下，当作备忘。
 
网上找到挺多安装 `nltk` 的教程，但经测试，好像都是适用于 `Python2` 的，对于 `Python3` 就勉为其难了。这里的主要问题是，前辈们分享的 `nltk_data` 包是不兼容 `Python3` 的。

<!--more-->

所以我的解决方案是：
到 [https://github.com/nltk/nltk_data](https://github.com/nltk/nltk_data) 下载 `gh-pages` 分支，里面的 `Packages` 就是我们要的资源。（注：截至2016年3月24日时本方案仍有效）
 
详细情况记录如下：
## 1、安装 nltk
截至今天，安装的是 `nltk3.2`，在有pip这些工具的情况下，安装这些库变得非常简单：
`pip install nltk`
另外官方的安装说明还附带了 `Numpy`，一个“赛 `Matlab` 的 `Python` 开源的数值计算扩展库”，说不定以后用得上：
`pip install numpy`

![nltk install](/assets/img/nltk1.jpg)
  
  

## 2、下载 nltk_data
`nltk_data` 是必要的 `nltk` 数据包，实现分词，词性标注，命名实体识别等功能都要用到这个数据包：
进入Python Shell后只需要两句就可以进入nltk_data的下载
```bash
$python
>>>import nltk
>>>nltk.download()
```
然后就可以按提示进行了。

![nltk download](/assets/img/nltk2.jpg)
  
  

## 3、手动下载 nltk_data
重点是，下载会很慢或干脆卡住。此时需要手动下载 `nltk_data` 包。
 
 
下载方法：到 [https://github.com/nltk/nltk_data](https://github.com/nltk/nltk_data) 下载`gh-pages`分支，里面的`Packages`就是我们要的资源。


里面有部分是压缩包，需要解压。


将 Packages 文件夹改名为 nltk_data，放在以下任一路径：`/usr/lib`、`/usr/local/lib`、
用户目录（比方说可能是`/home/bennu`）、其他 nltk 可以识别的路径，出错时有可能会在出错信息看到。

为了方便，我已将此文件包打包处理好上传到百度网盘：
 
~~[http://pan.baidu.com/s/1i4soBTb](http://pan.baidu.com/s/1i4soBTb)  密码：u3kn~~  

2018-05-16 更新：  
~~链接: [https://pan.baidu.com/s/1UfJhJY9bJ9hqeTuzKxgQvw](https://pan.baidu.com/s/1UfJhJY9bJ9hqeTuzKxgQvw)  密码: 2ce5~~  

2019-05-30 最后更新：    
链接：[https://pan.baidu.com/s/1iJGCrz4fW3uYpuquB5jbew](https://pan.baidu.com/s/1iJGCrz4fW3uYpuquB5jbew)  提取码：o5ea   
注：如果链接失效，请各位去 GitHub 下载哈

剩下就是继续努力学习了～

