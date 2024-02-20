# Kubernetes

```markdown
Kubernetes通过将容器放入在节点（Node）上运行的Pod中来执行你的工作负载。节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。每个节点包含运行Pods所需的服务，这些Pods由Control Plane负责管理
node上组件包括：kubelet、container runtime、kube-proxy
node对象的名称必须是合法的DNS子域名

![部署方式演变图](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)

![Kubernetes组件图](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

## k8s组件

### 控制平面组件

kube-apiserver：kube-apiserver可通过部署多个实例，并在这些实例之间平衡流量
etcd：一致性和高可用，作为保存k8s所有集群数据的后台数据库
kube-scheduler：负责监视新创建、未指定运行节点node的pods，分配pod到某个节点上运行，调度主要考虑因素是pod的资源需求、软硬件或策略的约束、亲和性和反亲和性规范、数据位置、工作负载间的干扰和最后时限
kube-controller-manage：在node节点运行控制器的组件
cloud-controller-manager：云控制管理器是指嵌入特定云的控制逻辑的控制平面组件

### Node组件

kubelet：集群中在每个node节点运行的代理，其保证每个容器都运行在pod中，kubelet不会管理非k8s创建的容器
kube-proxy：集群中每个节点上运行的网络代理，kube-proxy维护节点上的网络规则，这些规则允许从集群内部或外部与pod进行网络通信
容器进行时(container runtime)：容器运行时是负责运行容器的组件

### 插件(Addons)

插件使用k8s资源(DaemonSet、Deployment等)实现集群功能，插件命名空间域的资源属于kube-system命名空间
完整插件列表：![Addons](https://kubernetes.io/zh/docs/concepts/cluster-administration/addons)

![k8s基本架构介绍](https://kubernetes.io/zh/docs/concepts/architecture)


## Kubernetes提供若干种内置的工作负载资源：

* Deployment和ReplicaSet（替换原来的资源ReplicationController）。Deployment很适合用来管理你的集群上的无状态应用，Deployment中的所有Pod都是相互等价的，并且在需要的时候被换掉
* StatefulSet让你能够运行一个或者多个以某种方式跟踪应用状态的Pods。例如，如果你的负载会将数据作持久存储，你可以运行一个StatefulSet，将每个Pod与某个PersistentVolume对应起来。你在StatefulSet中各个Pod内运行的代码可以将数据复制到同一StatefulSet中的其它Pod中以提高整体的服务可靠性
* DaemonSet定义提供节点本地支撑设施的Pods。这些Pods可能对于你的集群的运维是非常重要的，例如作为网络链接的辅助工具或者作为网络插件的一部分等等。每次你向集群中添加一个新节点时，如果该节点与某DaemonSet的规约匹配，则控制面会为该DaemonSet调度一个Pod到该新节点上运行
* Job和CronJob。定义一些一直运行到结束并停止的任务。Job用来表达的是一次性的任务，而CronJob会根据其时间规划反复运行

```


## k8s组件

### 控制平面组件

kube-apiserver：kube-apiserver可通过部署多个实例，并在这些实例之间平衡流量
etcd：一致性和高可用，作为保存k8s所有集群数据的后台数据库
kube-scheduler：负责监视新创建、未指定运行节点node的pods，分配pod到某个节点上运行，调度主要考虑因素是pod的资源需求、软硬件或策略的约束、亲和性和反亲和性规范、数据位置、工作负载间的干扰和最后时限
kube-controller-manage：在node节点运行控制器的组件
cloud-controller-manager：云控制管理器是指嵌入特定云的控制逻辑的控制平面组件

### Node组件

kubelet：集群中在每个node节点运行的代理，其保证每个容器都运行在pod中，kubelet不会管理非k8s创建的容器
kube-proxy：集群中每个节点上运行的网络代理，kube-proxy维护节点上的网络规则，这些规则允许从集群内部或外部与pod进行网络通信
容器进行时(container runtime)：容器运行时是负责运行容器的组件

### 插件(Addons)

插件使用k8s资源(DaemonSet、Deployment等)实现集群功能，插件命名空间域的资源属于kube-system命名空间
完整插件列表：![Addons](https://kubernetes.io/zh/docs/concepts/cluster-administration/addons)

Ingress 是对集群中服务的外部访问进行管理的 API 对象，典型的访问方式是HTTP，必须具有 Ingress 控制器 才能满足 Ingress 的要求。 仅创建 Ingress 资源本身没有任何效果

Istio Ingress 是一个基于 Istio 的 Ingress 控制器
HAProxy Ingress 针对 HAProxy 的 Ingress 控制器
用于 Kubernetes 的 NGINX Ingress 控制器 能够与 NGINX Web 服务器（作为代理） 一起使用

Kubernetes提供若干种内置的工作负载资源：

* Deployment和ReplicaSet（替换原来的资源ReplicationController）。Deployment很适合用来管理你的集群上的无状态应用，Deployment中的所有Pod都是相互等价的，并且在需要的时候被换掉
* StatefulSet让你能够运行一个或者多个以某种方式跟踪应用状态的Pods。例如，如果你的负载会将数据作持久存储，你可以运行一个StatefulSet，将每个Pod与某个PersistentVolume对应起来。你在StatefulSet中各个Pod内运行的代码可以将数据复制到同一StatefulSet中的其它Pod中以提高整体的服务可靠性
* DaemonSet定义提供节点本地支撑设施的Pods。这些Pods可能对于你的集群的运维是非常重要的，例如作为网络链接的辅助工具或者作为网络插件的一部分等等。每次你向集群中添加一个新节点时，如果该节点与某DaemonSet的规约匹配，则控制面会为该DaemonSet调度一个Pod到该新节点上运行
* Job和CronJob。定义一些一直运行到结束并停止的任务。Job用来表达的是一次性的任务，而CronJob会根据其时间规划反复运行

Pod 的 status 字段是一个 PodStatus 对象，其中包含一个 phase 字段。

Pod 的阶段（Phase）是 Pod 在其生命周期中所处位置的简单宏观概述。 该阶段并不是对容器或 Pod 状态的综合汇总，也不是为了成为完整的状态机。
Pod 阶段的数量和含义是严格定义的。 除了本文档中列举的内容外，不应该再假定 Pod 有其他的 phase 值。
下面是 phase 可能的值：
取值	描述
Pending（悬决）	Pod 已被 Kubernetes 系统接受，但有一个或者多个容器尚未创建亦未运行。此阶段包括等待 Pod 被调度的时间和通过网络下载镜像的时间，
Running（运行中）	Pod 已经绑定到了某个节点，Pod 中所有的容器都已被创建。至少有一个容器仍在运行，或者正处于启动或重启状态。
Succeeded（成功）	Pod 中的所有容器都已成功终止，并且不会再重启。
Failed（失败）	Pod 中的所有容器都已终止，并且至少有一个容器是因为失败终止。也就是说，容器以非 0 状态退出或者被系统终止。
Unknown（未知）	因为某些原因无法取得 Pod 的状态。这种情况通常是因为与 Pod 所在主机通信失败


Kubernetes 支持很多类型的卷。 Pod 可以同时使用任意数目的卷类型。 临时卷类型的生命周期与 Pod 相同，但持久卷可以比 Pod 的存活期长。 因此，卷的存在时间会超出 Pod 中运行的所有容器，并且在容器重新启动时数据也会得到保留。 当 Pod 不再存在时，临时卷也将不再存在。但是持久卷会继续存在
Kubernetes 支持下列类型的卷
cephfs
cephfs 卷允许你将现存的 CephFS 卷挂载到 Pod 中。 不像 emptyDir 那样会在 Pod 被删除的同时也会被删除，cephfs 卷的内容在 Pod 被删除 时会被保留，只是卷被卸载了。这意味着 cephfs 卷可以被预先填充数据，且这些数据可以在 Pod 之间共享。同一 cephfs 卷可同时被多个写者挂载

configMap
configMap 卷 提供了向 Pod 注入配置数据的方法。 ConfigMap 对象中存储的数据可以被 configMap 类型的卷引用，然后被 Pod 中运行的 容器化应用使用。
引用 configMap 对象时，你可以在 volume 中通过它的名称来引用。 你可以自定义 ConfigMap 中特定条目所要使用的路径
说明：
在使用 ConfigMap 之前你首先要创建它。
容器以 subPath 卷挂载方式使用 ConfigMap 时，将无法接收 ConfigMap 的更新。
文本数据挂载成文件时采用 UTF-8 字符编码。如果使用其他字符编码形式，可使用 binaryData 字段

emptyDir
当 Pod 分派到某个 Node 上时，emptyDir 卷会被创建，并且在 Pod 在该节点上运行期间，卷一直存在。 就像其名称表示的那样，卷最初是空的。 尽管 Pod 中的容器挂载 emptyDir 卷的路径可能相同也可能不同，这些容器都可以读写 emptyDir 卷中相同的文件。 当 Pod 因为某些原因被从节点上删除时，emptyDir 卷中的数据也会被永久删除。
说明：
容器崩溃并不会导致 Pod 被从节点上移除，因此容器崩溃期间 emptyDir 卷中的数据是安全的。
emptyDir 的一些用途：
缓存空间，例如基于磁盘的归并排序。
为耗时较长的计算任务提供检查点，以便任务能方便地从崩溃前状态恢复执行。
在 Web 服务器容器服务数据时，保存内容管理器容器获取的文件

glusterfs
glusterfs 卷能将 Glusterfs (一个开源的网络文件系统) 挂载到你的 Pod 中。不像 emptyDir 那样会在删除 Pod 的同时也会被删除，glusterfs 卷的内容在删除 Pod 时会被保存，卷只是被卸载。 这意味着 glusterfs 卷可以被预先填充数据，并且这些数据可以在 Pod 之间共享。 GlusterFS 可以被多个写者同时挂载

hostPath 卷能将主机节点文件系统上的文件或目录挂载到你的 Pod 中。 虽然这不是大多数 Pod 需要的，但是它为一些应用程序提供了强大的逃生舱。
例如，hostPath 的一些用法有：
运行一个需要访问 Docker 内部机制的容器；可使用 hostPath 挂载 /var/lib/docker 路径。
在容器中运行 cAdvisor 时，以 hostPath 方式挂载 /sys。
允许 Pod 指定给定的 hostPath 在运行 Pod 之前是否应该存在，是否应该创建以及应该以什么方式存在

nfs
nfs 卷能将 NFS (网络文件系统) 挂载到你的 Pod 中。 不像 emptyDir 那样会在删除 Pod 的同时也会被删除，nfs 卷的内容在删除 Pod 时会被保存，卷只是被卸载。 这意味着 nfs 卷可以被预先填充数据，并且这些数据可以在 Pod 之间共享

local
local 卷所代表的是某个被挂载的本地存储设备，例如磁盘、分区或者目录。
local 卷只能用作静态创建的持久卷。尚不支持动态配置。
与 hostPath 卷相比，local 卷能够以持久和可移植的方式使用，而无需手动将 Pod 调度到节点。系统通过查看 PersistentVolume 的节点亲和性配置，就能了解卷的节点约束。
然而，local 卷仍然取决于底层节点的可用性，并不适合所有应用程序。 如果节点变得不健康，那么local 卷也将变得不可被 Pod 访问。使用它的 Pod 将不能运行。 使用 local 卷的应用程序必须能够容忍这种可用性的降低，以及因底层磁盘的耐用性特征 而带来的潜在的数据丢失风险
使用 local 卷时，你需要设置 PersistentVolume 对象的 nodeAffinity 字段。 Kubernetes 调度器使用 PersistentVolume 的 nodeAffinity 信息来将使用 local 卷的 Pod 调度到正确的节点

persistentVolumeClaim
persistentVolumeClaim 卷用来将持久卷（PersistentVolume） 挂载到 Pod 中。 持久卷申领（PersistentVolumeClaim）是用户在不知道特定云环境细节的情况下"申领"持久存储

PersistentVolume（PV）是一块集群里由管理员手动提供，或 kubernetes 通过 StorageClass 动态创建的存储。 PersistentVolumeClaim（PVC）是一个满足对 PV 存储需要的请求
PersistentVolumes 和 PersistentVolumeClaims 是独立于 Pod 生命周期而在 Pod 重启，重新调度甚至删除过程中保存数据

secret
secret 卷用来给 Pod 传递敏感信息，例如密码。你可以将 Secret 存储在 Kubernetes API 服务器上，然后以文件的形式挂在到 Pod 中，无需直接与 Kubernetes 耦合。 secret 卷由 tmpfs（基于 RAM 的文件系统）提供存储，因此它们永远不会被写入非易失性 （持久化的）存储器
说明： 使用前你必须在 Kubernetes API 中创建 secret。
说明： 容器以 subPath 卷挂载方式挂载 Secret 时，将感知不到 Secret 的更新

获取容器simmemleak-hra99终止之前的状态
kubectl get pod -o go-template='{{range.status.containerStatuses}}{{"Container Name: "}}{{.name}}{{"\r\nLastState: "}}{{.lastState}}{{end}}'  simmemleak-hra99

kube-scheduler调度器调优
设置阈值
percentageOfNodesToScore 选项接受从 0 到 100 之间的整数值。 0 值比较特殊，表示 kube-scheduler 应该使用其编译后的默认值。 如果你设置 percentageOfNodesToScore 的值超过了 100， kube-scheduler 的表现等价于设置值为 100
要修改这个值，先编辑 kube-scheduler 的配置文件 然后重启调度器。 大多数情况下，这个配置文件是 /etc/kubernetes/config/kube-scheduler.yaml

默认阈值
如果你不指定阈值，Kubernetes 使用线性公式计算出一个比例，在 100-节点集群 下取 50%，在 5000-节点的集群下取 10%。这个自动设置的参数的最低值是 5%

更改PV回收策略
kubectl patch pv <your-pv-name> -p '{"spec":{"persistentVolumeReclaimPolicy":"Retain"}}'

DaemonSet 有两种更新策略：要启用 DaemonSet 的滚动更新功能，必须设置 .spec.updateStrategy.type 为 RollingUpdate
OnDelete: 使用 OnDelete 更新策略时，在更新 DaemonSet 模板后，只有当你手动删除老的 DaemonSet pods 之后，新的 DaemonSet Pod 才会被自动创建。跟 Kubernetes 1.6 以前的版本类似。
RollingUpdate: 这是默认的更新策略。使用 RollingUpdate 更新策略时，在更新 DaemonSet 模板后， 老的 DaemonSet pods 将被终止，并且将以受控方式自动创建新的 DaemonSet pods。 更新期间，最多只能有 DaemonSet 的一个 Pod 运行于每个节点上

DaemonSet 滚动更新卡住原因：
一些节点可用资源耗尽
不完整的滚动更新，比如，容器处于崩溃循环状态或者容器镜像不存在
时钟偏差，master节点和worker节点时钟偏差

deployment示例
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

replicaset示例
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3

下面的示例演示了 StatefulSet 的组件
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi


daemonset示例
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers


job示例
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4

cronjob示例
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure

pv示例
kind: PersistentVolume
apiVersion: v1
metadata:
  name: test
  labels:
    type: quobyte
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: "base"
  quobyte:
    # the registry parameter was an required parameter, but is not used anymore.
    # It's here for API compatibility.
    registry: ignored:7861
    volume: test
    readOnly: false
    user: root
    group: root

statefulset和deployment、replicaset区别
无状态应用程序是一个不关心它正在使用哪个网络的应用程序，它不需要永久性存储。无状态应用程序的示例可能包括Web服务器（Apache，Nginx或Tomcat）
有状态应用程序。假设您有一个mysql数据库集群。为了使这样的应用程序正常运行，mysql实例本身也会在彼此之间建立连接以选举一个主节点。由于这种设计，mysql集群是有状态应用程序的一个示例，而且需要持久化的存储数据
