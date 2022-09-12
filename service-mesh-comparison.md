# （翻译）服务网格比较

原文：[Service Mesh Comparison](https://servicemesh.es/)

## 什么是服务网格？

服务网格是一个专用的基础设施层，它为服务之间的网络添加功能。 它允许控制流量并获得整个系统的洞察力。 可观察性、流量转移（如金丝雀发布）、弹性功能（例如断路器，请求重试和超时设置）和自动双向 TLS (mutual TLS) 可以配置一次并以分散的方式强制执行。 与类似功能的类库 (libraries) 相比，服务网格不需要更改代码。 相反，它添加了一层额外的容器，这些容器可靠地实现了这些功能，并且与技术或编程语言无关。

## 服务网格框架

![](./img/service_mesh_architecture.png)

### 没有使用服务网格 (上图左边部分)，

... 每个微服务必须自己实现业务逻辑和[横切关注点](https://en.wikipedia.org/wiki/Cross-cutting_concern)（CCC）。

### 使用了服务网格 (上图右边部分)，

... 流量指标、路由和加密等许多 CCC 从微服务中被移出并进入代理（数据平面，Data Plane）。业务逻辑和业务指标依然保留在微服务中。 进出的请求由代理透明地进行路由。 除了一层代理（数据平面）之外，服务网格还添加了一个所谓的控制平面 (Control Plane)。它将配置更新分发给所有代理并接收由代理收集的指标以进行后续处理，例如通过 Prometheus 等监控基础设施对指标数据进一步处理。

## 谁需要服务网格？

服务网格的价值随着应用所包含的服务数量而增长。从逻辑上讲，微服务架构是服务网格最常见的用例。然而，具体的交互可能与服务网格如何提高服务的控制、可靠性、安全性和可观察性更相关。即使是单体应用（monolith）也可以从服务网格中受益，而某些具体的微服务应用可能不会。

## 服务网格实现

### [Istio](https://istio.io/)

如果您听说过服务网格，那么您可能也听说过 Istio。Istio 是迄今为止最受欢迎的服务网格，因为它具有丰富的功能集以及 Google 和 IBM 的支持。

### [Linkerd](https://linkerd.io/)

Linkerd 是第一个服务网格。 最新版本 2.x 版本致力于简单、性能和构建在 Kubernetes 之上作为底层平台。

### [Consul](https://www.consul.io/)

HashiCorp Consul 长期以来一直以服务发现解决方案而闻名。 现在它采用了 Envoy 代理和 Sidecar 模式，Consul 可以作为服务网格服务于各种平台，如 Kubernetes 和 VM。

### [AWS App Mesh](https://aws.amazon.com/app-mesh/)

在服务网格大肆宣传后不久，AWS 为 AWS 上的应用添加了自己的服务网格。

### [Traefik Mesh](https://traefik.io/traefik-mesh/)

顾名思义，Traefik Mesh（以前称为 Maesh）是基于云原生 API 网关 Traefik 的服务网格。

### [Kuma](https://kuma.io/)

Kuma 是使用 Envoy 和由 Kong API 网关的开发人员开发的 sidecar 模式的服务网格。 它专注于多云，可以运行非 Kubernetes 工作负载。

### [Open Service Mesh (OSM)](https://openservicemesh.io/)

OSM 是由 Microsoft 最新实现的服务网格，它遵循常见的服务网格设计原则，例如采用 Envoy 代理，实现 SMI 规范。

### [Cilium](https://cilium.io/)

Cilium 早在 Service Mesh 一词出现之前就已经存在，它通过 eBPF 而不是 sidecars 有效地提供了安全、网络和可观察性功能。

## 服务网格接口 (SMI)

[SMI](https://smi-spec.io/) 不是服务网格，而是服务网格功能的 API 规范。 它由 Microsoft、Buoyant（开发 Linkerd）和 HashiCorp（开发 Consul）构建，旨在为如何配置服务网格功能创建一个通用标准，而不管底层服务网格实现如何。该服务网格接口规范允许基于服务网格功能的工具（例如仪表板、金丝雀发布等自动化工具....）的与实现无关的开发。当针对 SMI 开发时，工具将与任何符合 SMI 的服务网格相兼容。服务网格用户还可以从无需修改配置即可更换其服务网格实现的能力中受益。

## 如何选择服务网格实现

虽然服务网格对代码没有影响，但它们会改变操作过程并需要熟悉新的概念和技术。所以，尤其是在服务网格接口（SMI）得到广泛支持之前 —— 采用服务网格实现是一个长期的决定。因此，应事先仔细比较和测试这些服务网格实现。一开始，选择具有最多功能的、最灵活的服务网格似乎是合乎逻辑的。但情况可能恰恰相反，因为功能和灵活性通常需要付出认知和技术复杂性的代价。

评估的目的是找出哪些功能对您很重要，以及您如何从中受益。由于服务网格会影响延迟和资源消耗，因此也必须衡量这些缺点。

我们建议在您的决策过程中包括以下步骤：

- 作为一个团队，确定什么是服务网格要解决的最重要的问题。请记住，在某些情况下，类库或对现有架构的调整可能是不错的选择（见下文）。

- 讨论您对简单性/可用性、性能和兼容性的要求。

- 根据您的功能和非功能需求选择您的最合适的两三个服务网格实现。下表应该对您有所帮助。

- 通过学习服务网格提供的各自的教程（下面的链接）来试着使用服务网格实现，并可能放弃部分候选者来减少选择范围。

- 测试您的单个应用的延迟和资源开销。对于每个候选服务网格实现，设置相同的测试环境并安装服务网格。设置一个额外的无服务网格的环境作为对比环境。在所有环境中安装您的应用。使用 [Locust](https://locust.io/) 或 [Fortio](https://fortio.org/) 等工具执行负载测试并测量请求延迟、CPU 和内存消耗。

## 服务网格对比

|   | Istio | Linkerd | AWS App Mesh | Consul | Traefik Mesh (formerly Maesh) | Kuma | Open Service Mesh (OSM) | Cilium |
|----|----|----|----|----|----|----|----|----|
| 当前版本 | 1.13 | 2.11 | | 1.13 | 1.4 | 1.7 | 1.0 | 1.12 |
| 许可 | Apache License 2.0 | Apache License 2.0 | Closed Source | Mozilla License | Apache License 2.0 | Apache License 2.0 | Apache License 2.0 | Apache License 2.0 |