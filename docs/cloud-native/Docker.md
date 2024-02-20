# docker笔记

```markdown
Offical:一条命令安装最新稳定版docker：curl -fsSl https://get.docker.com | sh
![docker安装官方参考链接](https://docs.docker.com/engine/install)
![docker-compose安装官方参考链接](https://docs.docker.com/compose/install)
![docker reference](https://docs.docker.com/reference)

![Docker Captains撰写的学习资源](https://docs.docker.com/get-started/resources)
![docker-desktop启动k8s国内用户参考链接](https://github.com/AliyunContainerService/k8s-for-docker-desktop/blob/master/README.md)
```

自定义Windows10下docker-desktop的vm data存储路径

1. quit Docker Desktop
2. wsl -l -v   查看当前运行的vm
3. wsl --export docker-desktop-data "自定义路径\docker-desktop-data.tar" 导出当前数据到自定义目录
4. wsl --unregister docker-desktop-data unregister其data的vm，成功会自动删除默认的vhdx文件
5. wsl --import docker-desktop-data "自定义路径" "自定义路径\docker-desktop-data.tar" --version 2 导入之前导出的vm data

运行这个命令下载最新版本的Docker Compose:

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

## 卸载docker

```bash
yum remove docker-ce
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
To fix this problem, either remove the ~/.docker/ directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:

$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "$HOME/.docker" -R

## centos6安装docker依赖出错解决

```bash
yum remove docker-io
rm -rf /var/lib/docker
rm /etc/yum.repos.d/docker.repo
yum install docker-io或者yum install the docker-engine-1.7.1 rpm
yum install http://yum.dockerproject.org/repo/main/centos/6/Packages/docker-engine-1.7.1-1.el6.x86_64.rpm
service docker status
```

## Docker 中国官方镜像加速

```bash
通过 Docker 官方镜像加速，中国区用户能够快速访问最流行的 Docker 镜像。该镜像托管于中国大陆，本地用户现在将会享受到更快的下载速度和更强的稳定性，从而能够更敏捷地开发和交付 Docker 化应用

Docker 中国官方镜像加速可通过 registry.docker-cn.com 访问。该镜像库只包含流行的公有镜像。私有镜像仍需要从美国镜像库中拉取

您可以使用以下命令直接从该镜像加速地址进行拉取：

$ docker pull registry.docker-cn.com/myname/myrepo:mytag
例如:

$ docker pull registry.docker-cn.com/library/ubuntu:16.04
注: 除非您修改了 Docker 守护进程的 `--registry-mirror` 参数 (见下文), 否则您将需要完整地指定官方镜像的名称。例如，library/ubuntu、library/redis、library/nginx

使用 --registry-mirror 配置 Docker 守护进程
您可以配置 Docker 守护进程默认使用 Docker 官方镜像加速。这样您可以默认通过官方镜像加速拉取镜像，而无需在每次拉取时指定 registry.docker-cn.com

您可以在 Docker 守护进程启动时传入 --registry-mirror 参数：

$ docker --registry-mirror=https://registry.docker-cn.com daemon
为了永久性保留更改，您可以修改 /etc/docker/daemon.json 文件并添加上 registry-mirrors 键值

{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}

修改保存后重启 Docker 以使配置生效

注: 您也可以使用适用于 Mac 的 Docker 和适用于 Windows 的 Docker 来进行设置

使用TLS(https)保护docker守护进程套接字安全参考链接：
https://docs.docker.com/engine/security/protect-access/
```

## ![docker网络](https://docs.docker.com/network)

```markdown
Docker的网络子系统可使用驱动程序插入。默认情况下，有几个驱动程序，它们提供核心联网功能：

1. bridge：默认的网络驱动程序。如果您未指定驱动程序，则这是您正在创建的网络类型。当您的应用程序在需要通信的独立容器中运行时，通常会使用网桥网络
    详解：网桥是在网段之间转发流量的链路层设备，其可以是主机内核中运行的硬件设备和软件设备，docker中使用软件网桥来允许同一网桥网络的容器通信，bridge网络
    用于同一个docker守护进程主机中运行的容器，为了让不同docker守护进程主机上的容器之间通信，可以在OS级别设置路由，也可以使用overlay网络，用户可以自定义
    创建网桥网络，且用户定义的网桥网络优先于默认bridge网络

2. host：对于独立容器，请删除容器与Docker主机之间的网络隔离，然后直接使用主机的网络
  详解：对容器使用host网络模式，则该容器的网络堆栈不会与Docker主机隔离（该容器共享主机的网络名称空间，并且该容器不会分配自己的IP地址
  主机模式网络对于优化性能以及在容器需要处理大量端口的情况下很有用，因为它不需要网络地址转换（NAT），并且不会为每个端口创建“ userland-proxy”

3. overlay：覆盖网络将多个Docker守护程序连接在一起，并使群集服务能够相互通信。您还可以使用覆盖网络来促进群集服务和独立容器之间或不同Docker守护程序上的两个独立容器之间的通信。这种策略消除了在这些容器之间进行操作系统级路由的需要
  详解：overlay网络驱动程序创建多个主机docker守护进程之间的分布式网络，其位于特定主机网络之上(覆盖特定主机的网络)，从而在启用加密后允许与其连接的容器(包括群集服务容器)进行安全通信

  初始化群集或将Docker主机加入现有群集时，将在该Docker主机上创建两个新网络：
  1. 一个称为的覆盖网络ingress，用于处理与群体服务有关的控制和数据流量。创建群集服务并且不将其连接到用户定义的覆盖网络时，ingress 默认情况下它将连接到网络
  2.  一个名为的桥接网络docker_gwbridge，它将各个Docker守护程序连接到该集群中的其他守护程序
  可以overlay使用docker network create来创建自定义网络，就像创建自定义的bridge网络一样。服务或容器可以一次连接到多个网络。但服务或容器只能在它们各自连接的网络之间进行通信

4. macvlan：Macvlan网络允许您为容器分配MAC地址，使其在网络上显示为物理设备。Docker守护程序通过其MAC地址将流量路由到容器。macvlan 在处理希望直接连接到物理网络而不是通过Docker主机的网络堆栈进行路由的旧应用程序时，使用驱动程序有时是最佳选择

5. none：对于此容器，请禁用所有联网。通常与自定义网络驱动程序一起使用。none不适用于群体服务

6. 网络插件：例如：Weave Net、flannel、calico等

网络驱动总结
当您需要多个容器在同一个 Docker 主机上进行通信时，用户定义的桥接网络是最佳选择。
当网络堆栈不应与 Docker 主机隔离，但您希望容器的其他方面被隔离时，主机网络是最佳选择。
当您需要在不同 Docker 主机上运行的容器进行通信时，或者当多个应用程序使用 swarm 服务协同工作时，覆盖网络是最佳选择。
当您从 VM 设置迁移或需要容器看起来像网络上的物理主机时，Macvlan 网络是最佳选择，每个主机都有唯一的 MAC 地址。
第三方网络插件允许您将 Docker 与专门的网络堆栈集成

自定义网桥和默认bridge的区别
用户定义的网桥在容器之间提供自动 DNS 解析
用户定义的桥提供更好的隔离
容器可以在运行中从用户定义的网络连接和分离
每个用户定义的网络都会创建一个可配置的网桥
默认网桥网络上的链接容器共享环境变量
```

https://github.com/wsargent/docker-cheat-sheet

## Docker支持以下存储驱动程序

```markdown
overlay2 是当前所有受支持的Linux发行版的首选存储驱动程序，并且不需要任何额外的配置
aufs是在内核3.13上的Ubuntu 14.04上运行的Docker 18.06及更早版本的首选存储驱动程序，而该内核不支持overlay2
fuse-overlayfs仅在不提供对rootless的支持的主机上运行Rootless Docker时才首选overlay2。在Ubuntu和Debian 10上，即使在无根模式下fuse-overlayfs也不需要使用该驱动程序overlay2。请参阅无根模式文档
devicemapper支持，但是direct-lvm对于生产环境是必需的，因为loopback-lvm零配置虽然性能很差。devicemapper是CentOS和RHEL的推荐存储驱动程序，因为它们的内核版本不支持overlay2。但是，当前版本的CentOS和RHEL现在支持overlay2，这是推荐的驱动程序
如果btrfs和zfs驱动程序是后备文件系统（安装了Docker的主机的文件系统），则使用它们。这些文件系统允许使用高级选项，例如创建“快照”，但需要更多的维护和设置。这些中的每一个都依赖于正确配置的后备文件系统
该vfs存储驱动程序的目的是为了进行测试，并在那里不能使用任何写入时复制文件系统的情况。此存储驱动程序的性能很差，通常不建议在生产中使用
```

### 支持的文件系统

| 存储驱动 | 支持的文件系统 |
| :------: | :------: |
| overlay2, overlay | xfs with ftype=1, ext4 |
| fuse-overlayfs | any filesystem |
| aufs | xfs, ext4 |
| devicemapper | direct-lvm |
| btrfs | btrfs |
| zfs | zfs |
| vfs | any filesystem |

除其他事项外，每个存储驱动程序都有其自己的性能特征，这使其或多或少地适合于不同的工作负载。考虑以下概括：

* overlay2，，aufs和overlay都在文件级别而不是块级别运行。这样可以更有效地使用内存，但是在写繁重的工作负载中，容器的可写层可能会变得非常大
* 块级存储驱动器，例如devicemapper，btrfs和zfs更好地为写繁重的工作负担（虽然不是还有泊坞窗卷）执行
* 对于具有许多层或较深文件系统的许多小型写操作或容器，其性能 overlay可能比更好overlay2，但会消耗更多的inode，这可能导致inode耗尽
* btrfs并zfs需要大量的内存
* zfs 对于高密度工作负载（例如PaaS）是一个不错的选择

Linux 容器依赖于控制组 ，这些控制组不仅跟踪进程组，还公开有关 CPU、内存和块 I/O 使用情况的指标
在基于 systemd 的系统上，可以通过添加systemd.unified_cgroup_hierarchy=1 到内核​​ cmdline来启用 cgroup v2



## Dockerfile参考

```md
构建由Docker守护程序运行，而不是由CLI运行。构建过程做的第一件事是将整个上下文（递归地）发送到守护进程。在大多数情况下，最好从一个空目录作为上下文开始，并将 Dockerfile 保存在该目录中。仅添加构建 Dockerfile 所需的文件

**警告**：不要使用根目录/作为PATH构建上下文，因为它会导致构建将硬盘驱动器的全部内容传输到 Docker 守护程序

## 解析器指令
解析器指令是可选的，会影响 aDockerfile中后续行的处理方式。解析器指令不会向构建添加层，也不会显示为构建步骤。解析器指令是作为一种特殊类型的注释编写的# directive=value。单个指令只能使用一次。

处理完注释、空行或构建器指令后，Docker 不再查找解析器指令。相反，它将任何格式化为解析器指令的内容视为注释，并且不会尝试验证它是否可能是解析器指令。因此，所有解析器指令都必须位于Dockerfile.

解析器指令不区分大小写。但是，约定是小写的。约定还包括在任何解析器指令之后包含一个空行。解析器指令不支持换行符

自定义 Dockerfile 实现允许您：
* 在不更新Docker守护进程的情况下自动修复错误
* 确保所有用户都使用相同的实现来构建您的Dockerfile
* 无需更新Docker守护程序即可使用最新功能
* 在将新功能或第三方功能集成到Docker守护进程之前试用它们
* 使用替代的构建定义，或创建自己的

Dockerfile中的以下指令支持ENV变量：
* ADD
* COPY
* ENV
* EXPOSE
* FROM
* LABEL
* STOPSIGNAL
* USER
* VOLUME
* WORKDIR
* ONBUILD （与上述支持的指令之一结合使用时）

RUN有两种形式：
RUN <command>（shell形式，命令在 shell 中运行，默认/bin/sh -c在 Linux 或cmd /S /CWindows 上）
RUN ["executable", "param1", "param2"]（执行形式）
该RUN指令将在当前图像之上的新层中执行任何命令并提交结果。生成的提交映像将用于Dockerfile

CMD指令有三种形式：
CMD ["executable","param1","param2"]（exec形式，这是首选形式）
CMD ["param1","param2"]（作为ENTRYPOINT 的默认参数）
CMD command param1 param2（外壳形式）
Dockerfile中只能有一条CMD指令。如果您列出多个，CMD 则只有最后一个CMD才会生效

不要疑惑RUN与CMD. RUN实际运行一个命令并提交结果；CMD在构建时不执行任何操作，但会指定映像的预期命令

LABEL指令向图像添加元数据

EXPOSE指令通知 Docker 容器在运行时侦听指定的网络端口。可以指定端口是监听TCP还是UDP，如果不指定协议，默认为TCP，要同时在 TCP 和 UDP 上公开，请包含两行

ADD指令从中复制新文件、目录或远程文件 URL <src> ，并将它们添加到路径 处的图像文件系统中<dest>

COPY指令从中复制新文件或目录<src> 并将它们添加到容器的文件系统中的路径<dest>

VOLUME指令创建一个具有指定名称的挂载点，并将其标记为保存来自本机主机或其他容器的外部挂载卷。该值可以是 JSON 数组

基于 Windows 的容器上的卷：使用基于 Windows 的容器时，容器内卷的目标必须是以下之一：
  一个不存在或空的目录
  C:驱动器以外的驱动器
从 Dockerfile 中更改卷：如果任何构建步骤在声明卷后更改了卷中的数据，则这些更改将被丢弃。
JSON 格式：列表被解析为 JSON 数组。您必须用双引号 ( ") 而不是单引号 ( ')将单词括起来。
主机目录在容器运行时声明：主机目录（挂载点）本质上是依赖于主机的。这是为了保持图像的可移植性，因为不能保证给定的主机目录在所有主机上都可用。因此，您无法从 Dockerfile 中挂载主机目录。该VOLUME指令不支持指定host-dir 参数。您必须在创建或运行容器时指定挂载点

HEALTHCHECK指令告诉 Docker 如何测试容器以检查它是否仍在工作。这可以检测诸如 Web 服务器陷入无限循环并且无法处理新连接之类的情况，即使服务器进程仍在运行

HELL指令允许覆盖用于命令的shell形式的默认 shell 。Linux 上的默认 shell 是["/bin/sh", "-c"]，Windows 上是["cmd", "/S", "/C"]. 该SHELL指令必须以 JSON 格式写入 Dockerfile

https://docs.docker.com/compose/compose-file/compose-file-v3/

重启策略
配置是否以及如何在退出时重新启动容器。替换 restart.
condition: none,on-failure或any(默认: any) 之一。
delay：重新启动尝试之间等待的时间，指定为 持续时间（默认值：5s）。
max_attempts：在放弃之前尝试重新启动容器的次数（默认值：永不放弃）。如果在配置的 内重新启动未成功 window，则此尝试不计入配置max_attempts值。例如，如果max_attempts设置为“2”，并且第一次尝试重启失败，则可能会尝试两次以上的重启。
window：在决定重启是否成功之前等待多长时间，指定为持续时间（默认值：立即决定

回滚配置
配置在更新失败的情况下应如何回滚服务。
parallelism：一次回滚的容器数量。如果设置为 0，则所有容器同时回滚。
delay：每个容器组回滚之间等待的时间（默认为 0 秒）。
failure_action: 如果回滚失败怎么办。其中一个continue或者pause（默认pause）
monitor：每次任务更新后监控失败的持续时间(ns|us|ms|s|m|h)（默认 5s）注意：设置为 0 将使用默认 5s。
max_failure_ratio：回滚期间允许的故障率（默认为 0）。
order：回滚期间的操作顺序。之一stop-first（旧任务在开始新任务之前停止），或start-first（首先启动新任务，并且正在运行的任务短暂重叠）（默认stop-first）。

更新配置
配置应如何更新服务。用于配置滚动更新。
parallelism：一次更新的容器数量。
delay：更新一组容器之间的等待时间。
failure_action: 如果更新失败怎么办。其中一个continue，rollback或者pause （默认：pause）
monitor：每次任务更新后监控失败的持续时间(ns|us|ms|s|m|h)（默认 5s）注意：设置为 0 将使用默认 5s。
max_failure_ratio：更新期间可容忍的故障率。
order：更新期间的操作顺序。之一stop-first（旧任务在开始新任务之前停止），或start-first（新任务首先启动，并且正在运行的任务短暂重叠）（默认stop-first）注意：仅支持 v3.4 及更高版本

```

## 实例

```bash
## 清理冗余container和image，可采用以下方法：

### 停止所有的container，这样才能够删除其中的images：

docker stop $(docker ps -a -q)

### 如果想要删除所有container的话再加一个指令：

docker rm -f $(docker ps -a -q)

### 根据过滤条件删除container，例如：删除某container之前的container

docker rm -f $(sudo docker ps --before="container_id_here" -q)

### 删除所有退出状态的container

docker rm $(docker ps -q -f status=exited)

# 想要删除untagged images，也就是那些id为None的image的话可以用
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

# 要删除全部image的话
docker rmi $(docker images -q)

# 同时删除container和images
docker ps -a --no-trunc  | grep "search_term_here" | awk "{print $1}" | xargs -r --no-run-if-empty docker stop && \
docker ps -a --no-trunc  | grep "search_term_here" | awk "{print $1}" | xargs -r --no-run-if-empty docker rm && \
docker images --no-trunc | grep "search_term_here" | awk "{print $3}" | xargs -r --no-run-if-empty docker rmi

# 删除exited状态和non-tagged的容器
docker ps --filter 'status=Exited' -a | xargs docker stop docker images --filter "dangling=true" -q | xargs docker rmi

docker volume inspect {container}   # 查看容器数据存储位置
docker volume rm $(docker volume ls -qf dangling=true)  # 清理orphaned volumes

docker network rm $(docker network ls -q)   # clean up orphaned networks

## NEW IMAGES/CONTAINERS

\# create new docker container, ie. ubuntu
docker pull ubuntu:latest # 1x pull down image
docker run -i -t ubuntu /bin/bash # drops you into new container as root
```

## 问题及故障解决办法

1.question：Cannot connect to the Docker daemon. Is 'docker -d' running on this host?

yum install -y device-mapper-libs device-mapper

2.helps whit error:'unexpected end of JON input'

docker rm -f $(docker ps -a -q)

\#go to container command line
docker exec -it "container_name_here" bash
