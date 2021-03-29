# docker笔记

```markdown
Offical:一条命令安装最新稳定版docker：curl -fsSl https://get.docker.com | sh

![Docker Captains撰写的学习资源](https://docs.docker.com/get-started/resources)
![docker-desktop安装参考链接](https://github.com/AliyunContainerService/k8s-for-docker-desktop/blob/master/README.md)
```

运行这个命令下载最新版本的Docker Compose:

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

## centos7下docker安装与卸载

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum list docker-ce --showduplicates | sort -r
yum install <FULLY-QUALIFIED-PACKAGE-NAME>  # 根据docker-ce-18.09.5-3.el7.x86_64
systemctl start docker
systemctl enable docker
```

## Ubuntu (使用apt-get进行安装)

```bash
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装 Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce

# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# apt-cache madison docker-ce
#   docker-ce | 17.03.1~ce-0~ubuntu-xenial | http://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
#   docker-ce | 17.03.0~ce-0~ubuntu-xenial | http://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# Step 2: 安装指定版本的Docker-CE: (VERSION 例如上面的 17.03.1~ce-0~ubuntu-xenial)
# sudo apt-get -y install docker-ce=[VERSION]
```

## 卸载docker

```bash
yum remove docker-ce
rm -rf /var/lib/docker
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

## 更改docker镜像安装目录(新版本通过配置文件参数自定义指定)

```bash
方法一：
停止docker进程
Ubuntu/Debian：使用-g选项编辑/etc/default/docker文件：DOCKER_OPTS =“ - dns 8.8.8.8 -dns 8.8.4.4 -g /mnt”
Fedora/Centos：编辑/etc/sysconfig/docker，并在other_args变量中添加-g选项：ex。 other_args =“ -g /var/lib/testdir”。如果有多个选项，请确保将它们括在“”中。重启后，（服务docker重启）Docker应该使用新目录

方法二：
使用符号链接是另一种更改图像存储的方法，注意 - 这些步骤取决于您当前的/var/lib/docker是否为实际目录（不是指向其他位置的符号链接）
停止docker进程
备份/var/lib/docker/目录
tar -zcC /var/lib docker > /mnt/pd0/var_lib_docker-backup-$(date +%s).tar.gz
移动/var/lib/docker目录到新分区: mv /var/lib/docker /mnt/pd0/docker
添加符号链接: ln -s /mnt/pd0/docker /var/lib/docker
看一下目录结构，确保它看起来像是在mv之前的一样: ls /var/lib/docker/ (请注意用于解析符号链接的尾部斜杠)

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
```

## Docker支持以下存储驱动程序

```markdown
overlay2 是当前所有受支持的Linux发行版的首选存储驱动程序，并且不需要任何额外的配置
aufs是在内核3.13上的Ubuntu 14.04上运行的Docker 18.06及更早版本的首选存储驱动程序，而该内核不支持overlay2
fuse-overlayfs仅在不提供对rootless的支持的主机上运行Rootless Docker时才首选overlay2。在Ubuntu和Debian 10上，即使在无根模式下fuse-overlayfs也不需要使用该驱动程序overlay2。请参阅无根模式文档
devicemapper支持，但是direct-lvm对于生产环境是必需的，因为loopback-lvm零配置虽然性能很差。devicemapper是CentOS和RHEL的推荐存储驱动程序，因为它们的内核版本不支持overlay2。但是，当前版本的CentOS和RHEL现在支持overlay2，这是推荐的驱动程序
如果btrfs和zfs驱动程序是后备文件系统（安装了Docker的主机的文件系统），则使用它们。这些文件系统允许使用高级选项，例如创建“快照”，但需要更多的维护和设置。这些中的每一个都依赖于正确配置的后备文件系统
该vfs存储驱动程序的目的是为了进行测试，并在那里不能使用任何写入时复制文件系统的情况。此存储驱动程序的性能很差，通常不建议在生产中使用
```

## 官方示例的daemon.json文件配置

## Docker命令

```bash
用法:docker [OPTIONS] COMMAND

Options:
      --config string      Location of client config files (default "/root/.docker")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/root/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/root/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/root/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  builder     Manage builds
  config      Manage Docker configs
  container   Manage containers
  engine      Manage the docker engine
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.

```

## 实例

```bash


docker run -dt -p 127.0.0.1:9980:9980 -e "domain=192\.168\.124\.3" -e "username=admin" -e "password=admin" --restart always --cap-add MKNOD docker.io/thedarkknight/libreoffice-online-unlimited



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
