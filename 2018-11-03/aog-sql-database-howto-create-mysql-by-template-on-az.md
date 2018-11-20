---
title: "使用 az 命令通过模板创建 MySQL PaaS"
description: "使用 az 命令通过模板创建 MySQL PaaS"
author: Xing Bing
resourceTags: 'Mysql Database, az, template'
ms.service: mysql-database
wacn.topic: aog
ms.topic: article
ms.author: Xing Bing
ms.date: 11/20/2018
wacn.date: 11/20/2018
---

# 使用 az 命令通过模板创建 MySQL PaaS

选择新建 MySQL PaaS，填写参数之后，不选择创建，选择自动化模板，然后点下载，将 template.json 和 parameters.json 文件下载。

![01](media/aog-sql-database-howto-create-mysql-by-template-on-az/01.png "01")

![02](media/aog-sql-database-howto-create-mysql-by-template-on-az/02.png "02")

首先使用 az 命令登陆 Azure 门户账号：

![03](media/aog-sql-database-howto-create-mysql-by-template-on-az/03.jpg "03")

接下来使用命令az group deployment来部署：

![04](media/aog-sql-database-howto-create-mysql-by-template-on-az/04.jpg "04")

我的 Servername 叫做 zhtest，其中 parameters.json 文件里，我添加了密码参数：

![05](media/aog-sql-database-howto-create-mysql-by-template-on-az/05.jpg "05")

最后部署成功：

![06](media/aog-sql-database-howto-create-mysql-by-template-on-az/06.jpg "06")