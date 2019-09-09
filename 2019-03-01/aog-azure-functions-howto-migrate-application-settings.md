---
title: "迁移 Azure 函数应用设置的几种方法"
description: "迁移 Azure 函数应用设置的几种方法"
author: Ciriwu
resourceTags: 'Azure Funciton, Application Setting'
ms.service: azure-function
wacn.topic: aog
ms.topic: article
ms.author: v-ciwu
wacn.authorDisplayName: 'Ciri Wu'
ms.date: 03/25/2019
wacn.date: 03/25/2019
---

# 迁移 Azure 函数应用设置的几种方法

## 问题描述

当用户新建函数的应用程序设置需求与现有应用的应用程序设置相同，可以使用以下几种工具快速将应用设置迁移至新应用。

## 解决方案

### 通过 Azure PowerShell 迁移应用设置

1. 安装 [Azure PowerShell 模块](https://docs.azure.cn/zh-cn/powershell-install-configure)。

2. 使用 Azure PowerShell 登录订阅：

    ```powershell
    Login-AzureRmAccount –Environment AzureChinaCloud
    ```

3. 将以下脚本的变量改为用户相应的参数，并且运行：

    ```powershell
    # 从原应用获取应用程序设置
    $resource = Invoke-AzureRmResourceAction -ResourceGroupName <资源组名称> -ResourceType Microsoft.Web/sites/config -ResourceName "<函数应用名>/appsettings" -Action list -ApiVersion 2016-08-01 -Force

    # 使用 $resource.Properties 来更新目标应用
    New-AzureRmResource -PropertyObject $resource.Properties -ResourceGroupName <目标函数资源组名称> -ResourceType Microsoft.Web/sites/config -ResourceName "<目标函数应用名>/appsettings" -ApiVersion 2016-08-01 –Force
    ```

### 通过 Azure CLI 迁移应用设置

1. 安装 [Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

2. 安装用于处理 JSON 的 [JQ 命令行](https://stedolan.github.io/jq/download/)。

3. 运行 Azure CLI 将云环境改为中国并登录订阅：

    ```cli
    az cloud set -n AzureChinaCloud
    az login
    ```

4. 将以下脚本的变量改为用户相应的参数，并且运行：

    ```cli
    $srcResourceGroup="<资源组名称>"
    $srcName="<函数应用名>"
    $dstResourceGroup="<目标函数资源组名称>"
    $dstName="<目标函数应用名>"
    $settingsToBeRemoved=az functionapp config appsettings list --resource-group $dstResourceGroup --name $dstName | jq '.[] | .name' -r
    az functionapp config appsettings delete --resource-group $dstResourceGroup --name $dstName --setting-names $settingsToBeRemoved 
    $settingsToBeCopied=az functionapp config appsettings list --resource-group $srcResourceGroup --name $srcName | jq '.[] | .name+\"=\"+.value' -r
    az functionapp config appsettings set --resource-group $dstResourceGroup --name $dstName --settings $settingsToBeCopied
    ```