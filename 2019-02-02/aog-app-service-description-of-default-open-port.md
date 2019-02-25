---
title: "App Service 默认开放端口说明"
description: "App Service 默认开放端口说明"
author: maysmiling
resourceTags: 'App Service, Default Open Port'
ms.service: app-service
wacn.topic: aog
ms.topic: article
ms.author: v-amazha
wacn.authorDisplayName: 'Zhang Hongmei'
ms.date: 02/22/2019
wacn.date: 02/22/2019
---

# App Service 默认开放端口说明

## 问题描述

基于安全的角度来考虑，在网站上线之前用户会对自己的网站进行安全扫描，以防网站因为某些漏洞而被非法攻击。
而在扫描过程中，会发现除了 80 和 443 之外的一些其他端口也被开放了。例如：454, 455, 4020, 4022, 4024 等端口也处于开放状态。

## 解决方法

针对以上现象，现做如下端口的说明：

* 454：必需端口，Azure 架构使用此端口来管理和维护服务。

* 455：必需端口，Azure 架构使用此端口来管理和维护服务。

* 80：HTTP 访问时的默认入站端口。

* 443：SSL 访问时的默认入站端口。

* 21：FTP 的控制通道端口。

* 10001~10020：FTP 的数据传输端口。

* 4020：为 Visual Studio 2015 远程调试的预留端口。

* 4022：为 Visual Studio 2017 远程调试的预留端口。

* 4024：为 Visual Studio 2019 远程调试的预留端口。

> [!NOTE]
>
> * 以上并不是 Web 应用开发的全部端口，并且在以后使用的过程中端口可能会增加或者减少。例如：用于 Visual Studio 2012 和 Visual studio 2013 的远程调试端口4016和4018已经在系统层面被关闭。
>
> * 基于目前 App Service 架构的特殊性，用户并不能手动关闭这些端口，也无法针对单个用户进行端口的调整。

## 参考文档

* [Remote Debugger Port Assignments](https://github.com/MicrosoftDocs/visualstudio-docs/blob/master/docs/debugger/remote-debugger-port-assignments.md)

* [如何控制应用服务环境的入站流量](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-control-inbound-traffic)
