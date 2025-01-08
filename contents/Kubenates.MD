# [官网](https://kubernetes.io/zh-cn/docs/concepts/architecture/#container-resource-monitoring)

# 架构

<img src="https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg" alt="img" style="zoom:70%;"/>

# 核心组件

### 1. 控制平台组件

`管理集群的整体状态`

- [kube-apiserver](https://kubernetes.io/zh-cn/docs/concepts/architecture/#kube-apiserver)：公开 Kubernetes HTTP API 的核心组件服务器
- [etcd](https://kubernetes.io/zh-cn/docs/concepts/architecture/#etcd)：具备一致性和高可用性的键值存储，用于所有 API 服务器的数据存储
- [kube-scheduler](https://kubernetes.io/zh-cn/docs/concepts/architecture/#kube-scheduler)：查找尚未绑定到节点的 Pod，并将每个 Pod 分配给合适的节点。
- [kube-controller-manager](https://kubernetes.io/zh-cn/docs/concepts/architecture/#kube-controller-manager)：运行[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/)来实现 Kubernetes API 行为。

### 2. Node 组件

`在每个节点上运行，维护运行的 Pod 并提供 Kubernetes 运行时环境`

- [kubelet](https://kubernetes.io/zh-cn/docs/concepts/architecture/#kubelet)：确保 Pod 及其容器正常运行。
- [kube-proxy](https://kubernetes.io/zh-cn/docs/concepts/architecture/#kube-proxy)：维护节点上的网络规则以实现 Service 的功能。

常用网络插件及作用

