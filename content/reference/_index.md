---
title: 😍 - 参考
linkTitle: "😍 - 参考"
main_menu: true
weight: 70
content_type: concept
---

<!--
---
title: Reference
approvers:
- chenopis
linkTitle: "Reference"
main_menu: true
weight: 70
content_type: concept
---
-->

<!-- overview -->

<!--
This section of the Kubernetes documentation contains references.
-->
这是 Kubernetes 文档的参考部分。



<!-- body -->

## API 参考

* [Kubernetes API 概述](/docs/reference/using-api/api-overview/) - Kubernetes API 概述。
* Kubernetes API 版本
  * [1.17](/docs/reference/generated/kubernetes-api/v1.17/)
  * [1.16](/docs/reference/generated/kubernetes-api/v1.16/)
  * [1.15](/docs/reference/generated/kubernetes-api/v1.15/)
  * [1.14](/docs/reference/generated/kubernetes-api/v1.14/)
  * [1.13](/docs/reference/generated/kubernetes-api/v1.13/)

<!--
## API Reference

* [Kubernetes API Overview](/docs/reference/using-api/api-overview/) - Overview of the API for Kubernetes.
* Kubernetes API Versions
  * [1.17](/docs/reference/generated/kubernetes-api/v1.17/)
  * [1.16](/docs/reference/generated/kubernetes-api/v1.16/)
  * [1.15](/docs/reference/generated/kubernetes-api/v1.15/)
  * [1.14](/docs/reference/generated/kubernetes-api/v1.14/)
  * [1.13](/docs/reference/generated/kubernetes-api/v1.13/)
-->

## API 客户端库

如果您需要通过编程语言调用 Kubernetes API，您可以使用
[客户端库](/docs/reference/using-api/client-libraries/)。以下是官方支持的客户端库：

- [Kubernetes Go 语言客户端库](https://github.com/kubernetes/client-go/)
- [Kubernetes Python 语言客户端库](https://github.com/kubernetes-client/python)
- [Kubernetes Java 语言客户端库](https://github.com/kubernetes-client/java)
- [Kubernetes JavaScript 语言客户端库](https://github.com/kubernetes-client/javascript)

<!--
## API Client Libraries

To call the Kubernetes API from a programming language, you can use
[client libraries](/docs/reference/using-api/client-libraries/). Officially supported
client libraries:

- [Kubernetes Go client library](https://github.com/kubernetes/client-go/)
- [Kubernetes Python client library](https://github.com/kubernetes-client/python)
- [Kubernetes Java client library](https://github.com/kubernetes-client/java)
- [Kubernetes JavaScript client library](https://github.com/kubernetes-client/javascript)
-->

## CLI 参考

* [kubectl](/docs/user-guide/kubectl-overview) - 主要的 CLI 工具，用于运行命令和管理 Kubernetes 集群。
    * [JSONPath](/docs/user-guide/jsonpath/) - 通过 kubectl 使用 [JSONPath 表达式](http://goessner.net/articles/JsonPath/) 的语法指南。
* [kubeadm](/docs/admin/kubeadm/) - 此 CLI 工具可轻松配置安全的 Kubernetes 集群。
* [kubefed](/docs/admin/kubefed/) - 此 CLI 工具可帮助您管理集群联邦。

<!--
## CLI Reference

* [kubectl](/docs/user-guide/kubectl-overview) - Main CLI tool for running commands and managing Kubernetes clusters.
    * [JSONPath](/docs/user-guide/jsonpath/) - Syntax guide for using [JSONPath expressions](http://goessner.net/articles/JsonPath/) with kubectl.
* [kubeadm](/docs/admin/kubeadm/) - CLI tool to easily provision a secure Kubernetes cluster.
* [kubefed](/docs/admin/kubefed/) - CLI tool to help you administrate your federated clusters.
-->

## 配置参考

* [kubelet](/docs/admin/kubelet/) - 在每个节点上运行的主 *节点代理* 。kubelet 采用一组 PodSpecs 并确保所描述的容器健康地运行。
* [kube-apiserver](/docs/admin/kube-apiserver/) - REST API，用于验证和配置 API 对象（如 pod，服务，副本控制器）的数据。
* [kube-controller-manager](/docs/admin/kube-controller-manager/) - 一个守护进程，它嵌入到了 Kubernetes 的附带的核心控制循环。
* [kube-proxy](/docs/admin/kube-proxy/) - 可以跨一组后端进行简单的 TCP/UDP 流转发或循环 TCP/UDP 转发。
* [kube-scheduler](/docs/admin/kube-scheduler/) - 一个调度程序，用于管理可用性、性能和容量。
* [federation-apiserver](/docs/admin/federation-apiserver/) - 联邦集群的 API 服务器。
* [federation-controller-manager](/docs/admin/federation-controller-manager/) - 一个守护进程，它嵌入到了 Kubernetes 联邦的附带的核心控制循环。

<!--
## Config Reference

* [kubelet](/docs/admin/kubelet/) - The primary *node agent* that runs on each node. The kubelet takes a set of PodSpecs and ensures that the described containers are running and healthy.
* [kube-apiserver](/docs/admin/kube-apiserver/) - REST API that validates and configures data for API objects such as  pods, services, replication controllers.
* [kube-controller-manager](/docs/admin/kube-controller-manager/) - Daemon that embeds the core control loops shipped with Kubernetes.
* [kube-proxy](/docs/admin/kube-proxy/) - Can do simple TCP/UDP stream forwarding or round-robin TCP/UDP forwarding across a set of back-ends.
* [kube-scheduler](/docs/admin/kube-scheduler/) - Scheduler that manages availability, performance, and capacity.
* [federation-apiserver](/docs/admin/federation-apiserver/) - API server for federated clusters.
* [federation-controller-manager](/docs/admin/federation-controller-manager/) - Daemon that embeds the core control loops shipped with Kubernetes federation.
-->

## 设计文档

Kubernetes 功能的设计文档归档，不妨考虑从 [Kubernetes 架构](https://git.k8s.io/community/contributors/design-proposals/architecture/architecture.md) 和 [Kubernetes 设计概述](https://git.k8s.io/community/contributors/design-proposals)开始阅读。

<!--
## Design Docs

An archive of the design docs for Kubernetes functionality. Good starting points are [Kubernetes Architecture](https://git.k8s.io/community/contributors/design-proposals/architecture/architecture.md) and [Kubernetes Design Overview](https://git.k8s.io/community/contributors/design-proposals).

-->

