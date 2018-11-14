---
title: "如何通过 kudu 查看/修改 java tomcat 服务端的配置文件"
description: "通过 kudu 查看/修改 java tomcat 服务端的配置文件"
author: Bu Lu
resourceTags: 'App Service Web, Kudu, tomcat'
ms.service: app-service
wacn.topic: aog
ms.topic: article
ms.author: Bu Lu
ms.date: 11/6/2018
wacn.date: 11/6/2018
---
# 如何通过 kudu 查看/修改 java tomcat 服务端的配置文件

由于 Azure App Service 可以在门户中选择 tomcat 的版本，当我们需要查看服务端的配置文件时，可以通过以下的 5 个步骤来实现：

1. 登陆 kudu ：

    ![login-kudu](media/aog-app-service-howto-modify-with-kudu/login-kudu.png "login-kudu")

    有如下两种方式登陆 kudu ：
    1. 在当前web 应用的 URL 中加入 *.scm* [插入位置在 site 名与 chinacloudsites 之间]。
    2. 在当前web 应用的 Azure 门户中操作，点击 **“ 开发工具 -> 高级工具 -> 转到 ”** 即可进入 kudu 页面。

2. 查看当前 java 进程信息，获取到当前 tomcat 路径：

    ![view-java-process](media/aog-app-service-howto-modify-with-kudu/view-java-process.png "view-java-process")

    点击 **"Process Expolorer"** 在java的进程上点击 **"Properties"** 查看详细信息：

   ![click-properties](media/aog-app-service-howto-modify-with-kudu/click-properties.png "click-properties")

    这一步中，获取到当前tomcat的路径为 D:\Program Files (x86)\apache-tomcat-8.5.20 文件夹。

3. 通过 Debug console 导航并进入到服务端的配置文件夹：

    ![debug-console](media/aog-app-service-howto-modify-with-kudu/debug-console.png "debug-console")

    逐步点击，找到 conf 文件夹：

    ![find-conf](media/aog-app-service-howto-modify-with-kudu/find-conf.png "find-conf")

4. 点击文件名边的编辑按钮，查看及修改文件内容：

    ![start-to-modify](media/aog-app-service-howto-modify-with-kudu/start-to-modify.png "start-to-modify")

5. 保存并重启 site 。
