---
title: 使用 Azure 门户创建 Express Route
description: 使用 Azure 门户创建 Express Route
service: ''
resource: Express Route
author: LiheZhang
displayOrder: ''
selfHelpType: ''
supportTopicIds: ''
productPesIds: ''
resourceTags: 'Express Route, Virtual Network, Virtual Network Gateway, Azure Portal'
cloudEnvironments: MoonCake

ms.service: express-route
wacn.topic: aog
ms.topic: article
ms.author: v-tawe
ms.date: 09/20/2017
wacn.date: 09/20/2017
---
# 使用 Azure 门户创建 Express Route

## 通过 Azure 门户创建 Express Route

通过 Azure 门户创建 Express Route 流程如下：

## 准备工作

向中国电信申请开通 Express Route 线路。

目前申请开通 Express Route 线路的中国电信参考联系方式如下：

北京电信： 010-59503241<br>
上海电信： 021-52341697(优先联系) 或 021-32070359

1. 电信成功开通 Express Route 线路后，在 Azure 门户创建 Express Route 线路，并将 Azure 侧生成的 Service Key 和后续需要使用的 VLAN ID 给到中国电信，请中国电信将线路状态置为 “**已设置**” 即 “**Provisioned**” 状态；
2. Azure 门户配置对等互联；
3. Azure 门户创建虚拟网络，并为该虚拟网络创建 Express Route 网关；
4. 将 VNET Gateway 链接到创建好的 Express Route 线路。

在 [Azure 门户](https://portal.azure.cn)配置 Express Route 的步骤如下：

1. 在 Azure 侧创建线路并请中国电信将创建好的线路至为 Provisioned 状态。

    向中国电信申请开通 Express Route 后，登录 Azure 门户预览，导航至 Express Route 配置页面，点击 “**新建**”，如图 1-1：

    ![1-1](media/aog-expressroute-howto-create-through-azure-portal/1-1.png)
    <center>图 1-1 创建 Express Route 线路</center>

    创建好线路后，请将线路的 Service Key 给到中国电信，电信将线路的状态置为 Provisioned 后，用户才可以继续配置。

2. 配置线路 :

    在电信将线路状态置为 Provisioned 状态后，配置 peer 及 VLAN ID。如果线路状态未置为 provisioned 的状态，配置界面将为灰色不可配置，如图 2-1：

    ![1-2-1](media/aog-expressroute-howto-create-through-azure-portal/1-2-1.png)
    <center>图 2-1 创建好的 Express Route 线路</center>

    如果如上图中的线路状态 “**提供程序状态**” 为 “**未设置**” 时，点击 Azure 公共对等或私有对等时，配置界面会显示无法配置，如图 2-2：

    ![1-2-2](media/aog-expressroute-howto-create-through-azure-portal/1-2-2.png)
    <center>图 2-2 线路状态为 “未设置” 时，无法配置公共对等互联</center>

    只有当图 2-1 中，“**提供程序状态**” 为 “**已设置**” 即 “**Provisioned**” 状态时，才可以继续配置 Azure 公用和私有对等互联。配置参数说明如下：

    **参数说明**

    **Azure Private**（专用对等互连）:

    可以通过专用对等域来连接虚拟网络内部署的 Azure 计算服务（即虚拟机 (IaaS) 和云服务 (PaaS)）。专用对等域被视为进入 Microsoft Azure 的核心网络的受信任扩展。可以在核心网络和 Azure 虚拟网络 (VNet) 之间设置双向连接。这样，您便可以使用专用 IP 地址直接连接到虚拟机和云服务。可以将多个虚拟网络连接到专用对等域。

    **Azure Public**（公共对等互连）:

    Azure 存储空间、SQL 数据库和 Web 应用等服务是使用公共 IP 地址提供的。您可以通过公共对等路由域私下连接到公共 IP 地址（包括云服务的 VIP）上托管的服务。可以将公共对等域连接到外围网络，并从 WAN 连接到公共 IP 地址上的所有 Azure 服务，而无需通过 Internet 连接。启用公共对等互连后，您将能够连接到所有 Azure 服务。

    **Peer ASN**: 

    Microsoft Azure 使用 AS 12076 进行 Azure 公共和 Azure 专用。我们保留了 AS 65515-65520 供内部使用。支持 16 和 32 位 AS 编号。您可以使用专用 AS 编号建立 Azure 专用对等互连。建议您使用 65001~65534 范围的 AS 编号进行配置。注意，65515-65520 为 Azure 保留的 ASN 编号，请务必不要使用 65515-65520。

    **Primary subnet & Secondary subnet**:

    Azure 专用对等互连的 IP 地址：
    可以使用专用 IP 地址或公共 IP 地址来配置对等互连。用于配置路由的地址范围不得与用于在 Azure 中创建虚拟网络或您的本地网络的地址范围重叠。

    - 必须为路由接口保留一个 /29 子网或两个 /30 子网。
    - 用于路由的子网可以是专用 IP 地址或公共 IP 地址。
    - 子网不得与客户保留用于 Microsoft 云的范围冲突。
    - 如果使用 /29 子网，它将拆分成两个 /30 子网。 
    - 第一个 /30 子网用于主链路，第二个 /30 子网用于辅助链路。
    - 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。Microsoft 使用 /30 子网的第二个 IP 地址来设置 BGP 会话。
    - 只有设置了两个 BGP 会话，我们的[可用性 SLA](http://azure.microsoft.com/support/legal/sla/) 才有效。 

    **对等互连示例**

    如果您选择使用 a.b.c.d/29 来设置对等互连，它将拆分成两个 /30 子网。在以下示例中，我们可以了解 a.b.c.d/29 子网的用法。

    a.b.c.d/29 拆分成 a.b.c.d/30 和 a.b.c.d+4/30 并通过预配 API 一路传递到 Windows Azure。您将使用 a.b.c.d+1 作为主要 PE 的 VRF IP，而 Windows Azure 将使用 a.b.c.d+2 作为主要 MSEE 的 VRF IP。您将使用 a.b.c.d+5 作为辅助 PE 的 VRF IP，而 Windows Azure 将使用 a.b.c.d+6 作为辅助 MSEE 的 VRF IP。

    假设您选择 192.168.100.128/29 来设置专用对等互连。192.168.100.128/29 包括从 192.168.100.128 到 192.168.100.135 的地址，其中：

    - 192.168.100.128/30 将分配给 link1（提供商使用 192.168.100.129，而 Windows Azure 使用 192.168.100.130）。
    - 192.168.100.132/30 将分配给 link2（提供商使用 192.168.100.133，而 Windows Azure 使用 192.168.100.134）。

    **VLAN ID**:

    VLAN ID 是客户自定义的 internal VLAN（C-tag）。不同的客户可以使用相同的 VLAN ID（C-tag），对于 IXP 来说，会有一个唯一的 S-tag 进行标记，不需要客户配置网络底层的 S-tag。您可以使用 1 至 4094 之间的数字配置 VLAN ID：
    
    **Shared key**:

    共享秘钥，也可以不为 ER 线路配置共享秘钥。

3. 创建虚拟网络并为该虚拟网络创建 Express Route 类型的网关。

    1. 创建 VNET

        Azure 门户预览,导航到 “**虚拟网络**” 页面，点击 “**添加**”，如图 3-1：

        ![1-3-1](media/aog-expressroute-howto-create-through-azure-portal/1-3-1.png)
        <center>图 3-1 创建虚拟网络</center>

        > [!Note]
        > 创建 VNET 前，请提前规划好网络地址空间及子网空间配置，虚拟网络的网关子网的地址范围最好配置为 /27 或更短的前缀（例如 /26 或 /25），因为 /28 的网关子网不支持 Express Route 混合 VPN 功能。


    2. 为虚拟网络创建 Express Route 类型的 VNET 网关

        > [!Note]
        > 1. 创建 VNET 网关时，请选择 Express Route 类型。
        > 2. 创建 VNET 网关时，虚拟网络的网关子网的地址范围最好配置为 /27 或更短的前缀（例如 /26 或 /25），因为 /28 的网关子网不支持 Express Route 混合 VPN 功能。

        导航至 “**虚拟网络网关**” 页面，点击 “**添加**”，为刚刚创建的 VNET（RMERVNETtest）创建网关，如图 3-2：

        ![1-3-2](media/aog-expressroute-howto-create-through-azure-portal/1-3-2.png)
        <center>图 3-2 为 VNET 创建网关</center>

4. 将 VNET 链接到 Express Route 线路

    Azure 门户预览导航至连接页面，点击 "**新建**"，如图 1.4-1 ，在图 1.4-2 中选择对应的虚拟网络网关和 Express Route 线路：

    ![1-4-1](media/aog-expressroute-howto-create-through-azure-portal/1-4-1.png)
    <center>图 4-1 创建 VNET 到 Express Route 线路的链接</center>

    ![1-4-2](media/aog-expressroute-howto-create-through-azure-portal/1-4-2.png)
    <center>图 4-2 选择虚拟网络网关及 ExpressRoute 线路</center>