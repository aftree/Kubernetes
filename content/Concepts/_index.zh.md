---
title: 😊 - Kubernetes Concepts
main_menu: true
content_type: concept
weight: 40
---

<!-- ---
title: Concepts
main_menu: true
content_type: concept
weight: 40
--- -->

<!-- overview -->

<!--
The Concepts section helps you learn about the parts of the Kubernetes system and the abstractions Kubernetes uses to represent your and helps you obtain a deeper understanding of how Kubernetes works.
-->

概念部分可以帮助你了解 Kubernetes 的各个组成部分以及 Kubernetes 用来表示集群的一些抽象概念，并帮助你更加深入的理解 Kubernetes 是如何工作的。



<!-- body -->

<!--
## Overview
-->

## 概述

<!--
To work with Kubernetes, you use *Kubernetes API objects* to describe your cluster's *desired state*: what applications or other workloads you want to run, what container images they use, the number of replicas, what network and disk resources you want to make available, and more. You set your desired state by creating objects using the Kubernetes API, typically via the command-line interface, `kubectl`. You can also use the Kubernetes API directly to interact with the cluster and set or modify your desired state.
-->

要使用 Kubernetes，你需要用 *Kubernetes API 对象* 来描述集群的 *预期状态（desired state）* ：包括你需要运行的应用或者负载，它们使用的镜像、副本数，以及所需网络和磁盘资源等等。你可以使用命令行工具 `kubectl`  来调用 Kubernetes API 创建对象，通过所创建的这些对象来配置预期状态。你也可以直接调用 Kubernetes API 和集群进行交互，设置或者修改预期状态。

<!--
Once you've set your desired state, the *Kubernetes Control Plane* makes the cluster's current state match the desired state via the Pod Lifecycle Event Generator ([PLEG](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/node/pod-lifecycle-event-generator.md)). To do so, Kubernetes performs a variety of tasks automatically--such as starting or restarting containers, scaling the number of replicas of a given application, and more. The Kubernetes Control Plane consists of a collection of processes running on your cluster:
-->

一旦你设置了你所需的目标状态，*Kubernetes 控制面（control plane）* 会通过 Pod 生命周期事件生成器([PLEG](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/node/pod-lifecycle-event-generator.md))，促成集群的当前状态符合其预期状态。为此，Kubernetes 会自动执行各类任务，比如运行或者重启容器、调整给定应用的副本数等等。Kubernetes 控制面由一组运行在集群上的进程组成：

<!--
* The **Kubernetes Master** is a collection of three processes that run on a single node in your cluster, which is designated as the master node. Those processes are: [kube-apiserver](/docs/admin/kube-apiserver/), [kube-controller-manager](/docs/admin/kube-controller-manager/) and [kube-scheduler](/docs/admin/kube-scheduler/).
* Each individual non-master node in your cluster runs two processes:
  * **[kubelet](/docs/admin/kubelet/)**, which communicates with the Kubernetes Master.
  * **[kube-proxy](/docs/admin/kube-proxy/)**, a network proxy which reflects Kubernetes networking services on each node.
-->

* **Kubernetes 主控组件（Master）** 包含三个进程，都运行在集群中的某个节点上，主控组件通常这个节点被称为 master 节点。这些进程包括：[kube-apiserver](/docs/admin/kube-apiserver/)、[kube-controller-manager](/docs/admin/kube-controller-manager/) 和 [kube-scheduler](/docs/admin/kube-scheduler/)。
* 集群中的每个非 master 节点都运行两个进程：
  * **[kubelet](/docs/admin/kubelet/)**，和 master 节点进行通信。
  * **[kube-proxy](/docs/admin/kube-proxy/)**，一种网络代理，将 Kubernetes 的网络服务代理到每个节点上。

<!--
## Kubernetes Objects
-->

## Kubernetes 对象

<!--
Kubernetes contains a number of abstractions that represent the state of your system: deployed containerized applications and workloads, their associated network and disk resources, and other information about what your cluster is doing. These abstractions are represented by objects in the Kubernetes API. See [Understanding Kubernetes Objects](/docs/concepts/overview/working-with-objects/kubernetes-objects/) for more details.
-->

Kubernetes 包含若干用来表示系统状态的抽象层，包括：已部署的容器化应用和负载、与它们相关的网络和磁盘资源以及有关集群正在运行的其他操作的信息。这些抽象使用 Kubernetes API 对象来表示。有关更多详细信息，请参阅[了解 Kubernetes 对象](/docs/concepts/overview/working-with-objects/kubernetes-objects/)。

<!--
The basic Kubernetes objects include:
-->

基本的 Kubernetes 对象包括：

* [Pod](/docs/concepts/workloads/pods/pod-overview/)
* [Service](/docs/concepts/services-networking/service/)
* [Volume](/docs/concepts/storage/volumes/)
* [Namespace](/docs/concepts/overview/working-with-objects/namespaces/)

<!--
Kubernetes also contains higher-level abstractions that rely on [Controllers](/docs/concepts/architecture/controller/) to build upon the basic objects, and provide additional functionality and convenience features. These include:
-->

Kubernetes 也包含大量的被称作 [Controller](/docs/concepts/architecture/controller/) 的高级抽象。控制器基于基本对象构建并提供额外的功能和方便使用的特性。具体包括：

* [Deployment](/docs/concepts/workloads/controllers/deployment/)
* [DaemonSet](/docs/concepts/workloads/controllers/daemonset/)
* [StatefulSet](/docs/concepts/workloads/controllers/statefulset/)
* [ReplicaSet](/docs/concepts/workloads/controllers/replicaset/)
* [Job](/docs/concepts/workloads/controllers/jobs-run-to-completion/)

<!--
## Kubernetes Control Plane
-->

## Kubernetes 控制面

<!--
The various parts of the Kubernetes Control Plane, such as the Kubernetes Master and kubelet processes, govern how Kubernetes communicates with your cluster. The Control Plane maintains a record of all of the Kubernetes Objects in the system, and runs continuous control loops to manage those objects' state. At any given time, the Control Plane's control loops will respond to changes in the cluster and work to make the actual state of all the objects in the system match the desired state that you provided.
-->

关于 Kubernetes 控制平面的各个部分，（如 Kubernetes 主控组件和 kubelet 进程），管理着 Kubernetes 如何与你的集群进行通信。控制平面维护着系统中所有的 Kubernetes 对象的状态记录，并且通过连续的控制循环来管理这些对象的状态。在任意的给定时间点，控制面的控制环都能响应集群中的变化，并且让系统中所有对象的实际状态与你提供的预期状态相匹配。

<!--
For example, when you use the Kubernetes API to create a Deployment, you provide a new desired state for the system. The Kubernetes Control Plane records that object creation, and carries out your instructions by starting the required applications and scheduling them to cluster nodes--thus making the cluster's actual state match the desired state.
-->

比如， 当你通过 Kubernetes API 创建一个 Deployment 对象，你就为系统增加了一个新的目标状态。Kubernetes 控制平面记录着对象的创建，并启动必要的应用然后将它们调度至集群某个节点上来执行你的指令，以此来保持集群的实际状态和目标状态的匹配。

<!--
### Kubernetes Master
-->

### Kubernetes Master 节点

<!--
The Kubernetes master is responsible for maintaining the desired state for your cluster. When you interact with Kubernetes, such as by using the `kubectl` command-line interface, you're communicating with your cluster's Kubernetes master.
-->

Kubernetes master 节点负责维护集群的目标状态。当你要与 Kubernetes 通信时，使用如 `kubectl` 的命令行工具，就可以直接与 Kubernetes master 节点进行通信。

<!--
> The "master" refers to a collection of processes managing the cluster state.  Typically all these processes run on a single node in the cluster, and this node is also referred to as the master. The master can also be replicated for availability and redundancy.
-->

> "master" 是指管理集群状态的一组进程的集合。通常这些进程都跑在集群中一个单独的节点上，并且这个节点被称为 master 节点。master 节点也可以扩展副本数，来获取更好的可用性及冗余。

<!--
### Kubernetes Nodes
-->

### Kubernetes Node 节点

<!--
The nodes in a cluster are the machines (VMs, physical servers, etc) that run your applications and cloud workflows. The Kubernetes master controls each node; you'll rarely interact with nodes directly.
-->

集群中的 node 节点（虚拟机、物理机等等）都是用来运行你的应用和云工作流的机器。Kubernetes master 节点控制所有 node 节点；你很少需要和 node 节点进行直接通信。







<!--
If you would like to write a concept page, see
[Using Page Templates](/docs/home/contribute/page-templates/)
for information about the concept page type and the concept template.
-->

如果你想编写一个概念页面，请参阅[使用页面模板](/docs/home/contribute/page-templates/)获取更多有关概念页面类型和概念模板的信息。


