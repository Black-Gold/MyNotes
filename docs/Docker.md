# docker笔记

```sh
Offical:一条命令安装最新稳定版docker：curl -fsSl https://get.docker.com | sh
```

运行这个命令下载最新版本的Docker Compose:

```sh
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

## centos7下docker安装与卸载

```sh
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum list docker-ce --showduplicates | sort -r
yum install <FULLY-QUALIFIED-PACKAGE-NAME>#根据docker-ce-18.03.0.ce-1.el7.centos.x86_64
systemctl start docker
systemctl enable docker
```

## Ubuntu (使用apt-get进行安装)

```sh
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

```sh
yum remove docker-ce
rm -rf /var/lib/docker
```

WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
To fix this problem, either remove the ~/.docker/ directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:

$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "$HOME/.docker" -R

## centos6安装docker依赖出错解决

```sh
yum remove docker-io
rm -rf /var/lib/docker
rm /etc/yum.repos.d/docker.repo
yum install docker-io或者yum install the docker-engine-1.7.1 rpm
yum install http://yum.dockerproject.org/repo/main/centos/6/Packages/docker-engine-1.7.1-1.el6.x86_64.rpm
service docker status
```

## 更改docker镜像安装目录

```sh
方法一：
停止docker进程
Ubuntu/Debian：使用-g选项编辑/etc/default/docker文件：DOCKER_OPTS =“ - dns 8.8.8.8 -dns 8.8.4.4 -g /mnt”
Fedora/Centos：编辑/etc/sysconfig/docker，并在other_args变量中添加-g选项：ex。 other_args =“ -g /var/lib/testdir”。如果有多个选项，请确保将它们括在“”中。重启后，（服务docker重启）Docker应该使用新目录。

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

```sh
通过 Docker 官方镜像加速，中国区用户能够快速访问最流行的 Docker 镜像。该镜像托管于中国大陆，本地用户现在将会享受到更快的下载速度和更强的稳定性，从而能够更敏捷地开发和交付 Docker 化应用。

Docker 中国官方镜像加速可通过 registry.docker-cn.com 访问。该镜像库只包含流行的公有镜像。私有镜像仍需要从美国镜像库中拉取。

您可以使用以下命令直接从该镜像加速地址进行拉取：

$ docker pull registry.docker-cn.com/myname/myrepo:mytag
例如:

$ docker pull registry.docker-cn.com/library/ubuntu:16.04
注: 除非您修改了 Docker 守护进程的 `--registry-mirror` 参数 (见下文), 否则您将需要完整地指定官方镜像的名称。例如，library/ubuntu、library/redis、library/nginx。

使用 --registry-mirror 配置 Docker 守护进程
您可以配置 Docker 守护进程默认使用 Docker 官方镜像加速。这样您可以默认通过官方镜像加速拉取镜像，而无需在每次拉取时指定 registry.docker-cn.com。

您可以在 Docker 守护进程启动时传入 --registry-mirror 参数：

$ docker --registry-mirror=https://registry.docker-cn.com daemon
为了永久性保留更改，您可以修改 /etc/docker/daemon.json 文件并添加上 registry-mirrors 键值。

{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}

修改保存后重启 Docker 以使配置生效。

注: 您也可以使用适用于 Mac 的 Docker 和适用于 Windows 的 Docker 来进行设置。
```

## 官方示例的daemon.json文件配置

在Linux上官方示例的daemon.json文件配置

```json
{
    "authorization-plugins": [],
    "data-root": "",
    "dns": [],
    "dns-opts": [],
    "dns-search": [],
    "exec-opts": [],
    "exec-root": "",
    "experimental": false,
    "storage-driver": "",
    "storage-opts": [],
    "labels": [],
    "live-restore": true,
    "log-driver": "",
    "log-opts": {},
    "mtu": 0,
    "pidfile": "",
    "cluster-store": "",
    "cluster-store-opts": {},
    "cluster-advertise": "",
    "max-concurrent-downloads": 3,
    "max-concurrent-uploads": 5,
    "default-shm-size": "64M",
    "shutdown-timeout": 15,
    "debug": true,
    "hosts": [],
    "log-level": "",
    "tls": true,
    "tlsverify": true,
    "tlscacert": "",
    "tlscert": "",
    "tlskey": "",
    "swarm-default-advertise-addr": "",
    "api-cors-header": "",
    "selinux-enabled": false,
    "userns-remap": "",
    "group": "",
    "cgroup-parent": "",
    "default-ulimits": {},
    "init": false,
    "init-path": "/usr/libexec/docker-init",
    "ipv6": false,
    "iptables": false,
    "ip-forward": false,
    "ip-masq": false,
    "userland-proxy": false,
    "userland-proxy-path": "/usr/libexec/docker-proxy",
    "ip": "0.0.0.0",
    "bridge": "",
    "bip": "",
    "fixed-cidr": "",
    "fixed-cidr-v6": "",
    "default-gateway": "",
    "default-gateway-v6": "",
    "icc": false,
    "raw-logs": false,
    "allow-nondistributable-artifacts": [],
    "registry-mirrors": [],
    "seccomp-profile": "",
    "insecure-registries": [],
    "no-new-privileges": false,
    "default-runtime": "runc",
    "oom-score-adjust": -500,
    "node-generic-resources": ["NVIDIA-GPU=UUID1", "NVIDIA-GPU=UUID2"],
    "runtimes": {
        "cc-runtime": {
            "path": "/usr/bin/cc-runtime"
        },
        "custom": {
            "path": "/usr/local/bin/my-runc-replacement",
            "runtimeArgs": [
                "--debug"
            ]
        }
    }
}

```

## 在Windows上官方示例的daemon.json文件配置

```json

{
    "authorization-plugins": [],
    "data-root": "",
    "dns": [],
    "dns-opts": [],
    "dns-search": [],
    "exec-opts": [],
    "experimental": false,
    "storage-driver": "",
    "storage-opts": [],
    "labels": [],
    "log-driver": "",
    "mtu": 0,
    "pidfile": "",
    "cluster-store": "",
    "cluster-advertise": "",
    "max-concurrent-downloads": 3,
    "max-concurrent-uploads": 5,
    "shutdown-timeout": 15,
    "debug": true,
    "hosts": [],
    "log-level": "",
    "tlsverify": true,
    "tlscacert": "",
    "tlscert": "",
    "tlskey": "",
    "swarm-default-advertise-addr": "",
    "group": "",
    "default-ulimits": {},
    "bridge": "",
    "fixed-cidr": "",
    "raw-logs": false,
    "allow-nondistributable-artifacts": [],
    "registry-mirrors": [],
    "insecure-registries": []
}

```
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

### 查看当前有些什么images

docker images

### 删除images，通过image的id来指定删除谁

```sh
docker rmi <image id>
```

### 想要删除untagged images，也就是那些id为None的image的话可以用

```sh
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

### 要删除全部image的话

docker rmi $(docker images -q)

### 同时删除container和images

\# stop and remove containers and associated images with common grep search term
docker ps -a --no-trunc  | grep "search_term_here" | awk "{print $1}" | xargs -r --no-run-if-empty docker stop && \
docker ps -a --no-trunc  | grep "search_term_here" | awk "{print $1}" | xargs -r --no-run-if-empty docker rm && \
docker images --no-trunc | grep "search_term_here" | awk "{print $3}" | xargs -r --no-run-if-empty docker rmi

\# stops only exited containers and delete only non-tagged images
docker ps --filter 'status=Exited' -a | xargs docker stop docker images --filter "dangling=true" -q | xargs docker rmi

## DELETE NETWORKS AND VOLUMES

\# clean up orphaned volumes
docker volume rm $(docker volume ls -qf dangling=true)

\# clean up orphaned networks
docker network rm $(docker network ls -q)

## NEW IMAGES/CONTAINERS

\# create new docker container, ie. ubuntu
docker pull ubuntu:latest # 1x pull down image
docker run -i -t ubuntu /bin/bash # drops you into new container as root

### Docker命令：

容器操作命令

```sh
docker ps <选项>

-a,--all=false 列出所有容器，不加-a输出running状态容器
--before="" 列出在特定容器创建之前的容器，包含停止的容器

-f,--filter filter 设置输出过滤，如：“exited=0”
   --format string 更
-l,--lastest=false 列
出最后创建的容器，包含停止的容器

-q,--quiet=false 只输出容器的id

```

## 问题及故障解决办法：

1.question：Cannot connect to the Docker daemon. Is 'docker -d' running on this host?

yum install -y device-mapper-libs device-mapper

2.helps whit error:'unexpected end of JON input'

docker rm -f $(docker ps -a -q)

\#go to container command line
docker exec -it "container_name_here" /bin/bash
