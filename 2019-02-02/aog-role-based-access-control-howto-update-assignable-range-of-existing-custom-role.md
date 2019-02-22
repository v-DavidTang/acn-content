---
title: "RBAC 如何更新已有自定义角色的可分配范围"
description: "RBAC 如何更新已有自定义角色的可分配范围"
author: jessie-pang
resourceTags: 'Role Based Access Control, Assignable Range'
ms.service: role-based-access-control
wacn.topic: aog
ms.topic: article
ms.author: heyang.pang
wacn.authorDisplayName: 'Jessie Pang'
ms.date: 02/22/2019
wacn.date: 02/22/2019
---

# RBAC 如何更新已有自定义角色的可分配范围

基于角色的访问控制（RBAC）是在 Azure 资源管理器基础上构建的授权系统，针对 Azure 中的资源提供精细的访问权限管理。RBAC 提供了[内置角色](https://docs.azure.cn/zh-cn/role-based-access-control/built-in-roles)和可以满足特定需求的[自定义角色](https://docs.azure.cn/zh-cn/role-based-access-control/custom-roles)。在[创建自定义角色](https://docs.azure.cn/zh-cn/role-based-access-control/tutorial-custom-role-powershell)时，需要为该角色指定一个可分配范围，包括特定的订阅或资源组。当您添加了新的订阅或资源组后，可能希望将该自定义角色的可分配范围扩展至新的订阅或资源组内。如果您尝试在新的订阅下创建配置完全相同的自定义角色，会收到如下报错：

"New-AzureRmRoleDefinition : A role definition cannot be updated with a name that already exists."

这是因为自定义角色信息存储在 Azure Active Directory (Azure AD) 目录中，即使是同一个 Azure AD 目录中的不同订阅也不允许出现重名的自定义角色。

当然，您可以使用一个新的自定义角色名称来避免这个报错。为了提供更好的管理体验，是否可以更新已有的自定义角色的可分配范围呢？答案是肯定的。请准备好 PowerShell 的环境跟我们一起来试试吧。

1. 登录到 Azure 并切换至相应的订阅下（请替换为您的订阅 ID）：

    ```powershell
    Login-AzureRmAccount -EnvironmentName AzureChinaCloud
    $subscriptionid = "00000000-0000-0000-0000-000000000000"
    Select-AzureRmSubscription -SubscriptionId $subscriptionid
    ```

2. 查看相应自定义角色的配置，输出中的 AssignableScopes 便是当前的可分配范围：

    ```powershell
    $role = Get-AzureRmRoleDefinition -Name "ROLE_DEFINITION_NAME"
    $role
    ```

3. 在 AssignableScopes 部分添加更多的订阅ID或资源组，可多次重复此步骤以加入所有需要的订阅 ID 或资源组：

    ```powershell
    $role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
    ```

4. 更新自定义角色配置：

    ```powershell
    Set-AzureRmRoleDefinition -Role $role
    ```

等待至新配置生效后，便可以在新的范围内使用该自定义角色了。
