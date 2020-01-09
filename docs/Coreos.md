# **CoreOS**

## Coreos介绍


```bash
生成安全哈希的几种方法：

# On Debian/Ubuntu (via the package "whois")
mkpasswd --method=SHA-512 --rounds=4096

# OpenSSL (note: this will only make md5crypt.  While better than plantext it should not be considered fully secure)
openssl passwd -1

# Python
python -c "import crypt,random,string; print(crypt.crypt(input('clear-text password: '), '\$6\$' + ''.join([random.choice(string.ascii_letters + string.digits) for _ in range(16)])))"

# Perl (change password and salt values)
perl -e 'print crypt("password","\$6\$SALT\$") . "\n"'

# Set the disk size to 20GB
vmware-vdiskmanager -x 20Gb coreos_developer_vmware_insecure.vmx
```

## 配置文件示例

```yaml

passwd:
  users:
    - name: core
      ssh_authorized_keys:
        - ssh-rsa <REST OF SSH KEY> user@somewhere.com
        - ssh-rsa <REST OF ANOTHER SSH KEY> user@somewhere.com

systemd:
  units:
    # Docker will be configured initially but we'll be using containerd exclusively and will disable it after containerd setup
    - name: docker.service
      enabled: true

    # containerd without docker as a shim, thanks to containerd.service.d/ overrides
    - name: containerd.service
      enabled: true

    - name: k8s-install.service
      enabled: true
      contents: |
        [Install]
        WantedBy=multi-user.target

        [Unit]
        Description=k8s installation script
        Wants=network-online.target
        After=network.target network-online.target

        [Service]
        Type=oneshot
        ExecStart=/ignition/init/k8s/install.sh

    - name: cni-install.service
      enabled: true
      contents: |
        [Install]
        WantedBy=multi-user.target

        [Unit]
        Description=cni plugin installation script
        Requires=k8s-install.service
        After=k8s-install.service

        [Service]
        Type=oneshot
        ExecStart=/ignition/init/cni/install.sh

    - name: containerd-install.service
      enabled: true
      contents: |
        [Install]
        WantedBy=multi-user.target

        [Unit]
        Description=containerd installation script
        Requires=cni-install.service
        After=cni-install.service

        [Service]
        Type=oneshot
        ExecStart=/ignition/init/cri-containerd/install.sh

    - name: kubeadm-install.service
      enabled: true
      contents: |
        [Install]
        WantedBy=multi-user.target

        [Unit]
        Description=kubeadm installation script
        Requires=containerd-install.service
        After=containerd-install.service

        [Service]
        Type=oneshot
        Environment="PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/opt/bin"
        ExecStart=/ignition/init/kubeadm/kubeadm-install.sh

    - name: k8s-setup.service
      enabled: true
      contents: |
        [Install]
        WantedBy=multi-user.target

        [Unit]
        Description=kubernetes setup script
        Requires=kubeadm-install.service
        After=kubeadm-install.service

        [Service]
        Type=oneshot
        User=core
        Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/opt/bin"
        ExecStart=/ignition/init/k8s/setup.sh

storage:
  filesystems:
    - mount:
        device: /dev/disk/by-label/ROOT
        format: xfs
        wipe_filesystem: true
        label: ROOT

  files:
    - path: /opt/bin/kubeadm
      filesystem: root
      mode: 493 # 0755
      contents:
        remote:
          url: https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubeadm
          verification:
            hash:
              function: sha512
              sum: fc96e821fd593a212c632a6c9093143fab5817f6833ba1df1ced2ce4fb82f1ebefde71d9a898e8f9574515e9ba19e40f6ab09a907f6b1b908d7adfcf57b3bf8b

    - path: /opt/bin/kubelet
      filesystem: root
      mode: 493 # 0755
      contents:
        remote:
          url: https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubelet
          verification:
            hash:
              function: sha512
              sum: 5cf4bde886d832d1cc48c47aeb43768050f67fe0458a330e4702b8071567665c975ed1fe2296cba5aea95a6de0bec4b731a32525837cac24646fb0158e2c2f64

    - path: /opt/bin/kubectl
      filesystem: root
      mode: 511 # 0777
      contents:
        remote:
          url: https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubectl
          verification:
            hash:
              function: sha512
              sum: 38a2746ac7b87cf7969cf33ccac177e63a6a0020ac593b7d272d751889ab568ad46a60e436d2f44f3654e2b4b5b196eabf8860b3eb87368f0861e2b3eb545a80

    - path: /etc/systemd/system/kubelet.service
      filesystem: root
      mode: 420 # 0644
      contents:
        remote:
          url: https://raw.githubusercontent.com/kubernetes/kubernetes/v1.10.2/build/debs/kubelet.service
          verification:
            hash:
              function: sha512
              sum: b9ca0db34fea67dfd0654e65d3898a72997b1360c1e802cab5adc4288199c1a08423f90751757af4a7f1ff5932bfd81d3e215ce9b9d3f4efa1c04a202228adc8

    - path: /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
      filesystem: root
      mode: 420 # 0644
      contents:
        remote:
          url: https://raw.githubusercontent.com/kubernetes/kubernetes/v1.10.2/build/debs/10-kubeadm.conf
          verification:
            hash:
              function: sha512
              sum: 32cfc8e56ec6e5ba93219852a68ec5eb25938a39c3e360ea4728fc71a14710b6ff85d0d84c2663eb5297d5dc21e1fad6914d6c0a8ce3357283f0b98ad4280ef7

    - path: /ignition/init/cri-containerd/cri-containerd-1.1.0.linux-amd64.tar.gz
      filesystem: root
      mode: 420 # 0644
      contents:
        remote:
          url: https://storage.googleapis.com/cri-containerd-release/cri-containerd-1.1.0.linux-amd64.tar.gz
          verification:
            hash:
              function: sha512
              sum: c5db1b99e1155ed0dfe945bc1d53842fd015cb891f8b773c60fea73f9fe7c3e0bda755133765aa618c08765eb13dbf244affb5a1572d5a888ff4298ba3d790cf

    - path: /ignition/init/cni/cni-plugins-v0.7.1.tgz
      filesystem: root
      mode: 420 # 0644
      contents:
        remote:
          url: https://github.com/containernetworking/plugins/releases/download/v0.7.1/cni-plugins-amd64-v0.7.1.tgz
          verification:
            hash:
              function: sha512
              sum: b3b0c1cc7b65cea619bddae4c17b8b488e7e13796345c7f75e069af93d1146b90a66322be6334c4c107e8a0ccd7c6d0b859a44a6745f9b85a0239d1be9ad4ccd

    - path: /ignition/init/canal/rbac.yaml
      filesystem: root
      mode: 493 # 0755
      contents:
        remote:
          url: https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/rbac.yaml
          verification:
            hash:
              function: sha512
              sum: e045645a1f37b4974890c3e4f8505a10bbb138ed0723869d7a7bc399c449072dfd2c8c2c482d3baac9bf700b7b0cfdca122cb260e70b437fb495eb86f9f6cccc

    - path: /ignition/init/canal/canal.yaml
      filesystem: root
      mode: 493 # 0755
      contents:
        remote:
          url: https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/canal.yaml
          verification:
            hash:
              function: sha512
              sum: 5c4953d74680a2ff84349c730bba48d0d43e28b70217c59cdf736f65e198f362c25b534b686c10ed5370dbb1bca4a1326e42039ddee7eb7b0dd314cc08523b67

    - path: /ignition/init/k8s/install.sh
      filesystem: root
      mode: 480 # 740
      contents:
        inline: |
          #!/bin/bash

          # Unzip the kubernetes binaries if not already present
          test -d /opt/bin/kubeadm && echo "k8s binaries (kubeadm) already installed" && exit 0

          # NOTE: If RELEASE is updated, the SHA512 SUMs will need to be as well
          echo -e "=> Installing k8s v1.10.2"

          echo "=> Cusomizing kubelet.service..."
          sed -i "s:/usr/bin:/opt/bin:g" /etc/systemd/system/kubelet.service
          sed -i "s:/usr/bin:/opt/bin:g" /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

          systemctl daemon-reload
          systemctl enable kubelet
          systemctl start kubelet

    - filesystem: root
      path: /ignition/init/cri-containerd/install.sh
      mode: 480 # 740
      contents:
        inline: |
          #!/bin/bash

          # Unzip the kubernetes binaries if not already present
          test -d /opt/containerd && echo "containerd binaries already installed" && exit 0

          VERSION=1.1.0
          echo -e "=> Installing containerd v${VERSION}"

          echo "=> Installing containerd...."
          cd /ignition/init/cri-containerd
          tar -C / -k -xzf cri-containerd-${VERSION}.linux-amd64.tar.gz

          echo "=> Copying /usr/local binaries to /opt/bin ...."
          mkdir -p /ignition/init/cri-containerd/unzipped
          tar -C unzipped -k -xzf cri-containerd-${VERSION}.linux-amd64.tar.gz
          cp -r unzipped/usr/local/bin/* /opt/bin
          systemctl start containerd

          echo "=> Adding dropins...."
          cat > /etc/systemd/system/kubelet.service.d/0-containerd.conf <<EOF
          [Service]
          Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock --volume-plugin-dir=/var/lib/kubelet/volumeplugins"
          EOF

          mkdir -p /etc/systemd/system/containerd.service.d/
          cat > /etc/systemd/system/containerd.service.d/0-direct-containerd.conf <<EOF
          [Service]
          ExecStart=
          ExecStart=/opt/bin/containerd
          EOF

          echo "=> Triggering systemctl daemon-reload...."
          systemctl daemon-reload
          systemctl restart containerd

    - filesystem: root
      path: /ignition/init/cni/install.sh
      mode: 480 # 740
      contents:
        inline: |
          #!/bin/bash

          # Unzip the kubernetes binaries if not already present
          test -d /opt/cni/bin && echo "CNI binaries already installed" && exit 0

          VERSION=0.7.1
          echo -e "=> Installing CNI (v${VERSION}) binaries to /opt/cni/bin"
          cd /ignition/init/cni
          mkdir -p /opt/cni/bin
          tar -C /opt/cni/bin -k -xzf cni-plugins-v${VERSION}.tgz

    - filesystem: root
      path: /ignition/init/kubeadm/kubeadm-install.sh
      mode: 480 # 740
      contents:
        inline: |
          #!/bin/bash

          # Ensure kubeadm binary is present
          test -f /opt/bin/kubeadm || (echo "Failed to find kubeadm binary" && exit 1)

          # Exit if kubeadm has already been run (/etc/kubernetes folder would have been created)
          test -d /etc/kubernetes && echo "/etc/kubernetes is present, kubeadm should have already been run once" && exit 0

          echo "=> Running kubeadm init..."
          /opt/bin/kubeadm init --cri-socket "/run/containerd/containerd.sock" --pod-network-cidr "10.244.0.0/16"

          # Disable docker (kubelet will use containerd runtime directly)
          sudo systemctl stop docker
          sudo systemctl disable docker

          echo "=> Running kubeadm post-install set up for user 'core'"
          mkdir -p /home/core/.kube
          cp -i /etc/kubernetes/admin.conf /home/core/.kube/config
          chown $(id -u core):$(id -g core) /home/core/.kube/config

    - filesystem: root
      path: /ignition/init/k8s/setup.sh
      mode: 493 # 0755
      contents:
        inline: |
          #!/bin/bash

          # Ensure /etc/kubernetes is present (created by kubeadm)
          test -d /etc/kubernetes || (echo "/etc/kubernetes not present, ensure kubeadm has run properly" && exit 1)

          echo "=> Enabling workload running on the master node"
          kubectl taint nodes --all node-role.kubernetes.io/master-

          echo "=> Installing canal"
          kubectl apply -f /ignition/init/canal/rbac.yaml
          kubectl apply -f /ignition/init/canal/canal.yaml



```

```bash
#!/bin/bash

echo "Installing coreos-install..."
curl -sSL https://raw.githubusercontent.com/coreos/init/master/bin/coreos-install > /bin/coreos-install
chmod +x /bin/coreos-install

echo "Installing ct..."
curl -sSL https://github.com/coreos/container-linux-config-transpiler/releases/download/v0.8.0/ct-v0.8.0-x86_64-unknown-linux-gnu > /bin/ct
echo "9f9d9d9c802a6dc875067295aa9d7f44f1e66914cc992cf215dd459ee2b719fde4ebfa036bb8488cfd87ae2efafc5d767de776fe11a4661fc45e8d54385953a4  ct" | sha512sum -c || (echo "ERROR: SHA512 Hash does not match for ct v0.8.0" && exit 1)
chmod +x /bin/ct

```
