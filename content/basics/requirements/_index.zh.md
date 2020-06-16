---
title: Kubernetes概念与术语
weight: 10
# disableToc: true
---

## Kubernetes概念与术语

## 组件

* etcd

* Master

  * kube-apiserver
  * kube-controller-manager
  * kube-scheduler

* Node

  * kubelet
  * kube-proxy
  * docker

* 附加组件

  * DNS

    * kube-dns
    * coredns

  * Ingress Controller

    * traefik
    * NGINX Ingress Controller 官方维护
    * Nginx Plus
    * HAProxy Ingress
    * AppsCode Voyager
    * contour
    * Cloudflare Warp Ingress
    * F5 Big IP Controller
    * Gloo
    * gRPC Load balancing
    * Kong
    * nghttpx Ingress Controller
    * Ambassador
    * Skipper

   * Heapster

   * Dashboard

   * Kubernator

   * Federation

   * eventrouter

      

## 资源对象

* Pod

  * Init Container
  * Pod Security Policy
  * Pod Lifecycle
  * Pod Hook
  * Pod Preset
  * Disruption
  * Resource Quota
  * liveness和readiness

 * 集群配置

   * Node
   * Namespace
   * Label
   * Annotation
   * Taint 和 Toleration
   * 亲和性（Affinity）和反亲和性（anti-affinity）
   * Garbage Collection

* 控制器
      * Deployment
          * DaemonSet
          * StatefulSet
          * ReplicaSet
          * Job
          * CronJob
          * Horizontal Pod Autoscaling
          * cron-hpa-controller
          * Escalator

* 服务发现

  * Service
    * 服务暴露
      * ClusterIP
      * NodePort
      * LoadBalance
  * Ingress

* 身份与权限控制

  * Service Account
  * Network Policy
    * kubernetes-network-policy-recipes
  * Security Context
  * RBAC

* 存储配置

  * Secret
  * ConfigMap
  * Volume
  * Persistent Volume
  * Local Volume
  * Storage Class
  * Stork

* API 扩展

  * CustomResourceDefinition
  * Operator
    * awesome-operators
    * Prometheus
      * ops-kube-alerting-rules-operator
    * Confluent Operator
    * Kong API
    * Kubernetes Operators
    * K8s Operator Workshop
    * Cert Operator
    * Cert manager
    * Operator Kit
    * Container Linux Update Operator
    * DB Operator
    * etcd
    * Elasticsearch
    * Memcached
    * MongoDB
    * MySQL Operator 
    * PostgreSQL
    * Another PostgreSQL
    * Kafka
    * Envoy Operator 
    * rbac-manager
    * Akrobateo
  * Aggregated API Server
  * custom-metrics-apiserver-ingress-nginx
  * metacontroller

  ## 部署配置

* 单机部署

  * minikube
  * kubeasz
  * Sealos

* 集群部署

  * Sealos
  * Breeze
  * kubeadm
  * Kubespray
  * LinuxKit
  * kubeasz

* 部署 Windows 节点

* Kubernetes on Azure

* 插件扩展

  * CNI
    * Flannel
    * Weave Net
    * Contiv
    * Calico
    * OVN
    * SR-IOV
    * Romana
    * OpenContrail
    * Canal
    * kuryr-kubernetes
    * Cilium
    * CNI-Genie
    * Kube-router
    * Nuage
    * Multus-cni
    * Virtlet
  * CSI
    * Ceph
  * CRI
    * Docker
    * HyperContainer
    * Runc
      * cri-containerd
      * cri-o
    * gVisor
    * Rkt
    * Mirantis
    * Infranetes
  * Scheduler 扩展
    * Sticky Node Scheduler
    * ksched
    * kube-node-index-prioritizing-scheduler
  * Device 插件
  * keepalived-vip
  * External DNS
  * kubevirt

* 服务治理

  * Helm
    * Helmfile
  * Service mesh
    * Istio
      * Envoy
        * 文档
          * LearnEnvoy
          * Envoy 官方文档中文版
          * Envoy 官方文档
        * 大牛
      * 工具
        * Fortio
        * outlier-istio
    * Linkerd
    * Conduit
  * 持续集成
    * Jenkins
    * Drone
    * Apollo
  * CI/CD
    * Skaffold
    * Jenkins X
    * Spinnaker
    * Kubernetes Pipeliner
    * Draft
    * Forge
    * Flux
    * GitKube
    * KubeCI
    * Keel
    * Brigade
  * Kompose
  * 灰度发布
    * Shipper