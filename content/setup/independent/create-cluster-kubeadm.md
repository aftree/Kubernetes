---
title: 💖 - 使用 kubeadm 创建一个单主集群
content_type: task
weight: 30
---

<!-- ---
reviewers:
- sig-cluster-lifecycle
title: Creating a single master cluster with kubeadm
content_type: task
weight: 30
--- -->


<!-- overview -->
<!--
<img src="https://raw.githubusercontent.com/cncf/artwork/master/projects/kubernetes/certified-kubernetes/versionless/color/certified-kubernetes-color.png" align="right" width="150px">**kubeadm** helps you bootstrap a minimum viable Kubernetes cluster that conforms to best practices.  With kubeadm, your cluster should pass [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification). Kubeadm also supports other cluster
lifecycle functions, such as upgrades, downgrade, and managing [bootstrap tokens](/docs/reference/access-authn-authz/bootstrap-tokens/).  -->

<img src="https://raw.githubusercontent.com/cncf/artwork/master/projects/kubernetes/certified-kubernetes/versionless/color/certified-kubernetes-color.png" align="right" width="150px">**kubeadm** 能帮助您建立一个小型的符合最佳实践的 Kubernetes 集群。通过使用 kubeadm, 您的集群会符合 [Kubernetes 合规性测试](https://kubernetes.io/blog/2017/10/software-conformance-certification)的要求. Kubeadm 也支持其他的集群生命周期操作，比如升级、降级和管理[启动引导令牌](/docs/reference/access-authn-authz/bootstrap-tokens/)。

<!-- Because you can install kubeadm on various types of machine (e.g. laptop, server,
Raspberry Pi, etc.), it's well suited for integration with provisioning systems
such as Terraform or Ansible. -->

因为您可以在不同类型的机器（比如笔记本、服务器和树莓派等）上安装 kubeadm，因此它非常适合与 Terraform 或 Ansible 这类自动化管理系统集成。

<!-- kubeadm's simplicity means it can serve a wide range of use cases:

- New users can start with kubeadm to try Kubernetes out for the first time.
- Users familiar with Kubernetes can spin up clusters with kubeadm and test their applications.
- Larger projects can include kubeadm as a building block in a more complex system that can also include other installer tools. -->

kubeadm 的简单便捷为大家带来了广泛的用户案例：

- 新用户可以从 kubeadm 开始来试用 Kubernetes。
- 熟悉 Kubernetes 的用户可以使用 kubeadm 快速搭建集群并测试他们的应用。
- 大型的项目可以将 kubeadm 和其他的安装工具一起形成一个比较复杂的系统。

<!-- kubeadm is designed to be a simple way for new users to start trying
Kubernetes out, possibly for the first time, a way for existing users to
test their application on and stitch together a cluster easily, and also to be
a building block in other ecosystem and/or installer tool with a larger
scope. -->

kubeadm 的设计初衷是为新用户提供一种便捷的方式来首次试用 Kubernetes，
同时也方便老用户搭建集群测试他们的应用。
此外 kubeadm 也可以跟其它生态系统与/或安装工具集成到一起，提供更强大的功能。

<!-- You can install _kubeadm_ very easily on operating systems that support
installing deb or rpm packages. The responsible SIG for kubeadm,
[SIG Cluster Lifecycle](https://github.com/kubernetes/community/tree/master/sig-cluster-lifecycle), provides these packages pre-built for you,
but you may also build them from source for other OSes. -->

您可以很方便地在支持 rpm 或 deb 软件包的操作系统上安装 _kubeadm_。对应 kubeadm 的 SIG，
[SIG Cluster Lifecycle](https://github.com/kubernetes/community/tree/master/sig-cluster-lifecycle)，
提供了预编译的这类安装包，当然您也可以自己基于源码为其它操作系统来构造安装包。

<!-- ### kubeadm Maturity

| Area                      | Maturity Level |
|---------------------------|--------------- |
| Command line UX           | beta           |
| Implementation            | beta           |
| Config file API           | alpha          |
| Self-hosting              | alpha          |
| kubeadm alpha subcommands | alpha          |
| CoreDNS                   | GA             |
| DynamicKubeletConfig      | alpha          | -->

### kubeadm 成熟程度

| 功能                       | 成熟程度        |
|---------------------------|--------------- |
| 命令行用户体验               | beta           |
| 功能实现                    | beta           |
| 配置文件 API                | alpha          |
| 自托管                     | alpha          |
| kubeadm alpha 子命令       | alpha          |
| CoreDNS                   | GA             |
| 动态 Kubelet 配置          | alpha          |

<!-- kubeadm's overall feature state is **Beta** and will soon be graduated to
**General Availability (GA)** during 2018. Some sub-features, like self-hosting
or the configuration file API are still under active development. The
implementation of creating the cluster may change slightly as the tool evolves,
but the overall implementation should be pretty stable. Any commands under
`kubeadm alpha` are by definition, supported on an alpha level. -->

kubeadm 的整体功能目前还是 **Beta** 状态，然而很快在 2018 年就会转换成**正式发布 (GA)** 状态。
一些子功能，比如自托管或者配置文件 API 还在开发过程当中。
随着工具的发展，创建集群的方法可能会有所变化，但是整体部署方案还是比较稳定的。
在 `kubeadm alpha` 下面的任何命令都只是 alpha 状态，目前只提供初期阶段的服务。

<!-- ### Support timeframes

Kubernetes releases are generally supported for nine months, and during that
period a patch release may be issued from the release branch if a severe bug or
security issue is found. Here are the latest Kubernetes releases and the support
timeframe; which also applies to `kubeadm`.

| Kubernetes version | Release month  | End-of-life-month |
|--------------------|----------------|-------------------|
| v1.6.x             | March 2017     | December 2017     |
| v1.7.x             | June 2017      | March 2018        |
| v1.8.x             | September 2017 | June 2018         |
| v1.9.x             | December 2017  | September 2018    |
| v1.10.x            | March 2018     | December 2018     |
| v1.11.x            | June 2018      | March 2019        |
| v1.12.x            | September 2018 | June 2019         |
-->

### 维护周期

Kubernetes 发布的版本通常只维护支持九个月，在维护周期内，如果发现有比较重大的 bug 或者安全问题的话，
可能会发布一个补丁版本。下面是 Kubernetes 的发布和维护周期，同时也适用于 `kubeadm`。

| Kubernetes 版本     | 发行月份        | 终止维护月份        |
|--------------------|----------------|-------------------|
| v1.6.x             | 2017 年 3 月    | 2017 年 12 月     |
| v1.7.x             | 2017 年 6 月    | 2018 年 3 月      |
| v1.8.x             | 2017 年 9 月    | 2018 年 6 月      |
| v1.9.x             | 2017 年 12 月   |  2018 年 9 月     |
| v1.10.x            | 2018 年 3 月    | 2018 年 12 月     |
| v1.11.x            | 2018 年 6 月    | 2019 年 3 月      |
| v1.12.x            | 2018 年 9 月    | 2019 年 6 月      |



## . heading "prerequisites" %}}

<!--

- One or more machines running a deb/rpm-compatible OS, for example Ubuntu or CentOS
- 2 GB or more of RAM per machine. Any less leaves little room for your
   apps.
- 2 CPUs or more on the master
- Full network connectivity among all machines in the cluster. A public or
   private network is fine.
 -->

- 一个或者多个兼容 deb 或者 rpm 软件包的操作系统，比如 Ubuntu 或者 CentOS
- 每台机器 2 GB 以上的内存，内存不足时应用会受限制
- 主节点上 2 CPU 以上
- 集群里所有的机器有完全的网络连接，公有网络或者私有网络都可以


<!-- steps -->
<!--
## Objectives

* Install a single master Kubernetes cluster or [high availability cluster](https://kubernetes.io/docs/setup/independent/high-availability/)
* Install a Pod network on the cluster so that your Pods can
  talk to each other
-->

## 目标

* 搭建一个单主 Kubernetes 集群或者[高可用集群](/docs/setup/independent/high-availability/)
* 在集群上安装 Pod 网络组件以便 Pod 之间可以互相通信

<!-- ## Instructions

### Installing kubeadm on your hosts

See ["Installing kubeadm"](/docs/setup/independent/install-kubeadm/).

. note >}}
**Note:** If you have already installed kubeadm, run `apt-get update &&
apt-get upgrade` or `yum update` to get the latest version of kubeadm.

When you upgrade, the kubelet restarts every few seconds as it waits in a crashloop for
kubeadm to tell it what to do. This crashloop is expected and normal.
After you initialize your master, the kubelet runs normally.
. /note >}}-->

## 步骤

### 在您的机器上安装 kubeadm

请查阅[安装 kubeadm](/docs/setup/independent/install-kubeadm/)。

. note >}}
如果您的机器已经安装了 kubeadm, 请运行 `apt-get update &&
apt-get upgrade` 或者 `yum update` 来升级至最新版本的 kubeadm.

升级过程中，kubelet 会每隔几秒钟重启并陷入了不断循环等待 kubeadm 发布指令的状态。
这个死循环的过程是正常的，当升级并初始化完成您的主节点之后，kubelet 才会正常运行。
. /note >}}

<!-- ### Initializing your master

The master is the machine where the control plane components run, including
etcd (the cluster database) and the API server (which the kubectl CLI
communicates with). -->

### 初始化您的主节点

主节点是集群里运行控制面的机器，包括 etcd (集群的数据库)和 API 服务（kubectl CLI 与之交互）。

<!-- 1. Choose a Pod network add-on, and verify whether it requires any arguments to
be passed to kubeadm initialization. Depending on which
third-party provider you choose, you might need to set the `--Pod-network-cidr` to
a provider-specific value. See [Installing a Pod network add-on](#Pod-network).
1. (Optional) Unless otherwise specified, kubeadm uses the network interface associated
with the default gateway to advertise the master's IP. To use a different
network interface, specify the `--apiserver-advertise-address=<ip-address>` argument
to `kubeadm init`. To deploy an IPv6 Kubernetes cluster using IPv6 addressing, you
must specify an IPv6 address, for example `--apiserver-advertise-address=fd00::101`
1. (Optional) Run `kubeadm config images pull` prior to `kubeadm init` to verify
connectivity to gcr.io registries.

Now run:

```bash
kubeadm init <args>
​``` -->


1. 选择一个 Pod 网络插件，并检查是否在 kubeadm 初始化过程中需要传入什么参数。这个取决于
您选择的网络插件，您可能需要设置 `--Pod-network-cidr` 来指定网络驱动的 CIDR。请参阅[安装网络插件](#Pod-network)。
1. (可选) 除非特别指定，kubeadm 会使用默认网关所在的网络接口广播其主节点的 IP 地址。若需使用其他网络接口，请
给 `kubeadm init` 设置 `--apiserver-advertise-address=<ip-address>` 参数。如果需要部署
IPv6 的集群，则需要指定一个 IPv6 地址，比如 `--apiserver-advertise-address=fd00::101`。
1. (可选) 在运行 `kubeadm init` 之前请先执行 `kubeadm config images pull` 来测试与 gcr.io 的连接。

现在运行:

​```bash
kubeadm init <args>
```

<!-- ### More information

For more information about `kubeadm init` arguments, see the [kubeadm reference guide](/docs/reference/setup-tools/kubeadm/kubeadm/).

For a complete list of configuration options, see the [configuration file documentation](/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file).

To customize control plane components, including optional IPv6 assignment to liveness probe for control plane components and etcd server, provide extra arguments to each component as documented in [custom arguments](/docs/admin/kubeadm#custom-args). -->

### 补充信息

想了解更多关于 `kubeadm init` 的参数, 请参阅[kubeadm 参考指南](/docs/reference/setup-tools/kubeadm/kubeadm/)。

想了解完整的配置选项，请参阅[配置文件](/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file)。

如果想定制控制面组件，包括为活跃性探测和 etcd 服务提供 IPv6 支持以及为各组件提供额外参数，请参阅[定制参数](/docs/admin/kubeadm#custom-args)。

<!-- To run `kubeadm init` again, you must first [tear down the cluster](#tear-down).

If you join a node with a different architecture to your cluster, create a separate
Deployment or DaemonSet for `kube-proxy` and `kube-dns` on the node. This is because the Docker images for these
components do not currently support multi-architecture.

`kubeadm init` first runs a series of prechecks to ensure that the machine
is ready to run Kubernetes. These prechecks expose warnings and exit on errors. `kubeadm init`
then downloads and installs the cluster control plane components. This may take several minutes.
The output should look like: -->

如果需要再次运行 `kubeadm init`，您必须先[卸载集群](#tear-down)。

如果您需要将不同架构的节点加入您的集群，请单独在这类节点上为 `kube-proxy` 和 `kube-dns` 创建 Deployment 或 DaemonSet。
这是因为这些组件的 Docker 镜像并不支持多架构。

`kubeadm init` 首先会执行一系列的运行前检查来确保机器满足运行 Kubernetes 的条件。
这些检查会抛出警告并在发现错误的时候终止整个初始化进程。
然后 `kubeadm init` 会下载并安装集群的控制面组件，这可能会花费几分钟时间，其输出如下所示：

```none
[init] Using Kubernetes version: vX.Y.Z
[preflight] Running pre-flight checks
[kubeadm] WARNING: starting in 1.8, tokens expire after 24 hours by default (if you require a non-expiring token use --token-ttl 0)
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [kubeadm-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.138.0.4]
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated sa key and public key.
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "scheduler.conf"
[controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] Waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests"
[init] This often takes around a minute; or longer if the control plane images have to be pulled.
[apiclient] All control plane components are healthy after 39.511972 seconds
[uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[markmaster] Will mark node master as master by adding a label and a taint
[markmaster] Master master tainted and labelled with key/value: node-role.kubernetes.io/master=""
[bootstraptoken] Using token: <token>
[bootstraptoken] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run (as a regular user):

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a Pod network to the cluster.
Run "kubectl apply -f [Podnetwork].yaml" with one of the addon options listed at:
  http://kubernetes.io/docs/admin/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```


<!-- To make kubectl work for your non-root user, run these commands, which are
also part of the `kubeadm init` output:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
​``` -->

如果需要让普通用户可以运行 kubectl，请运行如下命令，其实这也是 `kubeadm init` 输出的一部分：

​```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

<!-- Alternatively, if you are the `root` user, you can run:

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
​``` -->

或者，如果您是 `root` 用户，则可以运行：

​```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

<!-- Make a record of the `kubeadm join` command that `kubeadm init` outputs. You
need this command to [join nodes to your cluster](#join-nodes).

The token is used for mutual authentication between the master and the joining
nodes.  The token included here is secret. Keep it safe, because anyone with this
token can add authenticated nodes to your cluster. These tokens can be listed,
created, and deleted with the `kubeadm token` command. See the
[kubeadm reference guide](/docs/reference/setup-tools/kubeadm/kubeadm-token/). -->

请备份好 `kubeadm init` 输出中的 `kubeadm join` 命令，因为您会需要这个命令来[给集群添加节点](#join-nodes)。

令牌是主节点和新添加的节点之间进行相互身份验证的，因此请确保其安全。任何人只要知道了这些令牌，就可以随便给您的集群添加节点。
可以使用 `kubeadm token` 命令来列出、创建和删除这类令牌。
请参阅[kubeadm 参考指南](/docs/reference/setup-tools/kubeadm/kubeadm-token/)。

<!-- ### Installing a Pod network add-on {#Pod-network}

. caution >}}
**Caution:** This section contains important information about installation and deployment order. Read it carefully before proceeding.
. /caution >}}

You must install a Pod network add-on so that your Pods can communicate with
each other. -->

### 安装 Pod 网络插件 {#Pod-network}

. caution >}}
**注意:** 这一节包含了安装和部署顺序的重要信息，执行之前请仔细阅读。
. /caution >}}

您必须先安装 Pod 网络插件，以便您的 Pod 可以互相通信。

<!-- **The network must be deployed before any applications. Also, CoreDNS will not start up before a network is installed.
kubeadm only supports Container Network Interface (CNI) based networks (and does not support kubenet).**

Several projects provide Kubernetes Pod networks using CNI, some of which also
support [Network Policy](/docs/concepts/services-networking/networkpolicies/). See the [add-ons page](/docs/concepts/cluster-administration/addons/) for a complete list of available network add-ons.
- IPv6 support was added in [CNI v0.6.0](https://github.com/containernetworking/cni/releases/tag/v0.6.0).
- [CNI bridge](https://github.com/containernetworking/plugins/blob/master/plugins/main/bridge/README.md) and [local-ipam](https://github.com/containernetworking/plugins/blob/master/plugins/ipam/host-local/README.md) are the only supported IPv6 network plugins in Kubernetes version 1.9. -->

**网络必须在部署任何应用之前部署好。此外，在网络安装之前是 CoreDNS 不会启用的。
kubeadm 只支持基于容器网络接口（CNI）的网络而且不支持 kubenet 。**

有一些项目为 Kubernetes 提供使用 CNI 的 Pod 网络，其中一些也支持[网络策略](/docs/concepts/services-networking/networkpolicies/).
请参阅[插件页面](/docs/concepts/cluster-administration/addons/)了解可用网络插件的完整列表。
- [CNI v0.6.0](https://github.com/containernetworking/cni/releases/tag/v0.6.0) 也提供了 IPv6 的支持。
- [CNI 网桥](https://github.com/containernetworking/plugins/blob/master/plugins/main/bridge/README.md) 和 [local-ipam](https://github.com/containernetworking/plugins/blob/master/plugins/ipam/host-local/README.md) 是 Kubernetes 1.9 版本里提供的唯一支持 IPv6 的网络插件。

<!-- Note that kubeadm sets up a more secure cluster by default and enforces use of [RBAC](/docs/reference/access-authn-authz/rbac/).
Make sure that your network manifest supports RBAC.

You can install a Pod network add-on with the following command:

```bash
kubectl apply -f <add-on.yaml>
​``` -->

注意 kubeadm 默认会创建一个比较安全的集群并强制启用[RBAC](/docs/reference/access-authn-authz/rbac/)。
请确保您的网络方案支持 RBAC。

您可以使用下列命令安装网络插件：

​```bash
kubectl apply -f <add-on.yaml>
```

<!-- You can install only one Pod network per cluster.

. tabs name="tabs-Pod-install" >}}
. tab name="Choose one..." %}}
Please select one of the tabs to see installation instructions for the respective third-party Pod Network Provider.
. /tab %}} -->

您仅可以给任何一个集群安装一个网络插件。

. tabs name="tabs-Pod-install" >}}
. tab name="Choose one..." %}}
请选择一个选项来查看对应的第三方网络插件驱动的安装向导。
. /tab %}}

<!--
For more information about using Calico, see [Quickstart for Calico on Kubernetes](https://docs.projectcalico.org/latest/getting-started/kubernetes/), [Installing Calico for policy and networking](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/calico), and other related resources.

For Calico to work correctly, you need to pass `--Pod-network-cidr=192.168.0.0/16` to `kubeadm init` or update the `calico.yml` file to match your Pod network. Note that Calico works on `amd64` only.

```shell
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
```
-->

. tab name="Calico" %}}
想了解关于 Calico 的使用的更多信息, 请参阅[Kubernetes上的Calico快速实践](https://docs.projectcalico.org/latest/getting-started/kubernetes/)、[安装 Calico 实现网络策略](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/calico)和其他相关资源。

为了 Calico 可以正确工作，您需要给 `kubeadm init` 传递 `--Pod-network-cidr=192.168.0.0/16` 这样的选项，
或者根据您的网络方案更新 `calico.yml` 。注意 Calico 只适用于 `amd64` 架构。

```shell
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
```

. /tab %}}

<!--
Canal uses Calico for policy and Flannel for networking. Refer to the Calico documentation for the [official getting started guide](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/flannel).

For Canal to work correctly, `--Pod-network-cidr=10.244.0.0/16` has to be passed to `kubeadm init`. Note that Canal works on `amd64` only.

```shell
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/canal.yaml
```
-->

. tab name="Canal" %}}
Canal 使用 Calico 提供的网络策略和 Flannel 提供的网络功能。请查阅 Calico 的官方文档
[入门指引](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/flannel)。

为了 Canal 可以正确运行，`kubeadm init` 运行时需要设置`--Pod-network-cidr=10.244.0.0/16`，同时注意它只适用于 `amd64` 架构。

```shell
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/canal.yaml
```

. /tab %}}
<!--
For more information about using Cilium with Kubernetes, see [Quickstart for Cilium on Kubernetes](http://docs.cilium.io/en/v1.2/kubernetes/quickinstall/) and [Kubernetes Install guide for Cilium](http://docs.cilium.io/en/v1.2/kubernetes/install/).

Passing `--Pod-network-cidr` option to `kubeadm init` is not required, but highly recommended.

These commands will deploy Cilium with its own etcd managed by etcd operator.

```shell
# Download required manifests from Cilium repository
wget https://github.com/cilium/cilium/archive/v1.2.0.zip
unzip v1.2.0.zip
cd cilium-1.2.0/examples/kubernetes/addons/etcd-operator

# Generate and deploy etcd certificates
export CLUSTER_DOMAIN=$(kubectl get ConfigMap --namespace kube-system coredns -o yaml | awk '/kubernetes/ {print $2}')
tls/certs/gen-cert.sh $CLUSTER_DOMAIN
tls/deploy-certs.sh

# Label kube-dns with fixed identity label
kubectl label -n kube-system Pod $(kubectl -n kube-system get Pods -l k8s-app=kube-dns -o jsonpath='{range .items[]}{.metadata.name}{" "}{end}') io.cilium.fixed-identity=kube-dns

kubectl create -f ./

# Wait several minutes for Cilium, coredns and etcd Pods to converge to a working state
```
-->
. tab name="Cilium" %}}
想了解 Kubernetes 上使用 Cilium 的更多相关信息，请查参阅[Kubernetes 上 Cilium 的快速指南](http://docs.cilium.io/en/v1.2/kubernetes/quickinstall/) 和 [Kubernetes 上 Cilium 的安装向导](http://docs.cilium.io/en/v1.2/kubernetes/install/)。

尽管这里并不要求给 `kubeadm init` 设置 `--Pod-network-cidr` 参数，但是这是一个高度推荐操作的步骤。

这些命令会部署 Cilium 和它自己受 etcd 操作者管理的 etcd。

```shell
# 从 Cilium 库下载所需清单文件
wget https://github.com/cilium/cilium/archive/v1.2.0.zip
unzip v1.2.0.zip
cd cilium-1.2.0/examples/kubernetes/addons/etcd-operator

# 生成并部署 etcd 证书
export CLUSTER_DOMAIN=$(kubectl get ConfigMap --namespace kube-system coredns -o yaml | awk '/kubernetes/ {print $2}')
tls/certs/gen-cert.sh $CLUSTER_DOMAIN
tls/deploy-certs.sh

# 为 kube-dns 设置固定的标识标签
kubectl label -n kube-system Pod $(kubectl -n kube-system get Pods -l k8s-app=kube-dns -o jsonpath='{range .items[]}{.metadata.name}{" "}{end}') io.cilium.fixed-identity=kube-dns

kubectl create -f ./

# 等待几分钟，Cilium、coredns 和 etcd 的 Pods 会收敛到工作状态
```

. /tab %}}

<!--

For `flannel` to work correctly, you must pass `--Pod-network-cidr=10.244.0.0/16` to `kubeadm init`.

Set `/proc/sys/net/bridge/bridge-nf-call-iptables` to `1` by running `sysctl net.bridge.bridge-nf-call-iptables=1`
to pass bridged IPv4 traffic to iptables' chains. This is a requirement for some CNI plugins to work, for more information
please see [here](https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements).

Note that `flannel` works on `amd64`, `arm`, `arm64` and `ppc64le`.

```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

For more information about `flannel`, see [the CoreOS flannel repository on GitHub
](https://github.com/coreos/flannel).-->

. tab name="Flannel" %}}

为了让 `flannel` 能正确工作，您必须在运行 `kubeadm init` 时设置 `--Pod-network-cidr=10.244.0.0/16`。

通过运行 `sysctl net.bridge.bridge-nf-call-iptables=1` 将 `/proc/sys/net/bridge/bridge-nf-call-iptables` 设置成 `1`，
进而确保桥接的 IPv4 流量会传递给 iptables。
这是一部分 CNI 插件运行的要求条件，请查看[这篇文档](/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements)获取更详细信息。

注意 `flannel` 适用于 `amd64`、`arm`、`arm64` 和 `ppc64le` 架构平台。

```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

想了解更多关于 `flannel` 的信息,请查阅[ GitHub 上的 CoreOS flannel 仓库](https://github.com/coreos/flannel)。
. /tab %}}

<!--
Set `/proc/sys/net/bridge/bridge-nf-call-iptables` to `1` by running `sysctl net.bridge.bridge-nf-call-iptables=1`
to pass bridged IPv4 traffic to iptables' chains. This is a requirement for some CNI plugins to work, for more information
please see [here](https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements).

Kube-router relies on kube-controller-manager to allocate Pod CIDR for the nodes. Therefore, use `kubeadm init` with the `--Pod-network-cidr` flag.

Kube-router provides Pod networking, network policy, and high-performing IP Virtual Server(IPVS)/Linux Virtual Server(LVS) based service proxy.

For information on setting up Kubernetes cluster with Kube-router using kubeadm, please see official [setup guide](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kubeadm.md).
-->

. tab name="Kube-router" %}}
通过运行 `sysctl net.bridge.bridge-nf-call-iptables=1` 将 `/proc/sys/net/bridge/bridge-nf-call-iptables` 设置成 `1`，
确保桥接的 IPv4 流量会传递给 iptables。
这是一部分 CNI 插件的运行条件。请查看[这篇文档](/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements)了解更详细的信息。

Kube-router 依赖于 kube-controller-manager 来给节点分配 CIDR， 因此需要设置 `kubeadm init` 的 `--Pod-network-cidr` 参数。

Kube-router 提供 Pod 间联网、网络策略和和高效的基于 IPVS/LVS 的服务代理功能。

想了解关于使用 kubeadm 搭建 Kubernetes 和 Kube-router 的更多信息。请查看官方的[安装指引](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kubeadm.md)。
. /tab %}}

<!--
Set `/proc/sys/net/bridge/bridge-nf-call-iptables` to `1` by running `sysctl net.bridge.bridge-nf-call-iptables=1`
to pass bridged IPv4 traffic to iptables' chains. This is a requirement for some CNI plugins to work, for more information
please see [here](https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements).

The official Romana set-up guide is [here](https://github.com/romana/romana/tree/master/containerize#using-kubeadm).

Romana works on `amd64` only.

```shell
kubectl apply -f https://raw.githubusercontent.com/romana/romana/master/containerize/specs/romana-kubeadm.yml
```
-->

. tab name="Romana" %}}
通过运行 `sysctl net.bridge.bridge-nf-call-iptables=1` 将 `/proc/sys/net/bridge/bridge-nf-call-iptables` 设置成 `1`，
确保桥接的 IPv4 流量会传递给 iptables。这是一部分 CNI 插件的运行条件。
请查看[这篇文档](/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements)
获取更详细的信息。

官方的 Romana 安装指引在[这里](https://github.com/romana/romana/tree/master/containerize#using-kubeadm)。

注意，Romana 只适用于 `amd64` 架构。

```shell
kubectl apply -f https://raw.githubusercontent.com/romana/romana/master/containerize/specs/romana-kubeadm.yml
```
. /tab %}}

<!--
Set `/proc/sys/net/bridge/bridge-nf-call-iptables` to `1` by running `sysctl net.bridge.bridge-nf-call-iptables=1`
to pass bridged IPv4 traffic to iptables' chains. This is a requirement for some CNI plugins to work, for more information
please see [here](https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements).

The official Weave Net set-up guide is [here](https://www.weave.works/docs/net/latest/kube-addon/).

Weave Net works on `amd64`, `arm`, `arm64` and `ppc64le` without any extra action required.
Weave Net sets hairpin mode by default. This allows Pods to access themselves via their Service IP address
if they don't know their PodIP.

```shell
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
-->

. tab name="Weave Net" %}}

通过运行 `sysctl net.bridge.bridge-nf-call-iptables=1` 将 `/proc/sys/net/bridge/bridge-nf-call-iptables` 设置成 `1`，
将桥接的 IPv4 流量传递给 iptables。这是一部分 CNI 插件的运行条件。
请查看[这篇文档](/docs/concepts/cluster-administration/network-plugins/#network-plugin-requirements)
获取更详细的信息。

官方的 Weave Net 配置向导在[这里](https://www.weave.works/docs/net/latest/kube-addon/)。

Weave Net 适用于`amd64`、`arm`、`arm64` 和 `ppc64le` 而不需要其它额外的配置。
Weave Net 默认启用 hairpin 模式，可以让 Pod 在不知道他们自己的 PodIP 的时候仍可以使用服务的 IP 地址来访问他们自己。

```shell
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
. /tab %}}

<!--
Provides overlay SDN solution, delivering multicloud networking, hybrid cloud networking,
simultaneous overlay-underlay support, network policy enforcement, network isolation,
service chaining and flexible load balancing.

There are multiple, flexible ways to install JuniperContrail/TungstenFabric CNI.

Kindly refer to this quickstart: [TungstenFabric](https://tungstenfabric.github.io/website/)
-->

. tab name="JuniperContrail/TungstenFabric" %}}
提供了支持 overlay 的 SDN 解决方案，支持多云环境和混合云环境的网络方案，同时支持 overlay 和 underlay、网络策略、
网络隔离、服务链和灵活的负载均衡。

安装 JuniperContrail/TungstenFabric CNI 有很多灵活的方式。

请查阅这个[安装指引](https://tungstenfabric.github.io/website/)。
. /tab %}}
. /tabs >}}

<!-- Once a Pod network has been installed, you can confirm that it is working by
checking that the CoreDNS Pod is Running in the output of `kubectl get Pods --all-namespaces`.
And once the CoreDNS Pod is up and running, you can continue by joining your nodes.

If your network is not working or CoreDNS is not in the Running state, check
out our [troubleshooting docs](/docs/setup/independent/troubleshooting-kubeadm/). -->

一旦 Pod 网络安装完成，您就可以通过 `kubectl get Pods --all-namespaces` 的输出来验证 CoreDNS Pod 是否正常运行。
只要确认了 CoreDNS 正常运行，您就可以向集群中添加节点了。

如果您的网络不能工作或者 CoreDNS 不在运行状态，请查阅[查错方案](/docs/setup/independent/troubleshooting-kubeadm/)。

<!-- ### Master Isolation

By default, your cluster will not schedule Pods on the master for security
reasons. If you want to be able to schedule Pods on the master, e.g. for a
single-machine Kubernetes cluster for development, run:

```bash
kubectl taint nodes --all node-role.kubernetes.io/master-
```

With output looking something like:

```
node "test-01" untainted
taint "node-role.kubernetes.io/master:" not found
taint "node-role.kubernetes.io/master:" not found
```

This will remove the `node-role.kubernetes.io/master` taint from any nodes that
have it, including the master node, meaning that the scheduler will then be able
to schedule Pods everywhere. -->

### 主节点隔离

出于安全原因，默认您的主节点不会被调度运行任何 Pod。
如果您需要在主节点上运行 Pod，比如说部署环境是一个单机器集群，运行：

```bash
kubectl taint nodes --all node-role.kubernetes.io/master-
```

输出类似这样：

```
node "test-01" untainted
taint "node-role.kubernetes.io/master:" not found
taint "node-role.kubernetes.io/master:" not found
```

这个操作会从任何有 `node-role.kubernetes.io/master` 这种标签的节点移除该标签，包括主节点，
标签的移除意味着集群调度器可以将 Pod 调度到任何节点。

<!-- ### Joining your nodes {#join-nodes}

The nodes are where your workloads (containers and Pods, etc) run. To add new nodes to your cluster do the following for each machine:

* SSH to the machine
* Become root (e.g. `sudo su -`)
* Run the command that was output by `kubeadm init`. For example:

``` bash
kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
​``` -->

### 添加节点 {#join-nodes}

节点就是工作负载（容器和 Pod 等）运行的地方。如需向集群添加新节点，可以在每台机器上面执行如下操作：

* SSH 连接到机器上
* 成为 root 用户（比如 `sudo su -`）
* 运行 `kubeadm init` 输出里的命令，即：

​``` bash
kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```
<!--
If you do not have the token, you can get it by running the following command on the master node:

``` bash
kubeadm token list
```

The output is similar to this:

``` console
TOKEN                    TTL  EXPIRES              USAGES           DESCRIPTION            EXTRA GROUPS
8ewj1p.9r9hcjoqgajrj4gi  23h  2018-06-12T02:51:28Z authentication,  The default bootstrap  system:
                                                   signing          token generated by     bootstrappers:
                                                                    'kubeadm init'.        kubeadm:
                                                                                           default-node-token
​``` -->


如果您没有保存令牌的话，可以通过在主节点上执行下面的命令来获取：

​``` bash
kubeadm token list
```

输出类似这样:

``` console
TOKEN                    TTL  EXPIRES              USAGES           DESCRIPTION            EXTRA GROUPS
8ewj1p.9r9hcjoqgajrj4gi  23h  2018-06-12T02:51:28Z authentication,  The default bootstrap  system:
                                                   signing          token generated by     bootstrappers:
                                                                    'kubeadm init'.        kubeadm:
                                                                                           default-node-token
```

<!-- By default, tokens expire after 24 hours. If you are joining a node to the cluster after the current token has expired,
you can create a new token by running the following command on the master node:

``` bash
kubeadm token create
```

The output is similar to this:

``` console
5didvk.d09sbcov8ph2amjw
```

If you don't have the value of `--discovery-token-ca-cert-hash`, you can get it by running the following command chain on the master node:

``` bash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
```

The output is similar to this:

``` console
8cb2de97839780a412b93877f8507ad6c94f73add17d5d7058e91741c9d5ec78
​``` -->

默认情况下，令牌会在 24 小时内过期。如果在令牌过期之后添加节点，您可以在主节点上执行下面的命令创建一个新令牌：

​``` bash
kubeadm token create
```

输出类似这样：

``` console
5didvk.d09sbcov8ph2amjw
```

如果您也不知道这个 `--discovery-token-ca-cert-hash` 的值，您也可以在主节点上运行下面的命令来获取：

``` bash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
```

输出类似这样：

``` console
8cb2de97839780a412b93877f8507ad6c94f73add17d5d7058e91741c9d5ec78
```

<!-- . note >}}
**Note:** To specify an IPv6 tuple for `<master-ip>:<master-port>`, IPv6 address must be enclosed in square brackets, for example: `[fd00::101]:2073`.
. /note >}}

The output should look something like:

```
[preflight] Running pre-flight checks

... (log output of join workflow) ...

Node join complete:
* Certificate signing request sent to master and response
  received.
* Kubelet informed of new secure connection details.

Run 'kubectl get nodes' on the master to see this machine join.
```

A few seconds later, you should notice this node in the output from `kubectl get
nodes` when run on the master. -->

. note >}}
若需为 `<master-ip>:<master-port>` 参数设定一个 IPv6 的元组，地址必须写在一对方括号里面，比如: `[fd00::101]:2073`。
. /note >}}

输出类似这样:

```
[preflight] Running pre-flight checks

... (log output of join workflow) ...

Node join complete:
* Certificate signing request sent to master and response
  received.
* Kubelet informed of new secure connection details.

Run 'kubectl get nodes' on the master to see this machine join.
```

几秒钟之后，您将能在主节点上的 `kubectl get nodes` 的输出里发现新添加的节点。

<!-- ### (Optional) Controlling your cluster from machines other than the master

In order to get a kubectl on some other computer (e.g. laptop) to talk to your
cluster, you need to copy the administrator kubeconfig file from your master
to your workstation like this:

``` bash
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf get nodes
```

. note >}}
**Note:** The example above assumes SSH access is enabled for root. If that is not the
case, you can copy the `admin.conf` file to be accessible by some other user
and `scp` using that other user instead.

The `admin.conf` file gives the user _superuser_ privileges over the cluster.
This file should be used sparingly. For normal users, it's recommended to
generate an unique credential to which you whitelist privileges. You can do
this with the `kubeadm alpha phase kubeconfig user --client-name <CN>`
command. That command will print out a KubeConfig file to STDOUT which you
should save to a file and distribute to your user. After that, whitelist
privileges by using `kubectl create (cluster)rolebinding`.
. /note >}} -->

### (可选) 在非主节点上控制集群

为了能在其他机器（比如，笔记本）上使用 kubectl 来控制您的集群，您可以从主节点上复制管理员的
kubeconfig 到您的机器上，像下面这样操作：

``` bash
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf get nodes
```

. note >}}
上面的例子生效的前提是 SSH 允许 root 用户连接登录。
如果root 用户不能连接的话，您可以将 `admin.conf` 复制到允许其他用户访问的其他地方并将 `scp` 命令里的用户改成相对应的用户再复制。

这个 `admin.conf` 文件给予了用户整个集群的超级用户权限，因此这个操作必须小心谨慎。对于普通用户来说，
更建议创建一个适用于白名单某些权限的验证文件。您可以通过这个命令来生成 `kubeadm alpha phase kubeconfig user --client-name <CN>`。
这个命令会打印 KubeConfig 的内容到标准输出，然后您需要将它保存到一个文件里并分发给您的用户。然后再创建权限的白名单列表，
命令如下： `kubectl create (cluster)rolebinding` 。
. /note >}}

<!-- ### (Optional) Proxying API Server to localhost

If you want to connect to the API Server from outside the cluster you can use
`kubectl proxy`:

```bash
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf proxy
```

You can now access the API Server locally at `http://localhost:8001/api/v1` -->

### (可选) 将 API 服务代理到本地

如果您需要从集群外部连接到您的 API 服务器，请运行`kubectl proxy`:

```bash
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf proxy
```

现在您就可以在本地访问 `http://localhost:8001/api/v1` 来连接 API 服务器了。

<!-- ## Tear down {#tear-down}

To undo what kubeadm did, you should first [drain the
node](/docs/reference/generated/kubectl/kubectl-commands#drain) and make
sure that the node is empty before shutting it down.

Talking to the master with the appropriate credentials, run:

```bash
kubectl drain <node name> --delete-local-data --force --ignore-daemonsets
kubectl delete node <node name>
```

Then, on the node being removed, reset all kubeadm installed state:

```bash
kubeadm reset
```

If you wish to start over simply run `kubeadm init` or `kubeadm join` with the
appropriate arguments.

More options and information about the
[`kubeadm reset command`](/docs/reference/setup-tools/kubeadm/kubeadm-reset/).-->

## 卸载集群 {#tear-down}

想要回退 kubeadm 做出的修改，您需要首先[腾空节点](/docs/reference/generated/kubectl/kubectl-commands#drain)
而且必须确保在关闭节点之前没有任何工作负载在运行。

使用正确的登录凭据来连接到主节点：

```bash
kubectl drain <node name> --delete-local-data --force --ignore-daemonsets
kubectl delete node <node name>
```

然后在待移除的节点上，重置所有 kubeadm 的安装状态：

```bash
kubeadm reset
```

如果您只是想重新运行 `kubeadm init` 或者 `kubeadm join`，[`kubeadm reset`](/docs/reference/setup-tools/kubeadm/kubeadm-reset/)页面有更多的信息可供参考.

<!-- ## Maintaining a cluster {#lifecycle}

Instructions for maintaining kubeadm clusters (e.g. upgrades,downgrades, etc.) can be found [here.](/docs/tasks/administer-cluster/kubeadm)

## Explore other add-ons {#other-addons}

See the [list of add-ons](/docs/concepts/cluster-administration/addons/) to explore other add-ons,
including tools for logging, monitoring, network policy, visualization &amp;
control of your Kubernetes cluster. -->

## 集群维护 {#lifecycle}

维护集群（比如升级，降级）的详细指令，可参考[这里](/docs/tasks/administer-cluster/kubeadm)。

## 探索其他插件 {#other-addons}

查看[插件列表](/docs/concepts/cluster-administration/addons/)来发现其他插件，包括日志、监控、网络策略、
可视化和集群管理工具等等。

<!-- ## What's next {#whats-next}

* Verify that your cluster is running properly with [Sonobuoy](https://github.com/heptio/sonobuoy)
* Learn about kubeadm's advanced usage in the [kubeadm reference documentation](/docs/reference/setup-tools/kubeadm/kubeadm)
* Learn more about Kubernetes [concepts](/docs/concepts/) and [`kubectl`](/docs/user-guide/kubectl-overview/).
* Configure log rotation. You can use **logrotate** for that. When using Docker, you can specify log rotation options for Docker daemon, for example `--log-driver=json-file --log-opt=max-size=10m --log-opt=max-file=5`. See [Configure and troubleshoot the Docker daemon](https://docs.docker.com/engine/admin/) for more details.

## Feedback {#feedback}

* For bugs, visit [kubeadm GitHub issue tracker](https://github.com/kubernetes/kubeadm/issues)
* For support, visit kubeadm Slack Channel:
  [#kubeadm](https://kubernetes.slack.com/messages/kubeadm/)
* General SIG Cluster Lifecycle Development Slack Channel:
  [#sig-cluster-lifecycle](https://kubernetes.slack.com/messages/sig-cluster-lifecycle/)
* SIG Cluster Lifecycle [SIG information](#TODO)
* SIG Cluster Lifecycle Mailing List:
  [kubernetes-sig-cluster-lifecycle](https://groups.google.com/forum/#!forum/kubernetes-sig-cluster-lifecycle) -->

## 后续 {#whats-next}

* 使用 [Sonobuoy](https://github.com/heptio/sonobuoy) 验证集群是否正确运行
* 阅读[kubeadm 参考文档](/docs/reference/setup-tools/kubeadm/kubeadm)，学习 kubeadm 的高级应用
* 进一步了解 Kubernetes 的[概念](/docs/concepts/)和 [`kubectl`](/docs/user-guide/kubectl-overview/)
* 您可以使用 **logrotate** 配置日志轮转。使用 Docker 的时候，您可以给 Docker 守护进程设置日志轮转的选项，
比如 `--log-driver=json-file --log-opt=max-size=10m --log-opt=max-file=5`。请查阅 [Docker 守护进程的配置和纠错](https://docs.docker.com/engine/admin/)。

## 反馈 {#feedback}

* 如果发现故障，请访问 [kubeadm GitHub issue tracker](https://github.com/kubernetes/kubeadm/issues)
* 如果需要支持，请访问 kubeadm 的 Slack 频道：[#kubeadm](https://kubernetes.slack.com/messages/kubeadm/)
* 访问 SIG cluster-lifecycle 开发者所使用的 Slack 频道：
  [#sig-cluster-lifecycle](https://kubernetes.slack.com/messages/sig-cluster-lifecycle/)
* SIG cluster-lifecycle 的 [SIG 信息](#TODO)
* SIG cluster-lifecycle 的邮件列表:
  [kubernetes-sig-cluster-lifecycle](https://groups.google.com/forum/#!forum/kubernetes-sig-cluster-lifecycle)

<!-- ## Version skew policy {#version-skew-policy}

The kubeadm CLI tool of version vX.Y may deploy clusters with a control plane of version vX.Y or vX.(Y-1).
kubeadm CLI vX.Y can also upgrade an existing kubeadm-created cluster of version vX.(Y-1).

Due to that we can't see into the future, kubeadm CLI vX.Y may or may not be able to deploy vX.(Y+1) clusters.

Example: kubeadm v1.8 can deploy both v1.7 and v1.8 clusters and upgrade v1.7 kubeadm-created clusters to
v1.8.

Please also check our [installation guide](/docs/setup/independent/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)
for more information on the version skew between kubelets and the control plane. -->

## 版本偏差策略 {#version-skew-policy}

vX.Y 版本的 kubeadm 命令行工具可能会部署一个控制面版本为 vX.Y 或者 vX.(Y-1) 的集群，也可以用于升级一个 vX.(Y-1) 的由 kubeadm 创建的集群。
因为我们无法预见未来，版本为 vX.Y 的 kubeadm 可能可以也可能无法用于部署 vX.(Y+1) 版本的集群。
例子: kubeadm v1.8 可以用于部署 v1.7 和 v1.8 的集群，也可以升级 v1.7 的由 kubeadm 创建的集群到 1.8 版本。
请查看我们的[安装向导](/docs/setup/independent/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)，其中提供了关于 kubelet 和控制面版本偏差的更多信息。

<!-- ## kubeadm works on multiple platforms {#multi-platform}

kubeadm deb/rpm packages and binaries are built for amd64, arm (32-bit), arm64, ppc64le, and s390x
following the [multi-platform
proposal](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/multi-platform.md).

Only some of the network providers offer solutions for all platforms. Please consult the list of
network providers above or the documentation from each provider to figure out whether the provider
supports your chosen platform. -->

## 跨多平台上使用 kubeadm {#multi-platform}

kubeadm 的 deb/rpm 包和可执行文件都是适用于 amd64、arm (32位)、arm64、ppc64le 和 s390x等架构平台的，
请查阅[多平台方案](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/multi-platform.md)。

只有一部分的网络驱动提供了所有平台的网络解决方案，请查询上面的网络驱动列表或者对应的官方文档来确定是否支持您的平台。

<!-- ## Limitations {#limitations}

Please note: kubeadm is a work in progress and these limitations will be
addressed in due course.

1. The cluster created here has a single master, with a single etcd database
   running on it. This means that if the master fails, your cluster may lose
   data and may need to be recreated from scratch. Adding HA support
   (multiple etcd servers, multiple API servers, etc) to kubeadm is
   still a work-in-progress.

   Workaround: regularly
   [back up etcd](https://coreos.com/etcd/docs/latest/admin_guide.html). The
   etcd data directory configured by kubeadm is at `/var/lib/etcd` on the master.

## Troubleshooting {#troubleshooting}

If you are running into difficulties with kubeadm, please consult our [troubleshooting docs](/docs/setup/independent/troubleshooting-kubeadm/). -->

## 局限 {#limitations}

请注意，kubeadm 还处于正在开发的状态中，这些局限将会在适当的时间修正。

1. 这篇文档介绍的创建的集群只能有单一的主节点和单一的 etcd 数据库，这表示如果主节点宕机，您的集群将会丢失数据
   而且可能无法重新创建。目前给 kubeadm 添加高可用支持（比如多 etcd 多 API服务等等）的功能还在开发当中，因此可先参照下面的
   临时解决方案: 经常性地[备份 etcd](https://coreos.com/etcd/docs/latest/admin_guide.html)。
   由 kubeadm 配置的 etcd 数据位于主节点上的 `/var/lib/etcd` 目录。

## 查错 {#troubleshooting}

如果您在使用 kubeadm 发现任何问题，请查阅我们的[纠错文档](/docs/setup/independent/troubleshooting-kubeadm/)。





