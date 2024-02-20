# Knative

```markdown
Knative Serving 服务组件
Knative Eventing 事件组件
```

## Hashicorp Terraform

## Hashicorp Consul

```md
consul join 手动连接创建集群
consul info 查看集群信息
consul kv get -recurse  列出所有键值存储

sudo chown root:root consul
sudo mv consul /usr/local/bin/
consul -autocomplete-install
complete -C /usr/local/bin/consul consul
sudo useradd --system --home /etc/consul.d --shell /bin/false consul
创建数据目录
sudo mkdir --parents /opt/consul
sudo chown --recursive consul:consul /opt/consul

sudo cat > /etc/systemd/system/consul.service <<EOF
[Unit]
Description="HashiCorp Consul - A service mesh solution"
Documentation=https://www.consul.io/
Requires=network-online.target
After=network-online.target
ConditionFileNotEmpty=/etc/consul.d/consul.hcl

[Service]
Type=notify
User=consul
Group=consul
ExecStart=/usr/local/bin/consul agent -config-dir=/etc/consul.d/
ExecReload=/usr/local/bin/consul reload
KillMode=process
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF

sudo mkdir --parents /etc/consul.d
sudo touch /etc/consul.d/consul.hcl
sudo chown --recursive consul:consul /etc/consul.d
sudo chmod 640 /etc/consul.d/consul.hcl

encrypt- 指定用于加密consul网络流量的密钥,使用consul keygen命令来生成
cat > /etc/consul.d/consul.hcl <<EOF
datacenter = "dc1"
data_dir = "/opt/consul"
encrypt = "Luj2FZWwlt8475wD1WtwUQ=="
EOF
```

sudo mkdir --parents /etc/consul.d
sudo touch /etc/consul.d/server.hcl
sudo chown --recursive consul:consul /etc/consul.d
sudo chmod 640 /etc/consul.d/server.hcl3

ui = true   开启ui界面，只需选定一台运行ui即可
server- 用于控制代理是否处于服务器或客户端模式
bootstrap-expect- 提供数据中心中的预期服务器数
cat > /etc/consul.d/server.hcl <<EOF
server = true
bootstrap_expect = 3
EOF

## Hashicorp Vault

## Hashicorp
