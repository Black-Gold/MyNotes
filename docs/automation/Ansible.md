# Ansible

```markdown
sage: ansible <host-pattern> [options]

Define and run a single task 'playbook' against a set of hosts

Options:
  -a MODULE_ARGS, --args=MODULE_ARGS
                        module arguments
  --ask-vault-pass      ask for vault password
  -B SECONDS, --background=SECONDS
                        run asynchronously, failing after X seconds
                        (default=N/A)
  -C, --check           don't make any changes; instead, try to predict some
                        of the changes that may occur
  -D, --diff            when changing (small) files and templates, show the
                        differences in those files; works great with --check
  -e EXTRA_VARS, --extra-vars=EXTRA_VARS
                        set additional variables as key=value or YAML/JSON, if
                        filename prepend with @
  -f FORKS, --forks=FORKS
                        specify number of parallel processes to use
                        (default=5)
  -h, --help            show this help message and exit
  -i INVENTORY, --inventory=INVENTORY, --inventory-file=INVENTORY
                        specify inventory host path or comma separated host
                        list. --inventory-file is deprecated
  -l SUBSET, --limit=SUBSET
                        further limit selected hosts to an additional pattern
  --list-hosts          outputs a list of matching hosts; does not execute
                        anything else
  -m MODULE_NAME, --module-name=MODULE_NAME
                        module name to execute (default=command)
  -M MODULE_PATH, --module-path=MODULE_PATH
                        prepend colon-separated path(s) to module library
                        (default=[u'/root/.ansible/plugins/modules',
                        u'/usr/share/ansible/plugins/modules'])
  -o, --one-line        condense output
  --playbook-dir=BASEDIR
                        Since this tool does not use playbooks, use this as a
                        subsitute playbook directory.This sets the relative
                        path for many features including roles/ group_vars/
                        etc.
  -P POLL_INTERVAL, --poll=POLL_INTERVAL
                        set the poll interval if using -B (default=15)
  --syntax-check        perform a syntax check on the playbook, but do not
                        execute it
  -t TREE, --tree=TREE  log output to this directory
  --vault-id=VAULT_IDS  the vault identity to use
  --vault-password-file=VAULT_PASSWORD_FILES
                        vault password file
  -v, --verbose         verbose mode (-vvv for more, -vvvv to enable
                        connection debugging)
  --version             show program's version number and exit

  Connection Options:
    control as whom and how to connect to hosts

    -k, --ask-pass      ask for connection password
    --private-key=PRIVATE_KEY_FILE, --key-file=PRIVATE_KEY_FILE
                        use this file to authenticate the connection
    -u REMOTE_USER, --user=REMOTE_USER
                        connect as this user (default=None)
    -c CONNECTION, --connection=CONNECTION
                        connection type to use (default=smart)
    -T TIMEOUT, --timeout=TIMEOUT
                        override the connection timeout in seconds
                        (default=10)
    --ssh-common-args=SSH_COMMON_ARGS
                        specify common arguments to pass to sftp/scp/ssh (e.g.
                        ProxyCommand)
    --sftp-extra-args=SFTP_EXTRA_ARGS
                        specify extra arguments to pass to sftp only (e.g. -f,
                        -l)
    --scp-extra-args=SCP_EXTRA_ARGS
                        specify extra arguments to pass to scp only (e.g. -l)
    --ssh-extra-args=SSH_EXTRA_ARGS
                        specify extra arguments to pass to ssh only (e.g. -R)

  Privilege Escalation Options:
    control how and which user you become as on target hosts

    -s, --sudo          run operations with sudo (nopasswd) (deprecated, use
                        become)
    -U SUDO_USER, --sudo-user=SUDO_USER
                        desired sudo user (default=root) (deprecated, use
                        become)
    -S, --su            run operations with su (deprecated, use become)
    -R SU_USER, --su-user=SU_USER
                        run operations with su as this user (default=None)
                        (deprecated, use become)
    -b, --become        run operations with become (does not imply password
                        prompting)
    --become-method=BECOME_METHOD
                        privilege escalation method to use (default=sudo),
                        valid choices: [ sudo | su | pbrun | pfexec | doas |
                        dzdo | ksu | runas | pmrun | enable | machinectl ]
    --become-user=BECOME_USER
                        run operations as this user (default=root)
    --ask-sudo-pass     ask for sudo password (deprecated, use become)
    --ask-su-pass       ask for su password (deprecated, use become)
    -K, --ask-become-pass
                        ask for privilege escalation password

```


### Centos下安装部署

Ansible是一种批量部署工具，现在运维人员用的最多的三种开源集中化管理工具有：puppet,saltstack,ansible，各有各的优缺点，其中saltstack和ansible都是用python开发的。ansible其实准确的说只提供了一个框架，它要基于很多其他的python模块才能工作的，所以在安装ansible的时候你要再装很多其他的依赖包的

好处之一是使用者可以开发自己的模块，放在里面使用。第二个好处是无需在客户端安装agent，更新时，只需在操作机上进行一次更新即可。第三个好处是批量任务执行可以写成脚本，而且不用分发到远程就可以执行

```
(1)、python2.7安装,升级到2.7.9

https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tgz

# tar xvzf Python-2.7.9.tgz

# cd Python-2.7.9

# ./configure --prefix=/usr/local

# make && make install
## 将python头文件拷贝到标准目录，以避免编译ansible时，找不到所需的头文件

# cd /usr/local/include/python2.7

# cp -a ./* /usr/local/include/

## 备份旧版本的python，并符号链接新版本的python

# cd /usr/bin	#旧版本Python路径

# mv python python.old	#备份旧版本

# ln -s /usr/local/bin/python2 /usr/local/bin/python

# rm -f /usr/bin/python && cp /usr/local/bin/python2 /usr/bin/python

## 修改yum脚本，使其指向旧版本的python，已避免其无法运行

# vim /usr/bin/yum

#!/usr/bin/python  -->  #!/usr/bin/python2.7

No module named urlgrabber.grabber报错处理

vim /usr/libexec/urlgrabber-ext-down

#! /usr/bin/python  --> #! /usr/bin/python2.7

python --version验证版本
Tips :若python版本已经为2.6或以上，可以不需要再重装python，只是还需要安装python开发包：python-dev(有的操作系统下为python-devel)
        
(2)、setuptools pip wheel模块安装
wget https://bootstrap.pypa.io/get-pip.py

python get-pip.py

python -m pip install --upgrade pip setuptools wheel	#升级pip setuptools wheel

安装好setuptools后就可以利用easy_install或pip这个工具安装下面的python模块了

easy_install pycrypto pyyaml jinja2 paramiko simplejson

pip install pycrypto pyyaml jinja2 paramiko simplejson

(3)、ansible安装

https://github.com/ansible/ansible/archive/v1.7.2.tar.gz

# tar xvzf ansible-1.7.2.tar.gz

# cd ansible-1.7.2

# python setup.py install



(4)、SSH免密钥登录设置

## 生成公钥/私钥

# ssh-keygen -t rsa -P ''

## 写入信任文件（将/root/.ssh/id_rsa_storm1.pub分发到其他服务器，并在所有服务器上执行如下指令）：

# cat /root/.ssh/id_rsa_storm1.pub >> /root/.ssh/authorized_keys

# chmod 600 /root/.ssh/authorized_keys



(5)、拷贝，生成ansible配置文件

a 配置文件/etc/ansible/ansible.cfg

# mkdir -p /etc/ansible

#cp ansible-1.7.2/examples/ansible.cfg /etc/ansible/

b 配置文件/etc/ansible/hosts

# vim /etc/ansible/hosts

[test]

192.168.110.20

192.168.110.30



测试

# ansible test -m command -a 'uptime'

## 用来测试远程主机的运行状态

# ansible test -m ping

参看所有的参数

ansible-doc -l

```

# ansible的离线安装

需要提前准备的安装包脚本,install_ansible.sh

```

#!/bin/sh  
# 1.创建文件夹  
mkdir /opt/ansible  
  
# 2. 移动资源到目标文件夹  
cp -rf ./res/* /opt/ansible/  
  
# 3. 安装python  
cd /opt/ansible  
tar xvzf Python-2.7.tgz  
rm -f Python-2.7.tgz  
cd Python-2.7  
./configure --prefix=/usr/local  
make  
make install  
cd /usr/local/include/python2.7  
cp -a ./* /usr/local/include/  
cd /usr/bin  
mv python python.old  
ln -s -f /usr/local/bin/python2.7 /usr/local/bin/python  
rm -f /usr/bin/python && cp /usr/local/bin/python2.7 /usr/bin/python  
  
# 4. 安装setuptools  
cd /opt/ansible  
tar xvzf setuptools-7.0.tar.gz  
rm -f setuptools-7.0.tar.gz  
cd setuptools-7.0  
python setup.py install  
  
# 5. 安装pycrypto  
cd /opt/ansible  
tar xvzf pycrypto-2.6.tar.gz  
rm -f pycrypto-2.6.tar.gz  
cd pycrypto-2.6  
python setup.py install  
  
# 6. 安装PyYAML  
cd /opt/ansible  
tar xvzf yaml-0.1.5.tar.gz  
rm -f yaml-0.1.5.tar.gz  
cd yaml-0.1.5  
./configure --prefix=/usr/local  
make --jobs=`grep processor /proc/cpuinfo | wc -l`  
make install  #出问题了  
  
cd /opt/ansible  
tar xvzf PyYAML-3.11.tar.gz  
rm -f PyYAML-3.11.tar.gz  
cd PyYAML-3.11  
python setup.py install  
  
# 7. 安装MarkupSafe  
cd /opt/ansible  
tar xvzf MarkupSafe-0.9.3.tar.gz  
rm -f MarkupSafe-0.9.3.tar.gz  
cd MarkupSafe-0.9.3  
python setup.py install  
cd /opt/ansible  
tar xvzf Jinja2-2.7.3.tar.gz  
rm -f Jinja2-2.7.3.tar.gz  
cd Jinja2-2.7.3  
python setup.py install  
  
# 8. 安装ecdsa  
cd /opt/ansible  
tar xvzf ecdsa-0.13.tar.gz  
rm -f ecdsa-0.13.tar.gz  
cd ecdsa-0.13  
python setup.py install  
cd /opt/ansible  
tar xvzf paramiko-1.15.1.tar.gz  
rm -f paramiko-1.15.1.tar.gz  
cd paramiko-1.15.1  
python setup.py install  
 
# 9. 安装simplejson  
cd /opt/ansible  
tar xvzf simplejson-3.8.2.tar.gz  
rm -f simplejson-3.8.2.tar.gz  
cd simplejson-3.8.2  
python setup.py install  
cd /opt/ansible  
tar xvzf ansible-latest.tar.gz  
rm -f ansible-latest.tar.gz  
cd ansible-2.1.0.0  
python setup.py install  

```

## Apollo--携程框架部门研发的配置管理平台
