---
title: "java app 在向第三方服务传送中文时出现乱码"
description: "java app 在向第三方服务传送中文时出现乱码"
author: Guan Jun
resourceTags: 'app-service , java , garbled'
ms.service: app-service
wacn.topic: aog
ms.topic: article
ms.author: Guan Jun
ms.date: 11/9/2018
wacn.date: 11/9/2018
---
# java app 在向第三方服务传送中文时出现乱码

部署在 Azure 上的 Web APP 应用在向第三方传送中文字符串时，第三方服务接受到的是类似于 ？？ 之类的乱码，而本地运行发送是正常的。

这是因为 APP Service 环境的默认编码为 GBK ，在向第三方发送数据时要做默认的转码工作，即执行 *new String (“您要传送的字符串”.getBytes(),”UTF-8”)* ，而 getBytes 方法如果没有参数的话会使用系统默认的编码方式编码， gbk 编码和 utf-8 编码方式不同，所以在转换之后会出现乱码的现象。

若要解决这个问题，在 site/wwwroot 文件夹下创建 web.config 文件（如果已经有请直接修改），添加以下内容，主要目的时修改 jvm 的默认编码方式，修改成 utf-8 保证传输中编码不会出错：

```web.config
<configuration>
  <system.webServer>
    <handlers>
      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
    </handlers>
    <httpPlatform processPath="%AZURE_TOMCAT85_HOME%\bin\startup.bat" arguments="">
      <environmentVariables>
        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true -Dfile.encoding=UTF-8" />
      </environmentVariables>
    </httpPlatform>
  </system.webServer>
</configuration>
```