---
title: "为云服务创建内部负载均衡器"
description: "为云服务创建内部负载均衡器"
author: chenzheng1988
resourceTags: 'Cloud Service, Internal Load Balancer'
ms.service: cloud-service
wacn.topic: aog
ms.topic: article
ms.author: v-ciwu
wacn.authorDisplayName: 'Chen Zheng'
ms.date: 05/13/2019
wacn.date: 05/13/2019
---

# 为云服务创建内部负载均衡器

云服务支持内部负载均衡器，配置了内部负载均衡器后只能在该虚拟网络内访问云服务，以确保云服务的安全性。

## 解决方案

1. 创建经典虚拟网络。云服务只支持经典虚拟网络，在门户中创建经典虚拟网络请参考：[使用 Azure 门户创建虚拟网络（经典）](https://docs.azure.cn/zh-cn/virtual-network/virtual-networks-create-vnet-classic-pportal)。

2. 在云服务的配置文件 *.cscfg* 中添加虚拟网络和内部负载均衡。云服务（经典）需要在配置文件 *.cscfg* 中添加 *NetworkConfiguration*，并且需要重新做完全部署才能生效。需要注意检查添加相关配置之后的 *xml* 文件是否为有效文件，可以通过在线工具 [XML Beautifier](http://xmlbeautifier.com/) 生成有效 *xml* 文件。参考示例如下：

    ```xml
    <NetworkConfiguration>
        <VirtualNetworkSite name="经典虚拟网络的虚拟网络站点名称" />
        <AddressAssignments>
          <InstanceAddress roleName="角色名称">
            <Subnets>
              <Subnet name="子网名称" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <LoadBalancers>
        <LoadBalancer name="负载均衡名称">
            <FrontendIPConfiguration type="private" subnet="子网名称" staticVirtualNetworkIPAddress="子网静态 IP 地址"/>
        </LoadBalancer>
        </LoadBalancers>
    </NetworkConfiguration>
    ```

3. 更改服务定义 *csdef* 文件，以便向内部负载均衡添加终结点。创建角色实例的那一刻，服务定义文件会将角色实例添加到内部负载均衡：

    ```xml
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="http" port="80" localPort="80" loadBalancer="负载均衡名称"/>
    </Endpoints>
    ```

配置了内部负载均衡器后是无法使用 Swap 功能的，由于 Swap 需要 VIP，如果部署了内部负载均衡器，则无法 Swap VIP。

## 参考文档

* [开始为云服务创建内部负载均衡器（经典）](https://docs.microsoft.com/zh-cn/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)