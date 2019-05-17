---
title: "用户连接服务注意事项"
description: "用户连接服务注意事项"
author: chenzheng1988
resourceTags: 'Customer Engagement Fabric, Precautions'
ms.service: customer-engagement-fabric
wacn.topic: aog
ms.topic: article
ms.author: v-ciwu
wacn.authorDisplayName: 'Chen Zheng'
ms.date: 05/13/2019
wacn.date: 05/13/2019
---

# 用户连接服务注意事项

1. 发送短信会选择模板，模板可能会希望用户回复短信，**td**，**TD**，**Td**，**tD**，**n**，**N**，**T**，**t**，**BK**，**屏蔽码 00**，**00**，**退订**，**PBXX**，**pbxx**，**pBXX**，**pBxx** 这些都是退订参数，建议模板中尽量避免设计这些回复的指令。

2. 通知类和验证码信息一个手机号码一天只能发送 10 条，营销类短信一个手机号码一天只能发送一条，如果想要提高限制请与我们联系，商务团队会协助您处理此问题。

3. 营销类型的签名是无法创建通知类型和验证码类型的模板的。

4. 短信无法接收，出现 UNKNOWN 一般是以下几种原因：

    1. 手机号码为空号

    2. 手机信号有问题

    3. 手机号被加入运行商黑名单

    4. 手机号是关机状态