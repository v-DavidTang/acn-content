# 关于 wget 下载 Blob 文件如何断点续传 #
**问题：**Linux 环境下使用 wget 命令下载 Blob 文件断点续传问题

**现象：**使用 wget 下载 blob 文件时，如果遇到中断后重新执行下载命令，会重新下载。下载非 blob 文件，会从断点处继续下载：

**问题原因：** `wget –c` 通过 http range 实现断点续传功能，Storage  是基于 REST HTTP 构建的，但是随着架构不断更替，REST 版本也在不断升级。我们通过打印 `wget –c` 的服务器回应，发现默认发起的 http 请求是基于早期的 REST 版本的 2009-09-19，而这个版本及更早的则不支持 range 请求格式 “[offset]-”，导致无法根据 range 来实现断点续传。更多：

[https://msdn.microsoft.com/zh-cn/library/dd894041.aspx](https://msdn.microsoft.com/zh-cn/library/dd894041.aspx "https://msdn.microsoft.com/zh-cn/library/dd894041.aspx") 

![REST 版本](media/aog-wget-blob-download-resume/wget-rest-version.png "REST 版本")

**解决方法：**

通过加参数 `--header "x-ms-version: 2015-04-05"`，指定 REST HTTP 请求版本：

![REST HTTP 请求](media/aog-wget-blob-download-resume/wget-rest-http-request.png "REST HTTP 请求")

