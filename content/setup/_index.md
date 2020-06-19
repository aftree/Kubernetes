---
no_issue: true
title: 💖 - 入门
main_menu: true
weight: 20
content_type: concept
card:
  name: 设置
  weight: 20
  anchors:
  - anchor: "#learning-environment"
    title: 学习环境
  - anchor: "#production-environment"
    title: 生产环境  
---

<!--
---
reviewers:
- brendandburns
- erictune
- mikedanese
no_issue: true
title: Getting started
main_menu: true
weight: 20
content_type: concept
card:
  name: setup
  weight: 20
  anchors:
  - anchor: "#learning-environment"
    title: Learning environment
  - anchor: "#production-environment"
    title: Production environment  
---
-->

<!-- overview -->

<!--
This section covers different options to set up and run Kubernetes.
-->
本节介绍了设置和运行 Kubernetes 环境的不同选项。

<!--
Different Kubernetes solutions meet different requirements: ease of maintenance, security, control, available resources, and expertise required to operate and manage a cluster.
-->
不同的 Kubernetes 解决方案满足不同的要求：易于维护、安全性、可控制性、可用资源以及操作和管理 Kubernetes 集群所需的专业知识。

<!--
You can deploy a Kubernetes cluster on a local machine, cloud, on-prem datacenter; or choose a managed Kubernetes cluster. You can also create custom solutions across a wide range of cloud providers, or bare metal environments.
-->
可以在本地机器、云、本地数据中心上部署 Kubernetes 集群，或选择一个托管的 Kubernetes 集群。还可以跨各种云提供商或裸机环境创建自定义解决方案。

<!--
More simply, you can create a Kubernetes cluster in learning and production environments.
-->
更简单地说，可以在学习和生产环境中创建一个 Kubernetes 集群。



<!-- body -->

<!--
## Learning environment
-->
## 学习环境

<!--
If you're learning Kubernetes, use the Docker-based solutions: tools supported by the Kubernetes community, or tools in the ecosystem to set up a Kubernetes cluster on a local machine.
-->
如果正打算学习 Kubernetes，请使用基于 Docker 的解决方案：Docker 是 Kubernetes 社区支持或生态系统中用来在本地计算机上设置 Kubernetes 集群的一种工具。

<!--
 table caption="Local machine solutions table that lists the tools supported by the community and the ecosystem to deploy Kubernetes." >}}

|Community           |Ecosystem     |
| ------------       | --------     |
| [Minikube](/docs/setup/learning-environment/minikube/) | [Docker Desktop](https://www.docker.com/products/docker-desktop)|
| [kind (Kubernetes IN Docker)](/docs/setup/learning-environment/kind/) | [Minishift](https://docs.okd.io/latest/minishift/)|
|                     | [MicroK8s](https://microk8s.io/)|
-->
 table caption="本地机器解决方案表，其中列出了社区和生态系统支持的用于部署 Kubernetes 的工具。" >}}

|社区           |生态系统     |
| ------------       | --------     |
| [Minikube](/docs/setup/learning-environment/minikube/) | [Docker Desktop](https://www.docker.com/products/docker-desktop)|
| [kind (Kubernetes IN Docker)](/docs/setup/learning-environment/kind/) | [Minishift](https://docs.okd.io/latest/minishift/)|
|                     | [MicroK8s](https://microk8s.io/)|


<!--
## Production environment
-->
## 生产环境

<!--
When evaluating a solution for a production environment, consider which aspects of operating a Kubernetes cluster (or _abstractions_) you want to manage yourself or offload to a provider.
-->
在评估生产环境的解决方案时，请考虑要管理自己 Kubernetes 集群（_抽象层面_）的哪些方面或将其转移给提供商。

<!--
[Kubernetes Partners](https://kubernetes.io/partners/#conformance) includes a list of [Certified Kubernetes](https://github.com/cncf/k8s-conformance/#certified-kubernetes) providers.
-->
[Kubernetes 合作伙伴](https://kubernetes.io/partners/#conformance) 包括一个 [已认证的 Kubernetes](https://github.com/cncf/k8s-conformance/#certified-kubernetes) 提供商列表。


