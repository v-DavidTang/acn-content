---
title: 上传大文件到 Azure 存储块 Blob 
description: 上传大文件到 Azure 存储块 Blob 
service: ''
resource: Storage
author: Dillion132
displayOrder: ''
selfHelpType: ''
supportTopicIds: ''
productPesIds: ''
resourceTags: 'Storage, MIME'
cloudEnvironments: MoonCake

ms.service: Storage
wacn.topic: aog
ms.topic: article
ms.author: v-zhilv
ms.date: 11/24/2017
wacn.date: 11/24/2017
---

# 上传大文件到 Azure 存储块 Blob 

## 相关概念

Azure 存储提供三种类型的 Blob：块 Blob、页 Blob 和追加 Blob。其中，块 Blob 特别适用于存储短的文本或二进制文件，例如文档和媒体文件。

块 Blob 由块组成。每个块可以是不同的大小，最大为 100MB (对于2016-05-31 之前 REST 版本的请求为4 MB)，块 Blob 最多可以包含 50,000 块。因此，块 Blob 的最大大小约为 4.75 TB (100MB X 50,000块)。对于2016-05-31之前的 REST 版本，块blob的最大大小约为 195 GB（4 MB X 50,000块），更多详细信息，请参阅[这篇文章](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。

在上传文件到 Azure Blob 存储时，Azure 支持两种方式，**整体上传**和**分块上传**。

当上传到块 Blob 的文件小于等于 [SingleBlobUploadThresholdInBytes](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.singleblobuploadthresholdinbytes?view=azure-dotnet) 属性（客户端可以通过设置该属性设置单个 Blob 上传的最大值，范围介于 1MB 和 256MB 之间）的值时，则可以采用整体上传的方式，调用 PutBlob 完整的上传 Blob ，更多详细信息，请参考[PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob)。

当上传的块 Blob 的文件大于 SingleBlobUploadThresholdInBytes 属性的值时，存储客户端会根据 [StreamWriteSizeInBytes](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.streamwritesizeinbytes?view=azure-dotnet)(客户端可以通过设置该属性设置单个分块 Blob 的大小，范围介于 16 KB和100 MB 之间) 的值将文件分解成块, 采用分块上传的方式上传文件，更多详细信息，请参考[PubBlobList](https://docs.microsoft.com/rest/api/storageservices/put-block-list)。

例如： 设置 SingleBlobUploadThresholdInBytes 为：10MB , StreamWriteSizeInBytes 为：5MB。当上传文件小于等于 10MB 时，可以用一个 PutBlob 将文件整体上传；当文件大于 10 MB 时，客户端会将文件按照 StreamWriteSizeInBytes 属性设置的值，将文件切块， 然后使用 PutBlobList 上传。

本文主要使用以下两种方式上传大文件到 Blob 存储。

* [使用 .NET Storage SDK 上传文件](#netsdk)

* [使用 Microsoft Azure Storage Data Movement Library 上传文件](#datamovement)

## <a id="netsdk"></a>使用 .NET Storage SDK 上传文件

### 前提条件

需要在项目中引用两个包：[适用于 .NET 的 Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)和[适用于 .NET 的 Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)，也可以通过 NuGet 搜索 "WindowsAzure.Storage" 和 "WindowsAzure.ConfigurationManager" 安装。

本文使用的是 WindowsAzure.Storage 8.6.0 版本和 WindowsAzure.ConfigurationManager 3.2.3 版本。

代码如下：

```
    TimeSpan backOffPeriod = TimeSpan.FromSeconds(2);
    int retryCount = 1;
    //设置请求选项
    BlobRequestOptions requestoptions = new BlobRequestOptions()
    {
        SingleBlobUploadThresholdInBytes = 1024 * 1024 * 10, //10MB
        ParallelOperationThreadCount = 12,
        RetryPolicy = new ExponentialRetry(backOffPeriod, retryCount),
    };

    CloudStorageAccount account = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobclient = account.CreateCloudBlobClient();
    //设置客户端默认请求选项
    blobclient.DefaultRequestOptions = requestoptions;
    CloudBlobContainer blobcontainer = blobclient.GetContainerReference("uploadfiles");
    blobcontainer.CreateIfNotExists();
    //文件路径，文件大小117MB
    string sourcePath = @"D:\bigfiles.zip";
    CloudBlockBlob blockblob = blobcontainer.GetBlockBlobReference("bigfiles");
    //设置单个块 Blob 的大小（分块方式）
    blockblob.StreamWriteSizeInBytes = 1024 * 1024 * 5;
    try
    {
        Console.WriteLine("uploading");
        //使用 Stopwatch 查看上传时间
        var timer = System.Diagnostics.Stopwatch.StartNew();
        using (var filestream = System.IO.File.OpenRead(sourcePath))
        {
            blockblob.UploadFromStream(filestream);
        }
        timer.Stop();

        Console.WriteLine(timer.ElapsedMilliseconds);

        Console.WriteLine("Upload Successful, Time:" + timer.ElapsedMilliseconds);
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
```

截图如下：

![putbloblist.PNG](./media/aog-storage-how-to-upload-big-file-to-storage/putbloblist.PNG)

## <a id="datamovement"></a>使用 Microsoft Azure Storage Data Movement 类库上传文件

[Microsoft Azure Storage Data Movement](https://github.com/Azure/azure-storage-net-data-movement) 主要用于高性能上传，下载和复制Azure存储Blob和文件。 这个库是基于 [AzCopy](https://docs.azure.cn/storage/common/storage-use-azcopy?toc=%2fstorage%2fblobs%2ftoc.json) 为核心的数据移动框架。

### 前提条件

.NET Framework 4.5 或者以上版本，Netstandard2.0。

可以通过 Nuget 搜索 "Microsoft.Azure.Storage.DataMovement" 安装该类库，本文使用的是 Microsoft.Azure.Storage.DataMovement 0.6.5版本。

代码如下：
```
    CloudStorageAccount account = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobclient = account.CreateCloudBlobClient();
    CloudBlobContainer blobcontainer = blobclient.GetContainerReference("uploaddocuments");
    blobcontainer.CreateIfNotExists();

    // 获取文件路径, 本示例文件大小 771MB
    string sourcePath = @"D:\Documents.zip";
    CloudBlockBlob docBlob = blobcontainer.GetBlockBlobReference("documents");
    // 设置并发操作的数量。
    TransferManager.Configurations.ParallelOperations = 64;
    // 设置单块 blob 的大小，它必须在4MB到100MB之间，并且是4MB的倍数，默认情况下是 4MB
    TransferManager.Configurations.BlockSize = 64 * 1024 * 1024;
    // 设置传输上下文并跟踪上传进度
    var context = new SingleTransferContext();
    UploadOptions uploadOptions = new UploadOptions
    {
        DestinationAccessCondition = AccessCondition.GenerateIfExistsCondition()
    };
    context.ProgressHandler = new Progress<TransferStatus>(progress =>
    {
        //显示上传进度
        Console.WriteLine("Bytes uploaded: {0}", progress.BytesTransferred);
    });
    // 使用 Stopwatch 查看上传所需时间
    var timer = System.Diagnostics.Stopwatch.StartNew();
    // 上传 Blob
    TransferManager.UploadAsync(sourcePath, docBlob, uploadOptions, context, CancellationToken.None).Wait();
    timer.Stop();
    Console.WriteLine(timer.ElapsedMilliseconds);    
```

截图如下：

![datamovement.PNG](./media/aog-storage-how-to-upload-big-file-to-storage/datamovement.PNG)