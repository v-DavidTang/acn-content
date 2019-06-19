---
title: "Azure 应用程序网关的压力测试及诊断日志的存储"
description: "Azure 应用程序网关的压力测试及诊断日志的存储"
author: Bingyu83
resourceTags: 'Application Gateway, Stress Test, Diagnostic Log'
ms.service: application-gateway
wacn.topic: aog
ms.topic: article
ms.author: biyu
wacn.org: CSS
wacn.authorDisplayName: 'Bing Yu'
ms.date: 06/13/2019
wacn.date: 06/13/2019
---

# Azure 应用程序网关的压力测试及诊断日志的存储

## 压力测试目的

通常情况下，压力测试目的有以下三种：

1. 针对日常维护提供性能指标参考。

2. 针对特别市场活动或大型事件做性能模拟测试，预测届时所需的应用程序网关 SKU 以及实例数的最佳配比。

3. 业务上线以前针对重要业务请求 API 做安全规则过滤评估，避免由于安全不合规导致大量业务请求被安全规则阻断以及大量安全规则过滤引起性能瓶颈。

## 压力测试必要条件

根据以上目的，做有针对性的压力测试是必要的，以下列出具体必要条件：

1. 前端压力测试工具，例如 JMeter，有关如何使用 JMeter test plan 来做定制压力测试模型，可以参考：[Apache JMeter](http://jmeter.apache.org/usermanual/build-web-test-plan.html)。

2. 前端压力测试工具具有参数监控功能，监控项至少包括：HTTP response code, request per second (rps), time taken for each request, throughput, failed request counts, num of connection。

3. 启用诊断日志，并针对需求配置合理的[日志存储方式](https://docs.azure.cn/zh-cn/articles/azure-operations-guide/application-gateway/aog-application-gateway-diagnostic-log-details-and-compliance-requirements)，从而可以统计应用程序网关各方面的性能指标。