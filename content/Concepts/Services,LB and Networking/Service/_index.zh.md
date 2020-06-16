---
date: 2016-04-09T16:50:16+02:00
title: Service
---



```yaml
apiVersion: v1             
kind: Service              # string     Required    资源类型
metadata:                  # Object     Required    元数据
  name: string             # string     Required    Service名称
  namespace: string        # string     Required    命名空间，默认default
  labels:                  # list       Required    自定义标签属性列表
    - name: string
  annotations:             # list       Required    自定义注解属性列表
    - name: string
spec:                      # Object     Required    详细描述
  selector: []             # list       Required    Label Selector配置，将选择具有指定Label标签的Pod作为管理范围
  type: string             # string     Required    Service的类型，指定Service的访问方式，默认为ClusterIP
                           # ClusterIP：虚拟的服务IP地址，改地址用于Kubernetes集群内部的Pod访问，在Node上kube-proxy通过设置iptables规则进行转发
                           # NodePort:  使用宿主机的端口，使能够访问个Node的外部客户端通过Node的IP地址和端口号就能访问服务
                           # LoadBalancer: 使用外接负载均衡完成到服务的负载分发，需要在spec.status.loadBalancer字段指定外部负载均衡器的IP地址，
                           #               并同时定义nodePort和clusterIP，用于公有云环境
  clusterIP: string        # string     虚拟服务IP地址，当type=ClusterIP时，如果不指定，则系统自动分配，也可以手动指定；当type=LoaderBalancer需要指定
  sessionAffinity: string  # string     是否支持Session，可选值为ClientIP，默认为空
                           # ClientIP:  表示将同一个客户端（根据IP地址决定）的访问请求都转发到同一个后端Pod
  ports:                   # list       需要暴露的端口列表
    - name: string         # 端口名称
      protocol: string     # 端口协议    支持tcp和udp，默认为tcp
      port: int            # 服务监听的端口号
      targetPort: int      # 需要转发到后端Pod的端口号
      nodePort: int        # 当spec.type=NodePort时，指定映射到物理机的端口号
  status:                  # 当spec.type=LoadBalancer时，设置外部负载均衡器的地址，用于公有云环境
    loadBalancer:          # 外部负载均衡器
      ingress:             # 外部负载均衡器
        ip: string         # 外部负载均衡器的IP地址
        hostname: string   # 外部负载均衡器的主机名
```



### 1. 为什么要使用`IPVS`

从`k8s`的1.8版本开始，`kube-proxy`引入了`IPVS`模式，`IPVS`模式与`iptables`同样基于`Netfilter`，但是采用的`hash`表，因此当`service`数量达到一定规模时，hash查表的速度优势就会显现出来，从而提高`service`的服务性能。



