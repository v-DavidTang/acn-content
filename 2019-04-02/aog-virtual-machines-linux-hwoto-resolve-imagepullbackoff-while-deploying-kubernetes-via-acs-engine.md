---
title: "使用 AKS Engine 部署 Kubernetes 组件时如何解决 ImagePullBackOff 的问题"
description: "使用 AKS Engine 部署 Kubernetes 组件时如何解决 ImagePullBackOff 的问题"
author: EmiliaHuang
resourceTags: 'AKS Enginee, Kubernetes, ImagePullBackOff'
ms.service: virtual-machines-linux
wacn.topic: aog
ms.topic: article
ms.author: Xuzhou.Huang
wacn.authorDisplayName: 'Xuzhou Huang'
ms.date: 04/15/2019
wacn.date: 04/15/2019
---

# 使用 AKS Engine 部署 Kubernetes 组件时如何解决 ImagePullBackOff 的问题

## 问题描述

当用户在中国区 Azure 中使用 AKS Engine 部署 Kubernetes 时，若使用了如 Calico 等组件，可能会遇到 master node 上 namespace kube-system 中部分 pod 创建失败的情况，并伴随错误状态 ImagePullBackOff。

## 原因分析

用户可以通过命令 `kubectl describe pod <pod name> --namespace kube-system` 查看状态为 ImagePullBackOff 的 pod 的日志信息。若用户部署了 Calico，可以看到关于 pod cluster-proportional-autoscaler 如下信息：

```shell
Failed to pull image "k8s.gcr.io/cluster-proportional-autoscaler-amd64:1.1.2-r2": rpc error: code = Unknown desc = Error response from daemon: Get https://k8s.gcr.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```

这是由于 yaml file 中为 image 指向了 global registry，在 Azure China 中用户可以从[代理](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)下载 image。

## 解决方案

用户可以到路径 **/etc/kubernetes/addons** 中查找相关的 yaml 文件，将 image 指向代理路径。同样以 Calico 为例，用户可以修改 master node 中路径 **/etc/kubernetes/addons** 下的 *calico-daemonset.yaml* 文件：

```shell
metadata:
  name: calico-typha-horizontal-autoscaler
  namespace: kube-system
  … …
      containers:
      - image: gcr.azk8s.cn/google_containers/cluster-proportional-autoscaler-amd64:1.1.2-r2
        name: autoscaler
```

>[!Note]
>`image:gcr.azk8s.cn/google_containers/cluster-proportional-autoscaler-amd64:1.1.2-r2` 即为修改后的路径

然后通过如下命令应用更改：

```shell
kubectl apply -f /etc/kubernetes/addons/calico-daemonset.yaml
```

更改应用成功以后，可以看到 image 可以成功下载和创建 container：

```shell
Events:
  Type    Reason     Age    From                                  Message
  ----    ------     ----   ----                                  -------
  Normal  Scheduled  3m11s  default-scheduler                     Successfully assigned kube-system/calico-typha-horizontal-autoscaler-5b8f8b8479-rj6ff to k8s-mtdev2cnnode-60770230-0
  Normal  Pulling    3m8s   kubelet, k8s-mtdev2cnnode-60770230-0  pulling image "gcr.azk8s.cn/google_containers/cluster-proportional-autoscaler-amd64:1.1.2-r2"
  Normal  Pulled     3m4s   kubelet, k8s-mtdev2cnnode-60770230-0  Successfully pulled image "gcr.azk8s.cn/google_containers/cluster-proportional-autoscaler-amd64:1.1.2-r2"
  Normal  Created    3m4s   kubelet, k8s-mtdev2cnnode-60770230-0  Created container
  Normal  Started    3m2s   kubelet, k8s-mtdev2cnnode-60770230-0  Started container
```

## 更多信息

* [Microsoft aks-engine deployment guide on Azure China](https://github.com/Azure/container-service-for-azure-china/tree/master/aks-engine)

* [AKS on Azure China Best Practices](https://github.com/Azure/container-service-for-azure-china/tree/master/aks-engine)