# **Serverless**

## Istio

```markdown
**Kubernetes vs Service Mesh**
Kubernetes与ServiceMesh中的的服务访问关系(每个pod一个sidecar的模式)
![compare1](https://www.servicemesher.com/istio-handbook/images/kubernetes-vs-service-mesh.png)

控制平面的特点：
* 不直接解析数据包
* 与控制平面中的代理通信，下发策略和配置
* 负责网络行为的可视化
* 通常提供 API 或者命令行工具可用于配置版本化管理，便于持续集成和部署

数据平面的特点：
* 通常是按照无状态目标设计的，但实际上为了提高流量转发性能，需要缓存一些数据，因此无状态也是有争议的
* 直接处理入站和出站数据包，转发、路由、健康检查、负载均衡、认证、鉴权、产生监控数据等
* 对应用来说透明，即可以做到无感知部署

**Istio service mesh架构图**
![arch](https://www.servicemesher.com/istio-handbook/images/istio-mesh-arch.png)

**Istio核心组件：**
**Envoy**
Istio数据平面默认使用Envoy作为sidecar代理，主要功能包括：
* 路由、流量转移
* 弹性能力：如超时重试、熔断等
* 调试功能：如故障注入、流量镜像
**Pilot**
Pilot组件主要功能是将路由规则等配置信息转化为sidecar可以识别的信息，并下发给数据平面
**Citadel**
Citadel是负责安全的组件，内置身份和证书管理功能，实现强大的认证和授权操作
**Galley**
Galley是Istio 1.1版本中新增组件，目的是将Pilot和底层平台(K8s)进行解耦合，分担原来Pilot的部分功能，主要负责配置的验证、提取和处理等功能
```
