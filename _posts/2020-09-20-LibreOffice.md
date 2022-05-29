---
layout: post
title: "LibreOffice，办公文档格式转换的利器"
tags: [LibreOffice, LibreOffice Online, 文档格式转换]
excerpt_separator: <!--more-->
---

本文描述了使用 LibreOffice 搭建办公文档格式转换服务的基本步骤。


LibreOffice 提供 LibreOffice Local 和 LibreOffice Online 两个版本，即一个提供本地接口、一个提供远程接口的版本。

## 1、LibreOffice Local
LibreOffice Local 即 LibreOffice 软件，LibreOffice 作为默认办公处理软件预安装在众多 Linux 的发行版本。LibreOffice 采用对企业和个人用户均免费的 GPL 2.0 授权协议，可运行于 Microsoft Windows, GNU/Linux 以及 macOS 等操作系统上。LibreOffice Local 的缺点是不能远程访问，需要在应用所在的主机上部署，对于分布式应用而言并不友好，既增加了占用的资源，也增大了部署的难度。

<!--more-->

#### （1）部署
访问 LibreOffice 官网，根据操作系统下载不同版本的 LibreOffice。以 CentOS 系统为例，下载6.4.5版本的 RPM 包后，执行以下命令完成安装：
```bash
rpm -ivh LibreOffice_6.4.5_Linux_x86-64_rpm.tar.gz
```

#### （2）Jodconverter
JODConverter (for Java OpenDocument Converter) 使用 Java 语言封装了 LibreOffice 的文档处理能力，简化了 Java 应用实现文档格式转换的步骤。最新的 Jodconverter 项目地址位于：[https://github.com/sbraconnier/jodconverter/](https://github.com/sbraconnier/jodconverter/)，Jodconverter 根据 LibreOffice 部署的方式不同，提供若干种接入方式。对于已经安装 LibreOffice 软件的主机而言，Java 应用要接入文档格式转换能力只需要添加如下配置：
Maven：
```xml
<properties>
   <jodconverter.version>4.3.0</jodconverter.version>
</properties>
<dependencies>
  <dependency>
     <groupId>org.jodconverter</groupId>
     <artifactId>jodconverter-local</artifactId>
     <version>${jodconverter.version}</version>
  </dependency>
</dependencies>
```

Gradle:
```
ext {    jodconverterVersion = 4.3.0 }
compile "org.jodconverter:jodconverter-local:$jodconverterVersion" 
```

只需简单几行代码，Jodconverter 会自动识别 LibreOffice 的安装路径并发起调用:
```java
File inputFile = new File("document.doc");
File outputFile = new File("document.pdf");
// Create an office manager using the default configuration. 
// The default port is 2002. Note that when an office manager is installed, it will be the one used by default when a converter is created.
final LocalOfficeManager officeManager = LocalOfficeManager.install(); 
try {
   // Start an office process and connect to the started instance (on port 2002).     officeManager.start();
   // Convert
   JodConverter
            .convert(inputFile)
            .to(outputFile)
            .execute();
} finally {
   // Stop the office process
   OfficeUtils.stopQuietly(officeManager);
}
```

## 2、LibreOffice Online
LibreOffice Online 是单机版 LibreOffice 的衍生版本，基于 LibreOffice 的项目代码开发，作为服务提供一系列办公文档查看、编辑接口。
LibreOffice Online 主要由 Collabora 公司开发，该公司也提供了自己的分支版本 Collabora Online Development Edition （缩写为 CODE），实测 CODE 提供了一些新特性，但整体与 LibreOffice Online 的区别不大，稳定起见，建议采用 LibreOffice Online。LibreOffice Online 提供了原生和 Docker 两种部署方式，简易起见，建议采用 Docker 部署。

#### （1）部署
1）安装 docker，参考网上教程；  
2）拉取 LibreOffice Online 主干镜像：docker pull libreoffice/online:master  
3）启动容器，根据实际情况替换以下中文字段：  
```
docker run -t -d -p 宿主机端口:9980 -e "username=控制台账号" -e "password=控制台密码" -e "extra_params=--o:ssl.enable=false" --restart always libreoffice/online:master
```
注：控制台密码不允许有特殊字符，否则会被截断，如输入密码 “test@123”，实际密码会变为 test ）  
4）执行 docker ps 命令，如正常启动，可看到容器信息；进入该容器：  
```bash
docker exec -it 容器ID /bin/bash
```
5）(容器自带了nano) 修改 /etc/loolwsd/loolwsd.xml 配置，修改或新增 storage→wopi→host 节点：“.*”  
```xml
<hostdesc="Regexpatternofhostnametoallowordeny."allow="true">.*</host>
```  
6）修改配置文件完毕保存后，容器自动重启，终端回到宿主机。  
如上述步骤操作错误，可停止（docker stop）、删除（docker rm）容器，重新运行 docker run 命令；修改配置亦可通过docker cp命令将配置文件拷贝到宿主机、在宿主机修改完毕后再覆盖回容器。  
部署完毕后，可以访问LibreOffice Online主页（地址为：http://ip:port），显示OK；亦可访问控制台  
（地址为：http://ip:port/loleaflet/dist/admin/admin.html）  
部署完毕后，可通过curl工具验证转换效果：  
```bash
curl -F "data=@test.docx" http://ip:port/lool/convert-to/pdf > test.pdf
```
以上操作文件名、ip与port根据实际情况修改。  

#### （2）Jodconverter
对应 jodconverter-local，Jodconverter为 LibreOffice Online 提供的Java包是 jodconverter-remote，该库提供了 jodconverter-local 类似的接口，两者使用区别不大。
Maven：
```xml
<properties>
   <jodconverter.version>4.3.0</jodconverter.version>
</properties>
<dependencies>
  <dependency>
     <groupId>org.jodconverter</groupId>
     <artifactId>jodconverter-remote</artifactId>
     <version>${jodconverter.version}</version>
  </dependency>
</dependencies> 
```

Gradle:
```
ext {    jodconverterVersion = 4.3.0 }
compile "org.jodconverter:jodconverter-remote:$jodconverterVersion"
```

使用方法与 Local 版类似：
```java
File inputFile = new File("document.doc");
File outputFile = new File("document.pdf");
final OfficeManager officeManager = OnlineOfficeManager.make("http://libreoffice online's url");
OnlineConverter.builder().officeManager(officeManager).build()
            .convert(inputFile)
            .to(outputFile)
            .execute();
```

注：已知的问题，输入文件名包含中文字符时报错。