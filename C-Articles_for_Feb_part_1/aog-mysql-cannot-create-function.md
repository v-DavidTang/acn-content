---
title: "如何解决 MySQL Database on Azure 中无法创建函数的问题"
description: "如何解决 MySQL Database on Azure 中无法创建函数的问题"
author: Dillion132
resourceTags: 'MySQL , MIME'
ms.service: MySQL
wacn.topic: aog
ms.topic: article
ms.author: v-zhilv
ms.date: 2/7/2018
wacn.date: 2/7/2018
---

# 如何解决 MySQL Database on Azure 中无法创建函数的问题

## 问题描述

在使用 My SQL Database on Azure 中创建函数时报以下错误：

```
You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)
```

## 问题分析

造成这个问题的原因，与 MySQL DataBase on Azure 服务器参数 log_bin_trust_function_creators 的设置有关。

log_bin_trust_function_creators 参数主要用于控制用户是否有权限创建或更改函数，默认情况下，MySQL Database 服务器参数为 “OFF”。将该参数改为 “ON”，可以解决该问题。


## 解决方法

### 通过 Azure Portal 修改服务器参数

登陆 [Azure 门户](https://portal.azure.cn)，打开 MySQL 服务，在 MySQL 侧边栏中选中 “服务器参数”，修改 log_bin_trust_function_creators 参数值为 “ON”，点击保存按钮保存设置。

![](.\media\aog-mysql-cannot-create-function\mysql1.PNG)


