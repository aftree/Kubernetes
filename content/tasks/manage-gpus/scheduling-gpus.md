---
reviewers:
- vishh
content_type: concept
title: 😝 - 调度 GPUs
---
<!--
---
reviewers:
- vishh
content_type: concept
title: Schedule GPUs
---
--->

<!-- overview -->

<!--
Kubernetes includes **experimental** support for managing AMD and NVIDIA GPUs spread
across nodes. The support for NVIDIA GPUs was added in v1.6 and has gone through
multiple backwards incompatible iterations.  The support for AMD GPUs was added in
v1.9 via [device plugin](#deploying-amd-gpu-device-plugin).

This page describes how users can consume GPUs across different Kubernetes versions
and the current limitations.
--->
Kubernetes 支持对节点上的 AMD 和 NVIDA GPU 进行管理，目前处于**实验**状态。对 NVIDIA GPU 的支持在 v1.6 中加入，已经经历了多次不向后兼容的迭代。而对 AMD GPU 的支持则在 v1.9 中通过 [设备插件](#deploying-amd-gpu-device-plugin) 加入。

这个页面介绍了用户如何在不同的 Kubernetes 版本中使用 GPU，以及当前存在的一些限制。




<!-- body -->

<!--
## v1.8 onwards

**From 1.8 onwards, the recommended way to consume GPUs is to use [device
plugins](/docs/concepts/cluster-administration/device-plugins).**

To enable GPU support through device plugins before 1.10, the `DevicePlugins`
feature gate has to be explicitly set to true across the system:
`--feature-gates="DevicePlugins=true"`. This is no longer required starting
from 1.10.
--->
## 从 v1.8 起

**从 1.8 版本开始，我们推荐通过 [设备插件](/docs/concepts/cluster-administration/device-plugins) 的方式来使用 GPU。**

在 1.10 版本之前，为了通过设备插件开启 GPU 的支持，我们需要在系统中将 `DevicePlugins` 这一特性开关显式地设置为 true：`--feature-gates="DevicePlugins=true"`。不过，
从 1.10 版本开始，我们就不需要这一步骤了。

<!--
Then you have to install GPU drivers from the corresponding vendor on the nodes
and run the corresponding device plugin from the GPU vendor
([AMD](#deploying-amd-gpu-device-plugin), [NVIDIA](#deploying-nvidia-gpu-device-plugin)).

When the above conditions are true, Kubernetes will expose `nvidia.com/gpu` or
`amd.com/gpu` as a schedulable resource.
--->
接着你需要在主机节点上安装对应厂商的 GPU 驱动并运行对应厂商的设备插件 ([AMD](#deploying-amd-gpu-device-plugin)、[NVIDIA](#deploying-nvidia-gpu-device-plugin))。

当上面的条件都满足，Kubernetes 将会暴露 `nvidia.com/gpu` 或 `amd.com/gpu` 来作为
一种可调度的资源。

<!--
You can consume these GPUs from your containers by requesting
`<vendor>.com/gpu` just like you request `cpu` or `memory`.
However, there are some limitations in how you specify the resource requirements
when using GPUs:
--->
你也能通过像请求 `cpu` 或 `memory` 一样请求 `<vendor>.com/gpu` 来在容器中使用 GPU。然而，当你要通过指定资源请求来使用 GPU 时，存在着以下几点限制：

<!--
- GPUs are only supposed to be specified in the `limits` section, which means:
  * You can specify GPU `limits` without specifying `requests` because
    Kubernetes will use the limit as the request value by default.
  * You can specify GPU in both `limits` and `requests` but these two values
    must be equal.
  * You cannot specify GPU `requests` without specifying `limits`.
- Containers (and pods) do not share GPUs. There's no overcommitting of GPUs.
- Each container can request one or more GPUs. It is not possible to request a
  fraction of a GPU.

Here's an example:
--->
- GPU 仅仅支持在 `limits` 部分被指定，这表明：
  * 你可以仅仅指定 GPU 的 `limits` 字段而不必须指定 `requests` 字段，因为 Kubernetes 会默认使用 limit 字段的值来作为 request 字段的默认值。
  * 你能同时指定 GPU 的 `limits` 和 `requests` 字段，但这两个值必须相等。
  * 你不能仅仅指定 GPU 的 `request` 字段而不指定 `limits`。
- 容器（以及 pod）并不会共享 GPU，也不存在对 GPU 的过量使用。
- 每一个容器能够请求一个或多个 GPU。然而只请求一个 GPU 的一部分是不允许的。

下面是一个例子:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-vector-add
spec:
  restartPolicy: OnFailure
  containers:
    - name: cuda-vector-add
      # https://github.com/kubernetes/kubernetes/blob/v1.7.11/test/images/nvidia-cuda/Dockerfile
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```

<!--
### Deploying AMD GPU device plugin

The [official AMD GPU device plugin](https://github.com/RadeonOpenCompute/k8s-device-plugin)
has the following requirements:
--->
### 部署 AMD GPU 设备插件

[官方的 AMD GPU 设备插件](https://github.com/RadeonOpenCompute/k8s-device-plugin) 有以下要求：

<!--
- Kubernetes nodes have to be pre-installed with AMD GPU Linux driver.

To deploy the AMD device plugin once your cluster is running and the above
requirements are satisfied:
```
# For Kubernetes v1.9
kubectl create -f https://raw.githubusercontent.com/RadeonOpenCompute/k8s-device-plugin/r1.9/k8s-ds-amdgpu-dp.yaml

# For Kubernetes v1.10
kubectl create -f https://raw.githubusercontent.com/RadeonOpenCompute/k8s-device-plugin/r1.10/k8s-ds-amdgpu-dp.yaml
```
--->
- Kubernetes 节点必须预先安装 AMD GPU 的 Linux 驱动。

如果你的集群已经启动并且满足上述要求的话，可以这样部署 AMD 设备插件：
```
# 针对 Kubernetes v1.9
kubectl create -f https://raw.githubusercontent.com/RadeonOpenCompute/k8s-device-plugin/r1.9/k8s-ds-amdgpu-dp.yaml

# 针对 Kubernetes v1.10
kubectl create -f https://raw.githubusercontent.com/RadeonOpenCompute/k8s-device-plugin/r1.10/k8s-ds-amdgpu-dp.yaml
```

<!--
Report issues with this device plugin to [RadeonOpenCompute/k8s-device-plugin](https://github.com/RadeonOpenCompute/k8s-device-plugin).
--->
请到 [RadeonOpenCompute/k8s-device-plugin](https://github.com/RadeonOpenCompute/k8s-device-plugin) 报告有关此设备插件的问题。

<!--
### Deploying NVIDIA GPU device plugin

There are currently two device plugin implementations for NVIDIA GPUs:

#### Official NVIDIA GPU device plugin

The [official NVIDIA GPU device plugin](https://github.com/NVIDIA/k8s-device-plugin)
has the following requirements:
--->
### 部署 NVIDIA GPU 设备插件

对于 NVIDIA GPUs，目前存在两种设备插件的实现：

#### 官方的 NVIDIA GPU 设备插件

[官方的 NVIDIA GPU 设备插件](https://github.com/NVIDIA/k8s-device-plugin) 有以下要求:

<!--
- Kubernetes nodes have to be pre-installed with NVIDIA drivers.
- Kubernetes nodes have to be pre-installed with [nvidia-docker 2.0](https://github.com/NVIDIA/nvidia-docker)
- nvidia-container-runtime must be configured as the [default runtime](https://github.com/NVIDIA/k8s-device-plugin#preparing-your-gpu-nodes)
  for docker instead of runc.
- NVIDIA drivers ~= 361.93

To deploy the NVIDIA device plugin once your cluster is running and the above
requirements are satisfied:
--->
- Kubernetes 的节点必须预先安装了 NVIDIA 驱动
- Kubernetes 的节点必须预先安装 [nvidia-docker 2.0](https://github.com/NVIDIA/nvidia-docker)
- Docker 的[默认运行时](https://github.com/NVIDIA/k8s-device-plugin#preparing-your-gpu-nodes)必须设置为 nvidia-container-runtime，而不是 runc
- NVIDIA 驱动版本 ~= 361.93

如果你的集群已经启动并且满足上述要求的话，可以这样部署 NVIDIA 设备插件：

<!--
```
# For Kubernetes v1.8
kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v1.8/nvidia-device-plugin.yml

# For Kubernetes v1.9
kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v1.9/nvidia-device-plugin.yml
```

Report issues with this device plugin to [NVIDIA/k8s-device-plugin](https://github.com/NVIDIA/k8s-device-plugin).
--->
```
# 针对 Kubernetes v1.8
kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v1.8/nvidia-device-plugin.yml

# 针对 Kubernetes v1.9
kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v1.9/nvidia-device-plugin.yml
```

请到 [NVIDIA/k8s-device-plugin](https://github.com/NVIDIA/k8s-device-plugin) 报告有关此设备插件的问题。

<!--
#### NVIDIA GPU device plugin used by GCE

The [NVIDIA GPU device plugin used by GCE](https://github.com/GoogleCloudPlatform/container-engine-accelerators/tree/master/cmd/nvidia_gpu)
doesn't require using nvidia-docker and should work with any container runtime
that is compatible with the Kubernetes Container Runtime Interface (CRI). It's tested
on [Container-Optimized OS](https://cloud.google.com/container-optimized-os/)
and has experimental code for Ubuntu from 1.9 onwards.
--->
#### GCE 中使用的 NVIDIA GPU 设备插件

[GCE 使用的 NVIDIA GPU 设备插件](https://github.com/GoogleCloudPlatform/container-engine-accelerators/tree/master/cmd/nvidia_gpu) 并不要求使用 nvidia-docker，并且对于任何实现了 Kubernetes CRI 的容器运行时，都应该能够使用。这一实现已经在 [Container-Optimized OS](https://cloud.google.com/container-optimized-os/) 上进行了测试，并且在 1.9 版本之后会有对于 Ubuntu 的实验性代码。

<!--
On your 1.12 cluster, you can use the following commands to install the NVIDIA drivers and device plugin:

```
# Install NVIDIA drivers on Container-Optimized OS:
kubectl create -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/stable/daemonset.yaml

# Install NVIDIA drivers on Ubuntu (experimental):
kubectl create -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/stable/nvidia-driver-installer/ubuntu/daemonset.yaml

# Install the device plugin:
kubectl create -f https://raw.githubusercontent.com/kubernetes/kubernetes/release-1.12/cluster/addons/device-plugins/nvidia-gpu/daemonset.yaml
```
--->
在你 1.12 版本的集群上，你能使用下面的命令来安装 NVIDIA 驱动以及设备插件：

```
# 在容器优化的操作系统上安装 NVIDIA 驱动:
kubectl create -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/stable/daemonset.yaml

# 在 Ubuntu 上安装 NVIDIA 驱动 (实验性质):
kubectl create -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/stable/nvidia-driver-installer/ubuntu/daemonset.yaml

# 安装设备插件:
kubectl create -f https://raw.githubusercontent.com/kubernetes/kubernetes/release-1.12/cluster/addons/device-plugins/nvidia-gpu/daemonset.yaml
```

<!--
Report issues with this device plugin and installation method to [GoogleCloudPlatform/container-engine-accelerators](https://github.com/GoogleCloudPlatform/container-engine-accelerators).

Instructions for using NVIDIA GPUs on GKE are
[here](https://cloud.google.com/kubernetes-engine/docs/how-to/gpus)
--->
请到 [GoogleCloudPlatform/container-engine-accelerators](https://github.com/GoogleCloudPlatform/container-engine-accelerators) 报告有关此设备插件以及安装方法的问题。

<!--
## Clusters containing different types of GPUs

If different nodes in your cluster have different types of GPUs, then you
can use [Node Labels and Node Selectors](/docs/tasks/configure-pod-container/assign-pods-nodes/)
to schedule pods to appropriate nodes.

For example:
--->
## 集群内存在不同类型的 NVIDIA GPU

如果集群内部的不同节点上有不同类型的 NVIDIA GPU，那么你可以使用 [节点标签和节点选择器](/docs/tasks/configure-pod-container/assign-pods-nodes/) 来将 pod 调度到合适的节点上。

例如：

<!--
```shell
# Label your nodes with the accelerator type they have.
kubectl label nodes <node-with-k80> accelerator=nvidia-tesla-k80
kubectl label nodes <node-with-p100> accelerator=nvidia-tesla-p100
```
--->
```shell
# 为你的节点加上它们所拥有的加速器类型的标签
kubectl label nodes <node-with-k80> accelerator=nvidia-tesla-k80
kubectl label nodes <node-with-p100> accelerator=nvidia-tesla-p100
```

<!--
For AMD GPUs, you can deploy [Node Labeller](https://github.com/RadeonOpenCompute/k8s-device-plugin/tree/master/cmd/k8s-node-labeller), which automatically labels your nodes with GPU properties. Currently supported properties:
--->
对于 AMD GPUs，您可以部署 [节点标签器](https://github.com/RadeonOpenCompute/k8s-device-plugin/tree/master/cmd/k8s-node-labeller)，它会自动给节点打上 GPU 属性标签。目前支持的属性：

<!--
* Device ID (-device-id)
* VRAM Size (-vram)
* Number of SIMD (-simd-count)
* Number of Compute Unit (-cu-count)
* Firmware and Feature Versions (-firmware)
* GPU Family, in two letters acronym (-family)
  * SI - Southern Islands
  * CI - Sea Islands
  * KV - Kaveri
  * VI - Volcanic Islands
  * CZ - Carrizo
  * AI - Arctic Islands
  * RV - Raven

Example result:
--->
* 设备 ID (-device-id)
* VRAM 大小 (-vram)
* SIMD 数量(-simd-count)
* 计算单位数量(-cu-count)
* 固件和特性版本 (-firmware)
* GPU 系列，两个字母的首字母缩写(-family)
  * SI - Southern Islands
  * CI - Sea Islands
  * KV - Kaveri
  * VI - Volcanic Islands
  * CZ - Carrizo
  * AI - Arctic Islands
  * RV - Raven

示例:

    $ kubectl describe node cluster-node-23
    Name:               cluster-node-23
    Roles:              <none>
    Labels:             beta.amd.com/gpu.cu-count.64=1
                        beta.amd.com/gpu.device-id.6860=1
                        beta.amd.com/gpu.family.AI=1
                        beta.amd.com/gpu.simd-count.256=1
                        beta.amd.com/gpu.vram.16G=1
                        beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/os=linux
                        kubernetes.io/hostname=cluster-node-23
    Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                        node.alpha.kubernetes.io/ttl: 0
    ......

<!--
Specify the GPU type in the pod spec:
--->
在 pod 的 spec 字段中指定 GPU 的类型：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-vector-add
spec:
  restartPolicy: OnFailure
  containers:
    - name: cuda-vector-add
      # https://github.com/kubernetes/kubernetes/blob/v1.7.11/test/images/nvidia-cuda/Dockerfile
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100 # or nvidia-tesla-k80 etc.
```

<!--
This will ensure that the pod will be scheduled to a node that has the GPU type
you specified.
--->
这能够保证 pod 能够被调度到你所指定类型的 GPU 的节点上去。
