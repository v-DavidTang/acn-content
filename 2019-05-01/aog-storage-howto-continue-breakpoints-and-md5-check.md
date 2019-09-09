---
title: "Azure 存储的断点续传与 MD5 校验"
description: "Azure 存储的断点续传与 MD5 校验"
author: 123Jun321
resourceTags: 'Storage, Breakpoint Continuation, MD5'
ms.service: storage
wacn.topic: aog
ms.topic: article
ms.author: v-ciwu
wacn.org: 21V
wacn.authorDisplayName: 'Guan Jun'
ms.date: 05/14/2019
wacn.date: 05/14/2019
---

# Azure 存储的断点续传与 MD5 校验

## 问题分析

首先关于 Azure 存储中 MD5 的描述，我们已经有相关的介绍文档，可以参考 [Azure Blob 存储基于 MD5 的完整性检查](https://docs.azure.cn/zh-cn/articles/azure-operations-guide/storage/aog-storage-blob-integrity-checking-with-md5)的内容。

如果使用 .NET SDK 上传文件到 Blob 中可以在上传的方法中配置 BlobRequestOptions 类，将该类的 StoreBlobContentMD5 参数设置为 true，即可在上传时自动计算 MD5 值，并且在上传时将此值写入到请求头部（Content-MD5）中（可以参考 [BlobRequestOptions.StoreBlobContentMD5 Property](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5) 此文档描述）。

但是如果使用断点续传的方法，是将多个 PutBlock 请求加上一个 PubBlockList 请求组成，那么想要上传 MD5 值时需要将 MD5 写到另一个 x-ms-blob-content-md5 请求头部的参数中，BlobRequestOptions 中并没有关于赋值给该参数的属性，所以如果使用断点续传，采用 SDK 的 PubBlockList() 方法无法将 MD5 值上传上去，本篇文档即要解决如何在断点续传时上传 MD5 值。

## 解决方案

可以通过直接使用 Rest API 来解决此问题，关于如何使用可以参考此 Demo：[
Azure-Storage-resume-from-breakpoint-example](https://github.com/123Jun321/Azure-Storage-resume-from-breakpoint-example)。