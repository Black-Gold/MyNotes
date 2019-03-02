# Linux

系统命令行快捷键

Ctrl+左右键:在单词之间跳转
Ctrl+a:跳到本行的行首
Ctrl+e:跳到页尾
Ctrl+u：删除当前光标前面的文字 （还有剪切功能）
Ctrl+k：删除当前光标后面的文字(还有剪切功能)
Ctrl+L：进行清屏操作
Ctrl+y:粘贴Ctrl+u或ctrl+k剪切的内容
Ctrl+w:删除光标前面的单词的字符
Alt – d ：由光标位置开始，往右删除单词。往行尾删
说明
Ctrl – k: 先按住 Ctrl 键，然后再按 k 键；
Alt – k: 先按住 Alt 键，然后再按 k 键；
M – k：先单击 Esc 键，然后再按 k 键。
移动光标
Ctrl – a ：移到行首
Ctrl – e ：移到行尾
Ctrl – b ：往回(左)移动一个字符
Ctrl – f ：往后(右)移动一个字符
Alt – b ：往回(左)移动一个单词
Alt – f ：往后(右)移动一个单词
Ctrl – xx ：在命令行尾和光标之间移动
M-b ：往回(左)移动一个单词
M-f ：往后(右)移动一个单词
编辑命令
Ctrl – h ：删除光标左方位置的字符
Ctrl – d ：删除光标右方位置的字符（注意：当前命令行没有任何字符时，会注销系统或结束终端）
Ctrl – w ：由光标位置开始，往左删除单词。往行首删
Alt – d ：由光标位置开始，往右删除单词。往行尾删
M – d ：由光标位置开始，删除单词，直到该单词结束。
Ctrl – k ：由光标所在位置开始，删除右方所有的字符，直到该行结束。
Ctrl – u ：由光标所在位置开始，删除左方所有的字符，直到该行开始。
Ctrl – y ：粘贴之前删除的内容到光标后。
ctrl – t ：交换光标处和之前两个字符的位置。
Alt + . ：使用上一条命令的最后一个参数。
Ctrl – _ ：回复之前的状态。撤销操作。
Ctrl -a + Ctrl -k 或 Ctrl -e + Ctrl -u 或 Ctrl -k + Ctrl -u 组合可删除整行。

Bang(!)命令
!! ：执行上一条命令。
^foo^bar ：把上一条命令里的foo替换为bar，并执行。
!wget ：执行最近的以wget开头的命令。
!wget:p ：仅打印最近的以wget开头的命令，不执行。
!$ ：上一条命令的最后一个参数， 与 Alt - . 和 $_ 相同。
!* ：上一条命令的所有参数
!*:p ：打印上一条命令是所有参数，也即 !*的内容。
^abc ：删除上一条命令中的abc。
^foo^bar ：将上一条命令中的 foo 替换为 bar
^foo^bar^ ：将上一条命令中的 foo 替换为 bar
!-n ：执行前n条命令，执行上一条命令： !-1， 执行前5条命令的格式是： !-5
查找历史命令
Ctrl – p ：显示当前命令的上一条历史命令
Ctrl – n ：显示当前命令的下一条历史命令
Ctrl – r ：搜索历史命令，随着输入会显示历史命令中的一条匹配命令，Enter键执行匹配命令；ESC键在命令行显示而不执行匹配命令。
Ctrl – g ：从历史搜索模式（Ctrl – r）退出。
控制命令
Ctrl – l ：清除屏幕，然后，在最上面重新显示目前光标所在的这一行的内容。
Ctrl – o ：执行当前命令，并选择上一条命令。
Ctrl – s ：阻止屏幕输出
Ctrl – q ：允许屏幕输出
Ctrl – c ：终止命令
Ctrl – z ：挂起命令
重复执行操作动作
M – 操作次数 操作动作 ： 指定操作次数，重复执行指定的操作。

lshw -C network

I. stunnel -> vanish -> HAProxy -> nginx -> nodeJS -> memcached(redis) (for session storage[Session 对象存储])

II. nginx (for HTTP compression) –> Varnish cache (for caching) –> HTTP level load balancer (HAProxy, or nginx, or the Varnish built-in) –> webservers.

III. Apache Traffic Server/Squid/Vanish + HAProxy + Nginx + memcached(Redis) (for session storage[Session 对象存储])

## Ubuntu--Debian

[Ubuntu内核网站](http://kernel.ubuntu.com/~kernel-ppa/mainline/)

系统相关配置

挂起后进入系统输入：xdotool mouseup 1
然后注销重新登录

将Ubuntu启动栏移动到最下方
gsettings set com.canonical.Unity.Launcher launcher-position Bottom

得到启动栏位置
gsettings get com.canonical.Unity.Launcher launcher-position

卸载软件包，清除残余的配置文件
apt-get remove --purge -y
apt-get autoremove --purge -y
sudo apt-get clean
apt-get autoclean
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

要删除作为依赖项安装但不再具有父应用程序的软件包，请运行以下命令
apt-get autoremove

删除旧内核
dpkg --purge --force-remove-essential kernel-image-NNN

删除专用软件包
dpkg --get-selections | grep PACKAGE_NAME | awk '{ print $1}'| xargs apt-get -y --purge autoremove

禁用挂起suspend
创建文件：/etc/polkit-1/localauthority/90-mandatory.d/disable-suspend.pkla

```pkla
[Disable suspend (upower)]
Identity=unix-user:*
Action=org.freedesktop.upower.suspend
ResultActive=no
ResultInactive=no
ResultAny=no

[Disable suspend (logind)]
Identity=unix-user:*
Action=org.freedesktop.login1.suspend
ResultActive=no

[Disable suspend for all sessions (logind)]
Identity=unix-user:*
Action=org.freedesktop.login1.suspend-multiple-sessions
ResultActive=no
```

设置临时文件不再放置在硬盘加快速度，而放在虚拟RAM磁盘上。总内存必须大于4G
cp -v /usr/share/systemd/tmp.mount /etc/systemd/system/
systemctl enable tmp.mount
systemctl reload tmp.mount # 不生效可重启计算机。systemctl status tmp.mount检查状态
取消tmpfs,删除后重启生效
rm -v /etc/systemd/system/tmp.mount

减少文件写入(当用于非服务器时)在/etc/fstab写入以下内容：

```fstab
tmpfs    /tmp        tmpfs    defaults    0  0
tmpfs    /var/tmp    tmpfs    defaults    0  0
tmpfs    /var/log    tmpfs    defaults    0  0
```

创建脚本确保各种程序正常运行：

```sh
for dir in apparmor apt ConsoleKit cups dist-upgrade fsck gdm installer news ntpstats samba unattended-upgrades ; do
if [ ! -e /var/log/$dir ] ; then
mkdir /var/log/$dir
fi
done

# 要确保每次启动计算机时脚本都会运行，您需要将其添加到/etc/rc.local文件中，位于底部，位于exit 0行上方
```

系统～/.bashrc和~/.profile文件可通过/etc/skel/目录(系统存储的备份文件)恢复

find: ‘/run/user/1000/gvfs’解决办法

```sh
sudo umount /run/user/1000/gvfs
sudo rm -rf /run/user/1000/gvfs
```

```sh
pip3 install --no-cache-dir --prefix="/home/ww/公共的/pip3/" -U -i https://mirrors.huaweicloud.com/repository/pypi/simple some-package-name
```

系统调用 尝试“strace -ff -t -s 1000 -p {process id}

sudo apt-get install --only-upgrade packagename

自定义terminal位置和大小
cp /usr/share/applications/gnome-terminal.desktop ~/.local/share/applications
sed -i 's/^Exec=gnome-terminal$/& --geometry=80x24+100+100/' ~/.local/share/applications/gnome-terminal.desktop
gsettings set org.gnome.desktop.default-applications.terminal exec 'gnome-terminal --geometry=80x24+100+100'
删除自定义
rm ~/.local/share/applications/gnome-terminal.desktop
gsettings set org.gnome.desktop.default-applications.terminal exec 'gnome-terminal'

Ubuntu Linux系统环境变量配置文件：
/etc/profile : 在登录时,操作系统定制用户环境时使用的第一个文件 ,此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行。

/etc /environment : 在登录时操作系统使用的第二个文件, 系统在读取你自己的profile前,设置环境文件的环境变量。

~/.profile :  在登录时用到的第三个文件 是.profile文件,每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。

/etc/bashrc : 为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

~/.bashrc : 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。

PASH环境变量的设置方法：

方法一：用户主目录下的.profile或.bashrc文件（推荐）

登录到你的用户（非root），在终端输入：
$ sudo gedit ~/.profile(or .bashrc)
可以在此文件末尾加入PATH的设置如下：
export PATH=”$PATH:your path1:your path2 ...”
保存文件，注销再登录，变量生效。
该方式添加的变量只对当前用户有效。

方法二：系统目录下的profile文件（谨慎）

在系统的etc目录下，有一个profile文件，编辑该文件：
$ sudo gedit /etc/profile
在最后加入PATH的设置如下：
export PATH=”$PATH:your path1:your path2 ...”
该文件编辑保存后，重启系统，变量生效。
该方式添加的变量对所有的用户都有效。

方法三：系统目录下的 environment 文件（谨慎）

在系统的etc目录下，有一个environment文件，编辑该文件：
$ sudo gedit /etc/environment
找到以下的 PATH 变量：
PATH="<......>"
修改该 PATH 变量，在其中加入自己的path即可，例如：
PATH="<......>:your path1:your path2 …"
各个path之间用冒号分割。该文件也是重启生效，影响所有用户。
注意这里不是添加export PATH=… 。

方法四：直接在终端下输入

$ sudo export PATH="$PATH:your path1:your path2 …"
这种方式变量立即生效，但用户注销或系统重启后设置变成无效，适合临时变量的设置。

注 意：方法二和三的修改需要谨慎，尤其是通过root用户修改，如果修改错误，将可能导致一些严重的系统错误。因此笔者推荐使用第一种方法。另外嵌入式 Linux的开发最好不要在root下进行（除非你对Linux已经非常熟悉了！！），以免因为操作不当导致系统严重错误。

下面是一个对environment文件错误修改导致的问题以及解决方法示例：

问题：因为不小心在 etc/environment里设在环境变量导致无法登录
提示：不要在 etc/environment里设置 export PATH这样会导致重启后登录不了系统
解决方法：
在登录界面 alt +ctrl+f1进入命令模式，如果不是root用户需要键入（root用户就不许这么罗嗦，gedit编辑会不可显示）
/usr/bin/sudo /usr/bin/vi /etc/environment
光标移到export PATH** 行，连续按 d两次删除该行；
输入:wq保存退出；
然后键入/sbin/reboot重启系统（可能会提示need to boot，此时直接power off）

## Debian常见命令

|  |  |  |  |
| :------: | :------: | :------: | :------: |
| 分类 | 重要性 | 命令 | 描述 |
|  | • | apropos whatis | 显示和word相关的命令。 参见线程安全 |
|  | • | man -t man | ps2pdf - > man.pdf | 生成一个PDF格式的帮助文件 |
|  |   | which command | 显示命令的完整路径名 |
|  |   | time command | 计算命令运行的时间 |
|  | • | time cat | 开始计时. Ctrl-d停止。参见sw |
|  | • | nice info | 运行一个低优先级命令（这里是info） |
|  | • | renice 19 -p $$ | 使脚本运行于低优先级。用于非交互任务。 |
| 目录操作 |  |  |  |
|  | • | cd - | 回到前一目录 |
|  | • | cd | 回到用户目录 |
|  |   | cd dir && command | 进入目录dir，执行命令command然后回到当前目录 |
|  | • | pushd . | 将当前目录压入栈，以后你可以使用popd回到此目录 |
| 文件搜索 |  |  |  |
|  | • | alias l='ls -l --color=auto' | 单字符文件列表命令 |
|  | • | ls -lrt | 按日期显示文件. 参见newest |
|  | • | ls /usr/bin | pr -T9 -W$COLUMNS | 在当前终端宽度上打印9列输出 |
|  |   | find -name '*.[ch]' | xargs grep -E 'expr' | 在当前目录及其子目录下所有.c和.h文件中寻找'expr'. 参见findrepo |
|  |   | find -type f -print0 | xargs -r0 grep -F 'example' | 在当前目录及其子目录中的常规文件中查找字符串'example' |
|  |   | find -maxdepth 1 -type f | xargs grep -F 'example' | 在当前目录下查找字符串'example' |
|  |   | find -maxdepth 1 -type d | while read dir; do echo $dir; echo cmd2; done | 对每一个找到的文件执行多个命令使用while循环 |
|  | • | find -type f ! -perm -444 | 寻找所有不可读的文件对网站有用 |
|  | • | find -type d ! -perm -111 | 寻找不可访问的目录对网站有用 |
|  | • | locate -r 'file[^/]*\.txt' | 使用locate 查找所有符合*file*.txt的文件 |
|  | • | look reference | 在（有序）字典中快速查找 |
|  | • | grep --color reference /usr/share/dict/words | 使字典中匹配的正则表达式高亮 |
| 归档 and compression |  |  |  |
|  |   | gpg -c file | 文件加密 |
|  |   | gpg file.gpg | 文件解密 |
|  |   | tar -c dir/ | bzip2 > dir.tar.bz2 | 将目录dir/压缩打包 |
|  |   | bzip2 -dc dir.tar.bz2 | tar -x | 展开压缩包 对tar.gz文件使用gzip而不是bzip2 |
|  |   | tar -c dir/ | gzip | gpg -c | ssh user@remote 'dd of=dir.tar.gz.gpg' | 目录dir/压缩打包并放到远程机器上 |
|  |   | find dir/ -name '*.txt' | tar -c --files-from=- | bzip2 > dir_txt.tar.bz2 | 将目录dir/及其子目录下所有.txt文件打包 |
|  |   | find dir/ -name '*.txt' | xargs cp -a --target-directory=dir_txt/ --parents | 将目录dir/及其子目录下所有.txt按照目录结构拷贝到dir_txt/ |
|  |   |  tar -c /dir/to/copy  |  cd /where/to/ && tar -x -p  | 拷贝目录copy/到目录/where/to/并保持文件属性 |
|  |   |  cd /dir/to/copy && tar -c .  |  cd /where/to/ && tar -x -p  | 拷贝目录copy/下的所有文件到目录/where/to/并保持文件属性 |
|  |   |  tar -c /dir/to/copy  | ssh -C user@remote 'cd /where/to/ && tar -x -p' | 拷贝目录copy/到远程目录/where/to/并保持文件属性 |
|  |   | dd bs=1M if=/dev/sda | gzip | ssh user@remote 'dd of=sda.gz' | 将整个硬盘备份到远程机器上 |
|  |  | rsync 使用 --dry-run选项进行测试 |  |
|  |   | rsync -P rsync://rsync.server.com/path/to/file file | 只获取diffs.当下载有问题时可以作多次 |
|  |   | rsync --bwlimit=1000 fromfile tofile | 有速度限制的本地拷贝，对I/O有利 |
|  |   | rsync -az -e ssh --delete ~/public_html/ remote.com:'~/public_html' | 镜像网站使用压缩和加密 |
|  |   | rsync -auz -e ssh remote:/dir/ . && rsync -auz -e ssh . remote:/dir/ | 同步当前目录和远程目录 |
| ssh 安全 Shell |  |  |  |
|  |   | ssh $USER@$HOST command | 在$Host主机上以$User用户运行命令默认命令为Shell |
|  | • | ssh -f -Y $USER@$HOSTNAME xeyes | 在名为$HOSTNAME的主机上以$USER用户运行GUI命令 |
|  |   | scp -p -r $USER@$HOST: file dir/ | 拷贝到$HOST主机$USER'用户的目录下 |
|  |   | ssh -g -L 8080:localhost:80 root@$HOST | 由本地主机的8080端口转发到$HOST主机的80端口 |
|  |   | ssh -R 1434:imap:143 root@$HOST | 由主机的1434端口转发到imap的143端口 |
| wget 多用途下载工具 |  |  |  |
|  | • | cd cmdline && wget -nd -pHEKk <http://www.pixelbeat.org/cmdline.html> | 在当前目录中下载指定网页及其相关的文件使其可完全浏览 |
|  |   | wget -c <http://www.example.com/large.file | 继续上次未完的下载 |
|  |   | wget -r -nd -np -l1 -A '*.jpg' http://www.example.com/ | 批量下载文件到当前目录中 |
|  |   | wget ftp://remote/file[1-9].iso/ | 下载FTP站上的整个目录 |
|  | • | wget -q -O- http://www.pixelbeat.org/timeline.html | grep 'a href' | head | 直接处理输出 |
|  |   | echo 'wget url' | at 01:00 | 在下午一点钟下载指定文件到当前目录 |
|  |   | wget --limit-rate=20k url | 限制下载速度这里限制到20KB/s |
|  |   | wget -nv --spider --force-html -i bookmarks.html | 检查文件中的链接是否存在 |
|  |   | wget --mirror http://www.example.com/ | 更新网站的本地拷贝可以方便地用于cron |
| 网络ifconfig, route, mii-tool, nslookup 命令皆已过时 |  |  |  |
|  |   | ethtool eth0 | 显示网卡eth0的状态 |
|  |   | ethtool --change eth0 autoneg off speed 100 duplex full | 手动设制网卡速度 |
|  |   | iwconfig eth1 | 显示无线网卡eth1的状态 |
|  |   | iwconfig eth1 rate 1Mb/s fixed | 手动设制无线网卡速度 |
|  | • | iwlist scan | 显示无线网络列表 |
|  | • | ip link show | 显示interface列表 |
|  |   | ip link set dev eth0 name wan | 重命名eth0为wan |
|  |   | ip link set dev eth0 up | 启动interface eth0或关闭 |
|  | • | ip addr show | 显示网卡的IP地址 |
|  |   | ip addr add 1.2.3.4/24 brd + dev eth0 | 添加ip和掩码255.255.255.0 |
|  | • | ip route show | 显示路由列表 |
|  |   | ip route add default via 1.2.3.254 | 设置默认网关1.2.3.254 |
|  | • | tc qdisc add dev lo root handle 1:0 netem delay 20msec | 增加20ms传输时间到loopback设备调试用 |
|  | • | tc qdisc del dev lo root | 移除上面添加的传输时间 |
|  | • | host pixelbeat.org | 查寻主机的DNS IP地址 |
|  | • | hostname -i | 查寻本地主机的IP地址同等于host `hostname` |
|  | • | whois pixelbeat.org | 查寻某主机或莫IP地址的whois信息 |
|  | • | netstat -tupl | 列出系统中的internet服务 |
|  | • | netstat -tup | 列出活跃的连接 |
| windows networking samba提供所有windows相关的网络支持 |  |  |  |
|  | • | smbtree | 寻找一个windows主机. 参见findsmb |
|  |   | nmblookup -A 1.2.3.4 | 寻找一个指定ip的windows netbios名 |
|  |   | smbclient -L windows_box | 显示在windows主机或samba服务器上的所有共享 |
|  |   | mount -t smbfs -o fmask=666,guest //windows_box/share /mnt/share | 挂载一个windows共享 |
|  |   | echo 'message' | smbclient -M windows_box | 发送一个弹出信息到windows主机XP sp2默认关闭此功能 |
| 文本操作 sed使用标准输入和标准输出，如果想要编辑文件，则需添加\<oldfile>newfile |  |  |  |
|  |   | sed 's/string1/string2/g' | 使用string2替换string1 |
|  |   | sed 's/\.*\1/\12/g' | 将任何以1结尾的字符串替换为以2结尾的字符串 |
|  |   | sed '/^ *#/d; /^ *$/d' | 删除注释和空白行 |
|  |   | sed ':a; /\\$/N; s/\\\n//; ta' | 连接结尾有\的行和其下一行 |
|  |   | sed 's/[ \t]*$//' | 删除每行后的空白 |
|  |   | sed 's/\[\\`\\"$\\\\]\/\\\1/g' | 将所有转义字符之前加上\ |
|  | • | seq 10 | sed "s/^/      /; s/ *\.\{7,\}\/\1/" | 向右排N任意数列 |
|  |   | sed -n '1000p;1000q' | 输出第一千行 |
|  |   | sed -n '10,20p;20q' | 输出第10-20行 |
|  |   | sed -n 's/.*<title\>\.*\<\/title>.*/\1/ip;T;q' | 输出HTML文件的<title></title>字段中的 内容 |
|  |   | sort -t. -k1,1n -k2,2n -k3,3n -k4,4n | 排序IPV4地址 |
|  | • | echo 'Test' | tr '[:lower:]' '[:upper:]' | 转换成大写 |
|  | • | tr -dc '[:print:]' < /dev/urandom | 过滤掉不能打印的字符 |
|  | • | history | wc -l | 计算指定单词出现的次数 |
| 集合操作 如果是英文文本的话export LANG=C可以提高速度 |  |  |  |
|  |   | sort -u file1 file2 | 两个未排序文件的并集 |
|  |   | sort file1 file2 | uniq -d | 两个未排序文件的交集 |
|  |   | sort file1 file1 file2 | uniq -u | 两个未排序文件的差 集 |
|  |   | sort file1 file2 | uniq -u | 两个未排序文件的对称差集 |
|  |   | join -t'\0' -a1 -a2 file1 file2 | 两个有序文件的并集 |
|  |   | join -t'\0' file1 file2 | 两个有序文件的交集 |
|  |   | join -t'\0' -v2 file1 file2 | 两个有序文件的差集 |
|  |   | join -t'\0' -v1 -v2 file1 file2 | 两个有序文件的对称差集 |
| 数学 |  |  |  |
|  | • | echo '1 + sqrt5/2' | bc -l | 方便的计算器计算 φ |
|  | • | echo 'pad=20; min=64; 100*10^6/pad+min*8' | bc | 更复杂地计算，这里计算了最大的FastE包率 |
|  | • | echo 'pad=20; min=64; print 100E6/pad+min*8' | python | Python处理数值的科学表示法 |
|  | • | echo 'pad=20; plot [64:1518] 100*10**6/pad+x*8' | gnuplot -persist | 显示FastE包率相对于包大小的图形 |
|  | • | echo 'obase=16; ibase=10; 64206' | bc | 进制转换十进制到十六进制 |
|  | • | echo $0x2dec | 进制转换十六进制到十进制shell数学扩展 |
|  | • | units -t '100m/9.58s' 'miles/hour' | 单位转换公尺到英尺 |
|  | • | units -t '500GB' 'GiB' | 单位转换SI 到IEC 前缀. 参见 numfmt |
|  | • | units -t '1 googol' | 定义查找 |
|  | • | seq 100 | tr '\n' +; echo 0 | bc | 加N任意数列. 参见 add and funcpy |
| 日历 |  |  |  |
|  | • | cal -3 | 显示一日历 |
|  | • | cal 9 1752 | 显示指定月，年的日历 |
|  | • | date -d fri | 这个星期五是几号. 参见day |
|  | • | date --date='25 Dec' +%A | 今年的圣诞节是星期几 |
|  | • | date --date '1970-01-01 UTC 2147483647 seconds' | 将一相对于1970-01-01 00：00的秒数转换成时间 |
|  | • | TZ=':America/Los_Angeles' date | 显示当前的美国西岸时间使用tzselect寻找时区 |
|  |   | echo "mail -s 'get the train' P@draigBrady.com < /dev/null" | at 17:45 | 在指定的时间发送邮件 |
|  | • | echo "DISPLAY=$DISPLAY xmessage cooker" | at "NOW + 30 minutes" | 在给定的时间弹出对话框 |
| locales |  |  |  |
|  | • | printf "%'d\n" 1234 | 根据locale输出正确的数字分隔 |
|  | • | BLOCK_SIZE=\'1 ls -l | 用ls命令作类适于locale文件分组 |
|  | • | echo "I live in `locale territory`" | 从locale数据库中展开信息 |
|  | • | LANG=en_IE.utf8 locale int_prefix | 查找指定地区的locale信息。参见ccodes |
|  | • | locale | cut -d= -f1 | xargs locale -kc | less | 显示在locale数据库中的所有字段 |
| recode iconv, dos2unix, unix2dos 已经过时了 |  |  |  |
|  | • | recode -l | less | 显示所有有效的字符集及其别名 |
|  |   | recode windows-1252.. file_to_change.txt | 转换Windows下的ansi文件到当前的字符集自动进行回车换行符的转换 |
|  |   | recode utf-8/CRLF.. file_to_change.txt | 转换Windows下的ansi文件到当前的字符集 |
|  |   | recode iso-8859-15..utf8 file_to_change.txt | 转换Latin9（西欧）字符集文件到utf8 |
|  |   | recode ../b64 < file.txt > file.b64 | Base64编码 |
|  |   | recode /qp.. < file.txt > file.qp | Quoted-printable格式解码 |
|  |   | recode ..HTML < file.txt > file.html | 将文本文件转换成HTML |
|  | • | recode -lf windows-1252 | grep euro | 在字符表中查找欧元符号 |
|  | • | echo -n 0x80 | recode latin-9/x1..dump | 显示字符在latin-9中的字符映射 |
|  | • | echo -n 0x20AC | recode ucs-2/x2..latin-9/x | 显示latin-9编码 |
|  | • | echo -n 0x20AC | recode ucs-2/x2..utf-8/x | 显示utf-8编码 |
| 光盘 |  |  |  |
|  |   | gzip < /dev/cdrom > cdrom.iso.gz | 保存光盘拷贝 |
|  |   | mkisofs -V LABEL -r dir | gzip > cdrom.iso.gz | 建立目录dir的光盘镜像 |
|  |   | mount -o loop cdrom.iso /mnt/dir | 将光盘镜像挂载到 /mnt/dir 只读 |
|  |   | cdrecord -v dev=/dev/cdrom blank=fast | 清空一张CDRW |
|  |   | gzip -dc cdrom.iso.gz | cdrecord -v dev=/dev/cdrom - | 烧录光盘镜像 使用 dev=ATAPI -scanbus 来确认该使用的 dev |
|  |   | cdparanoia -B | 在当前目录下将光盘音轨转录成wav文件 |
|  |   | cdrecord -v dev=/dev/cdrom -audio *.wav | 将当前目录下的wav文件烧成音乐光盘 参见cdrdao |
|  |   | oggenc --tracknum='track' track.cdda.wav -o 'track.ogg' | 将wav文件转换成ogg格式 |
| 磁盘空间 参见FSlint |  |  |  |
|  | • | ls -lSr | 按文件大小降序显示文件 |
|  | • | du -s * | sort -k1,1rn | head | 显示当前目录下占用空间最大的一批文件. 参见dutop |
|  | • | df -h | 显示空余的磁盘空间 |
|  | • | df -i | 显示空余的inode |
|  | • | fdisk -l | 显示磁盘分区大小和类型（在root下执行） |
|  | • | rpm -q -a --qf '%10{SIZE}\t%{NAME}\n' | sort -k1,1n | 显示所有在rpm发布版上安装的包，并以包字节大小为序 |
|  | • | dpkg-query -W -f='${Installed-Size;10}\t${Package}\n' | sort -k1,1n | 显示所有在deb发布版上安装的包，并以KB包大小为序 |
|  | • | dd bs=1 seek=2TB if=/dev/null of=ext3.test | 建立一个大的测试文件（不占用空间）. 参见truncate |
| 监视/调试 |  |  |  |
|  | • | tail -f /var/log/messages | 监视Messages日志文件 |
|  | • | strace -c ls >/dev/null | 总结/剖析命令进行的系统调用 |
|  | • | strace -f -e open ls >/dev/null | 显示命令进行的系统调用 |
|  | • | ltrace -f -e getenv ls >/dev/null | 显示命令调用的库函数 |
|  | • | lsof -p $$ | 显示当前进程打开的文件 |
|  | • | lsof ~ | 显示打开用户目录的进程 |
|  | • | tcpdump not port 22 | 显示除了ssh外的网络交通. 参见tcpdump_not_me |
|  | • | ps -e -o pid,args --forest | 以树状结构显示进程 |
|  | • | ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu | sed '/^ 0.0 /d' | 以CPU占用率为序显示进程 |
|  | • | ps -e -orss=,args= | sort -b -k1,1n | pr -TW$COLUMNS | 以内存使用量为序显示进程. 参见ps_mem.py |
|  | • | ps -C firefox-bin -L -o pid,tid,pcpu,state | 显示指定进程的所有线程信息 |
|  | • | ps -p 1,2 | 显示指定进程ID的进程信息 |
|  | • | last reboot | 显示系统重启记录 |
|  | • | free -m | 显示剩余的内存总量-m以MB为单位显示 |
|  | • | watch -n.1 'cat /proc/interrupts' | 监测文件/proc/interrupts的变化 |
| 系统信息 参见sysinfo |  |  |  |
|  | • | uname -a | 查看内核/操作系统/CPU信息 |
|  | • | head -n1 /etc/issue | 查看操作系统版本 |
|  | • | cat /proc/partitions | 显示所有在系统中注册的分区 |
|  | • | grep MemTotal /proc/meminfo | 显示系统可见的内存总量 |
|  | • | grep "model name" /proc/cpuinfo | 显示CPU信息 |
|  | • | lspci -tv | 显示PCI信息 |
|  | • | lsusb -tv | 显示USB信息 |
|  | • | mount | column -t | 显示所有挂载的文件系统并对齐输出 |
|  | # | dmidecode -q | less | 显示SMBIOS/DMI 信息 |
|  | # | smartctl -A /dev/sda | grep Power_On_Hours | 系统开机的总体时间 |
|  | # | hdparm -i /dev/sda | 显示关于磁盘sda的信息 |
|  | # | hdparm -tT /dev/sda | 检测磁盘sda的读取速度 |
|  | # | badblocks -s /dev/sda | 检测磁盘sda上所有的坏扇区 |
| 交互 参见linux keyboard shortcut database |  |  |  |
|  | • | readline | Line editor used by bash, python, bc, gnuplot, ... |
|  | • | screen | 多窗口的虚拟终端, ... |
|  | • | mc | 强大的文件管理器，可以浏览rpm, tar, ftp, ssh, ... |
|  | • | gnuplot | 交互式并可进行脚本编程的画图工具 |
|  | • | links | 网页浏览器 |
| miscellaneous |  |  |  |
|  | • | alias hd='od -Ax -tx1z -v' | 方便的十六进制输出。 用法举例: • hd /proc/self/cmdline | less |
|  | • | alias realpath='readlink -f' | 显示符号链接指向的真实路径用法举例: • realpath ~/../$USER |
|  | • | set | grep $USER | 在当前环境中查找 |
|  |   | touch -c -t 0304050607 file | 改变文件的时间标签 YYMMDDhhmm |
|  | • | python -m SimpleHTTPServer | Serve current directory tree at http://$HOSTNAME:8000/ |

## Centos

```repo
 更改yum源为阿里云

1、备份
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
2、下载新的CentOS-Base.repo 到/etc/yum.repos.d/
    CentOS 5
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
    CentOS 6
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
    CentOS 7
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

更改yum源为163

 首先备份/etc/yum.repos.d/CentOS-Base.repo
 mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
 下载对应版本repo文件, 放入/etc/yum.repos.d/(操作前请做好相应备份)
 CentOS7 http://mirrors.163.com/.help/CentOS7-Base-163.repo
 CentOS6 http://mirrors.163.com/.help/CentOS6-Base-163.repo
 CentOS5 http://mirrors.163.com/.help/CentOS5-Base-163.repo
 运行以下命令生成缓存
 yum clean all
 yum makecache fast
3、之后运行yum makecache生成缓存
    yum -y upgrade  #只升级所有包，不升级软件和系统内核
    yum -y update   #升级所有包同时也升级软件和系统内核
在线安装epel源
    rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    To enable the EPEL repositories in CentOS/RHEL 7.x enter following command:
    yum -y install epel-release
```

## Linux安装java

```sh
在CentOS7安装Oracle Java JDK8
     rpm -qa | grep -E '^open[jre|jdk]|j[re|dk]'    # 查找已安装的jdk组件
     yum remove java-$Version   # 卸载之前已安装的jdk
    在/usr目录创建java文件夹，下载jdk包http://www.oracle.com/technetwork/java/javase/downloads/index.html
     mkdir /usr/java
     wget http://download.oracle.com/otn-pub/java/jdk/8u161-b12/...tar.gz
     tar -zxvf jdk...
    编辑.bashrc文件，在export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL下面添加如下代码：
     export JAVA_HOME=/usr/java/jdk-$version
     export PATH=$JAVA_HOME/bin:$PATH
     export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    配置生效并验证
     source .bashrc
     java --version
```

## 分区方案参考

```sh

http://blog.51cto.com/oldboy/634725

```

## centos完全重装python和yum

```py
1、删除现有Python
rpm -qa|grep python| xargs rpm -ev --allmatches --nodeps ##强制删除已安装程序及其关联
whereis python | xargs rm -frv ##删除所有残余文件 ##xargs，允许你对输出执行其他某些命令
whereis python ##验证删除，返回无结果
2、删除现有的yum
rpm -qa|grep yum| xargs rpm -ev --allmatches --nodeps
whereis yum | xargs rm -frv
3、从http://mirrors.aliyun.com/centos/7/os/x86_64/Packages/下载相应的包
由于源中版本会更新，具体请查看URL中的版本再下载下来！
rpm -Uvh --replacepkgs *.rpm
可能之间还需要zlib和zlib-devel包，根据情况下载并安装！
3、运行python进行测试
[root@localhost ~]# python
Python 2.7.5 (default, Aug  4 2017, 00:39:18)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-16)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import yum
>>>
如上，要是什么都没报，则说明OK啦~
源码编译安装yum
wget http://yum.baseurl.org/download/3.4/yum-3.4.3.tar.gz
tar xvf yum-3.4.3.tar.gz
cd yum-3.4.3
yummain.py install yum
结果提示错误： CRITICAL:yum.cli:Config Error: Error accessing file for config file:///etc/
缺少配置文件。在etc目录下面新建yum.conf文件，然后再次运行 yummain.py install yum，顺利完成安装。
```

###　更改默认ssh连接端口

```sh
1、防火墙开放端口

在这里我们是要将默认的ssh端口22修改为2121，所以要将2121端口在防火墙打开

/sbin/iptables -I INPUT -p tcp --dport 2121 -j ACCEPT

/etc/rc.d/init.d/iptables save

sed -i "/A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT/d" /etc/sysconfig/iptables

service iptables restart

2、修改sshd_config文件，将#Port 22直接替换成Port 2121

sed -i 's/#Port 22/Port 2121/' /etc/ssh/sshd_config

3、重启SSH服务

service sshd restart

这个时候就无法使用22端口进行ssh远程连接，必须使用2121端口
```

## 优化内核参数 [根据实际情况调整]

两种修改内核参数方法:
1、使用echo value方式直接追加到文件里如echo "1" >/proc/sys/net/ipv4/tcp_syn_retries，但这种方法设备重启后又会恢复为默认值

2、把参数添加到/etc/sysctl.conf中，然后执行sysctl -p使参数生效，永久生效

内核生产环境常用优化参数参考，具体优化值要参考应用场景

```sh
net.ipv4.tcp_syn_retries = 1

net.ipv4.tcp_synack_retries = 1

net.ipv4.tcp_keepalive_time = 600

net.ipv4.tcp_keepalive_probes = 3

net.ipv4.tcp_keepalive_intvl =15

net.ipv4.tcp_retries2 = 5

# 增加tcp-time-wait存储桶大小以防止简单的DOS攻击
net.ipv4.tcp_max_tw_buckets = 36000
net.ipv4.tcp_tw_recycle = 1 # 默认为0
net.ipv4.tcp_tw_reuse = 1 # 默认为0

net.ipv4.tcp_max_orphans = 32768

net.ipv4.tcp_syncookies = 1

net.ipv4.tcp_max_syn_backlog = 16384

net.ipv4.tcp_wmem = 8192 131072 16777216
net.ipv4.udp_wmem_min = 16384   # 增加可分配的写缓冲区空间

net.ipv4.tcp_rmem = 32768 131072 16777216

net.ipv4.tcp_mem = 786432 1048576 1572864

net.ipv4.ip_local_port_range = 1024 65000

net.ipv4.ip_conntrack_max = 65536
-
net.ipv4.netfilter.ip_conntrack_max=65536

net.ipv4.netfilter.ip_conntrack_tcp_timeout_established=180

net.core.somaxconn = 16384

net.core.netdev_max_backlog = 16384
net.core.optmem_max = 25165824  # 增加内存缓冲区的最大数量
net.core.busy_poll = 50 # 轮询有助于减少网络接收路径中的延迟 允许套接字层代码轮询网络设备的接收队列， 并禁用网络中断。
```

```sh
#优化TCP
vi /etc/sysctl.conf
#禁用包过滤功能
net.ipv4.ip_forward = 0
#启用源路由核查功能
net.ipv4.conf.default.rp_filter = 1
#禁用所有IP源路由
net.ipv4.conf.default.accept_source_route = 0
#使用sysrq组合键是了解系统目前运行情况，为安全起见设为0关闭
kernel.sysrq = 0
#控制core文件的文件名是否添加pid作为扩展
kernel.core_uses_pid = 1
#开启SYN Cookies，当出现SYN等待队列溢出时，启用cookies来处理
net.ipv4.tcp_syncookies = 1
#每个消息队列的大小（单位：字节）限制
kernel.msgmnb = 65536
#整个系统最大消息队列数量限制
kernel.msgmax = 65536
#单个共享内存段的大小（单位：字节）限制，计算公式64G*1024*1024*1024(字节)
kernel.shmmax = 68719476736
#所有内存大小（单位：页，1页 = 4Kb），计算公式16G*1024*1024*1024/4KB(页)
kernel.shmall = 4294967296
#timewait的数量，默认是180000
net.ipv4.tcp_max_tw_buckets = 6000
#开启有选择的应答
net.ipv4.tcp_sack = 1
#支持更大的TCP窗口. 如果TCP窗口最大超过65535(64K), 必须设置该数值为1
net.ipv4.tcp_window_scaling = 1
#TCP读buffer
net.ipv4.tcp_rmem = 4096 131072 1048576
#TCP写buffer
net.ipv4.tcp_wmem = 4096 131072 1048576
#为TCP socket预留用于发送缓冲的内存默认值（单位：字节）
net.core.wmem_default = 8388608
#为TCP socket预留用于发送缓冲的内存最大值（单位：字节）
net.core.wmem_max = 16777216
#为TCP socket预留用于接收缓冲的内存默认值（单位：字节）
net.core.rmem_default = 8388608
#为TCP socket预留用于接收缓冲的内存最大值（单位：字节）
net.core.rmem_max = 16777216
#每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许送到队列的数据包的最大数目
net.core.netdev_max_backlog = 262144
#web应用中listen函数的backlog默认会给我们内核参数的net.core.somaxconn限制到128，而nginx定义的NGX_LISTEN_BACKLOG默认为511，所以有必要调整这个值
net.core.somaxconn = 262144
#系统中最多有多少个TCP套接字不被关联到任何一个用户文件句柄上。这个限制仅仅是为了防止简单的DoS攻击，不能过分依靠它或者人为地减小这个值，更应该增加这个值(如果增加了内存之后)
net.ipv4.tcp_max_orphans = 3276800
#记录的那些尚未收到客户端确认信息的连接请求的最大值。对于有128M内存的系统而言，缺省值是1024，小内存的系统则是128
net.ipv4.tcp_max_syn_backlog = 262144
#时间戳可以避免序列号的卷绕。一个1Gbps的链路肯定会遇到以前用过的序列号。时间戳能够让内核接受这种“异常”的数据包。这里需要将其关掉
net.ipv4.tcp_timestamps = 0
#为了打开对端的连接，内核需要发送一个SYN并附带一个回应前面一个SYN的ACK。也就是所谓三次握手中的第二次握手。这个设置决定了内核放弃连接之前发送SYN+ACK包的数量
net.ipv4.tcp_synack_retries = 1
# 使用cookie来处理SYN队列溢出
net.ipv4.tcp_syncookies = 1 (0 by default)
# 路由刷新频率
net.ipv4.route.gc_timeout = 100
#在内核放弃建立连接之前发送SYN包的数量
net.ipv4.tcp_syn_retries = 1
#开启TCP连接中time_wait sockets的快速回收
net.ipv4.tcp_tw_recycle = 1
#开启TCP连接复用功能，允许将time_wait sockets重新用于新的TCP连接（主要针对time_wait连接）
net.ipv4.tcp_tw_reuse = 1
#1st低于此值,TCP没有内存压力,2nd进入内存压力阶段,3rdTCP拒绝分配socket(单位：内存页)
net.ipv4.udp_mem = 65536 131072 262144  # 增加可分配的最大总缓冲区空间,此为以page为单位（4096字节）测量
net.ipv4.tcp_mem = 65536 131072 262144
#如果套接字由本端要求关闭，这个参数决定了它保持在FIN-WAIT-2状态的时间。对端可以出错并永远不关闭连接，甚至意外当机。缺省值是60 秒。2.2 内核的通常值是180秒，你可以按这个设置，但要记住的是，即使你的机器是一个轻载的WEB服务器，也有因为大量的死套接字而内存溢出的风险，FIN- WAIT-2的危险性比FIN-WAIT-1要小，因为它最多只能吃掉1.5K内存，但是它们的生存期长些。
net.ipv4.tcp_fin_timeout = 15   # (默认60s，建议15-30s)
#表示当keepalive起用的时候，TCP发送keepalive消息的频度（单位：秒）
net.ipv4.tcp_keepalive_time = 300
# 确定了isAlive间隔探测之间的等待时间   (默认75s,建议15-30s)
net.ipv4.tcp_keepalive_intvl = 15
# 超时之前的探测次数    # 默认值：9，推荐5
net.ipv4.tcp_keepalive_probes = 5
#对外连接端口范围，RHEL 7 default: 32768 61000
net.ipv4.ip_local_port_range = 2048 65000
# 防止TCP时间等待
net.ipv4.tcp_rfc1337 = 1
#表示文件句柄的最大数量
fs.file-max = 102400

#iptables 防火墙
echo -e "net.nf_conntrack_max = 25000000" >> /etc/sysctl.conf
echo -e "net.netfilter.nf_conntrack_max = 25000000" >> /etc/sysctl.conf
echo -e "net.netfilter.nf_conntrack_tcp_timeout_established = 180" >> /etc/sysctl.conf
echo -e "net.netfilter.nf_conntrack_tcp_timeout_time_wait = 30" >> /etc/sysctl.conf
echo -e "net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60" >> /etc/sysctl.conf
echo -e "net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 30" >> /etc/sysctl.conf

```

优化参数详解：颜色表示常用优化参数
根据参数文件所处目录不同而进行分表整理

下列文件所在目录：/proc/sys/net/ipv4/

| 名称 | 默认值 | 建议值 | 描述 |
| :------: | :------: | :------: | :------: | :------: |
| `tcp_syn_retries` | 5 | 1 | 对于一个新建连接，内核要发送多少个 SYN 连接请求才决定放弃。不应该大于255，默认值是5，对应于180秒左右时间。。对于大负载而物理通信良好的网络而言,这个值偏高,可修改为2.这个值仅仅是针对对外的连接,对进来的连接,是由tcp_retries1决定的 |
| `tcp_synack_retries` | 5 | 1 | 对于远端的连接请求SYN，内核会发送SYN ＋ ACK数据报，以确认收到上一个 SYN连接请求包。这是所谓的三次握手 threeway handshake机制的第二个步骤。这里决定内核在放弃连接之前所送出的 SYN+ACK 数目。不应该大于255，默认值是5，对应于180秒左右时间。 |
| `tcp_keepalive_time` | 7200 | 600 | TCP发送keepalive探测消息的间隔时间（秒），用于确认TCP连接是否有效。 |
|  |  |  | 防止两边建立连接但不发送数据的攻击。 |
| `tcp_keepalive_probes` | 9 | 3 | TCP发送keepalive探测消息的间隔时间（秒），用于确认TCP连接是否有效。 |
| `tcp_keepalive_intvl` | 75 | 15 | 探测消息未获得响应时，重发该消息的间隔时间（秒）。默认值为75秒。 对于普通应用来说,这个值有一些偏大,可以根据需要改小.特别是web类服务器需要改小该值,15是个比较合适的值 |
| tcp_retries1 | 3 | 3 | 放弃回应一个TCP连接请求前﹐需要进行多少次重试。RFC 规定最低的数值是3 |
| `tcp_retries2` | 15 | 5 | 在丢弃激活已建立通讯状况的TCP连接之前﹐需要进行多少次重试。默认值为15，根据RTO的值来决定，相当于13-30分钟RFC1122规定，必须大于100秒.这个值根据目前的网络设置,可以适当地改小,我的网络内修改为了5 |
| tcp_orphan_retries | 7 | 3 | 在近端丢弃TCP连接之前﹐要进行多少次重试。默认值是7个﹐相当于 50秒 - 16分钟﹐视RTO 而定。如果您的系统是负载很大的web服务器﹐那么也许需要降低该值﹐这类 sockets 可能会耗费大量的资源。另外参的考tcp_max_orphans。事实上做NAT的时候,降低该值也是好处显著的,我本人的网络环境中降低该值为3 |
| `tcp_fin_timeout` | 60 | 2 | 对于本端断开的socket连接，TCP保持在FIN-WAIT-2状态的时间。对方可能会断开连接或一直不结束连接或不可预料的进程死亡。默认值为 60 秒。 |
| `tcp_max_tw_buckets` | 180000 | 36000 | 系统在同时所处理的最大 timewait sockets 数目。如果超过此数的话﹐time-wait socket 会被立即砍除并且显示警告信息。之所以要设定这个限制﹐纯粹为了抵御那些简单的 DoS 攻击﹐不过﹐如果网络条件需要比默认值更多﹐则可以提高它或许还要增加内存。事实上做NAT的时候最好可以适当地增加该值 |
| `tcp_tw_recycle` | 0 | 1 | 打开快速 TIME-WAIT sockets 回收。除非得到技术专家的建议或要求﹐请不要随意修改这个值。做NAT的时候，建议打开它 |
| `tcp_tw_reuse` | 0 | 1 | 表示是否允许重新应用处于TIME-WAIT状态的socket用于新的TCP连接这个对快速重启动某些服务,而启动后提示端口已经被使用的情形非常有帮助 |
| `tcp_max_orphans` | 8192 | 32768 | 系统所能处理不属于任何进程的TCP sockets最大数量。假如超过这个数量﹐那么不属于任何进程的连接会被立即reset，并同时显示警告信息。之所以要设定这个限制﹐纯粹为了抵御那些简单的 DoS 攻击﹐千万不要依赖这个或是人为的降低这个限制。如果内存大更应该增加这个值。这个值Redhat AS版本中设置为32768,但是很多防火墙修改的时候,建议该值修改为2000 |
| tcp_abort_on_overflow | 0 | 0 | 当守护进程太忙而不能接受新的连接，就象对方发送reset消息，默认值是false。这意味着当溢出的原因是因为一个偶然的猝发，那么连接将恢复状态。只有在你确信守护进程真的不能完成连接请求时才打开该选项，该选项会影响客户的使用。对待已经满载的sendmail,apache这类服务的时候,这个可以很快让客户端终止连接,可以给予服务程序处理已有连接的缓冲机会,所以很多防火墙上推荐打开它 |
| `tcp_syncookies` | 0 | 1 | 只有在内核编译时选择了CONFIG_SYNCOOKIES时才会发生作用。当出现syn等候队列出现溢出时象对方发送syncookies。目的是为了防止syn flood攻击。 |
| tcp_stdurg | 0 | 0 | 使用 TCP urg pointer 字段中的主机请求解释功能。大部份的主机都使用老旧的 BSD解释，因此如果您在 Linux 打开它﹐或会导致不能和它们正确沟通。 |
| `tcp_max_syn_backlog` | 1024 | 16384 | 对于那些依然还未获得客户端确认的连接请求﹐需要保存在队列中最大数目。对于超过 128Mb 内存的系统﹐默认值是 1024 ﹐低于 128Mb 的则为 128。如果服务器经常出现过载﹐可以尝试增加这个数字。警告﹗假如您将此值设为大于 1024﹐最好修改include/net/tcp.h里面的TCP_SYNQ_HSIZE﹐以保持TCP_SYNQ_HSIZE*16SYN Flood攻击利用TCP协议散布握手的缺陷，伪造虚假源IP地址发送大量TCP-SYN半打开连接到目标系统，最终导致目标系统Socket队列资源耗尽而无法接受新的连接。为了应付这种攻击，现代Unix系统中普遍采用多连接队列处理的方式来缓冲而不是解决这种攻击，是用一个基本队列处理正常的完全连接应用Connect和Accept ，是用另一个队列单独存放半打开连接。这种双队列处理方式和其他一些系统内核措施例如Syn-Cookies/Caches联合应用时，能够比较有效的缓解小规模的SYN Flood攻击事实证明 |
| tcp_window_scaling | 1 | 1 | 该文件表示设置tcp/ip会话的滑动窗口大小是否可变。参数值为布尔值，为1时表示可变，为0时表示不可变。tcp/ip通常使用的窗口最大可达到 65535 字节，对于高速网络，该值可能太小，这时候如果启用了该功能，可以使tcp/ip滑动窗口大小增大数个数量级，从而提高数据传输的能力RFC 1323。（对普通地百M网络而言，关闭会降低开销，所以如果不是高速网络，可以考虑设置为0） |
| tcp_timestamps | 1 | 1 | Timestamps 用在其它一些东西中﹐可以防范那些伪造的 sequence 号码。一条1G的宽带线路或许会重遇到带 out-of-line数值的旧sequence 号码假如它是由于上次产生的。Timestamp 会让它知道这是个 '旧封包'。该文件表示是否启用以一种比超时重发更精确的方法（RFC 1323）来启用对 RTT 的计算；为了实现更好的性能应该启用这个选项。 |
| tcp_sack | 1 | 1 | 使用 Selective ACK﹐它可以用来查找特定的遗失的数据报--- 因此有助于快速恢复状态。该文件表示是否启用有选择的应答（Selective Acknowledgment），这可以通过有选择地应答乱序接收到的报文来提高性能（这样可以让发送者只发送丢失的报文段）。对于广域网通信来说这个选项应该启用，但是这会增加对 CPU 的占用。 |
| tcp_fack | 1 | 1 | 打开FACK拥塞避免和快速重传功能。注意，当tcp_sack设置为0的时候，这个值即使设置为1也无效[这个是TCP连接靠谱的核心功能] |
| tcp_dsack | 1 | 1 | 允许TCP发送"两个完全相同"的SACK。 |
| tcp_ecn | 0 | 0 | TCP的直接拥塞通告功能。 |
| tcp_reordering | 3 | 6 | TCP流中重排序的数据报最大数量。 一般有看到推荐把这个数值略微调整大一些,比如5 |
| tcp_retrans_collapse | 1 | 0 | 对于某些有bug的打印机提供针对其bug的兼容性。一般不需要这个支持,可以关闭它 |
| `tcp_wmem：mindefaultmax` | 4096 | 8192 | 发送缓存设置 |
|  | 16384 | 131072 | min：为TCP socket预留用于发送缓冲的内存最小值。每个tcp socket都可以在建议以后都可以使用它。默认值为40964K。 |
|  | 131072 | 16777216 | default：为TCP socket预留用于发送缓冲的内存数量，默认情况下该值会影响其它协议使用的net.core.wmem_default 值，一般要低于net.core.wmem_default的值。默认值为1638416K。 |
|  |  |  | max: 用于TCP socket发送缓冲的内存最大值。该值不会影响net.core.wmem_max，"静态"选择参数SO_SNDBUF则不受该值影响。默认值为131072128K。（对于服务器而言，增加这个参数的值对于发送数据很有帮助,在我的网络环境中,修改为了51200 131072 204800） |
| `tcp_rmem：mindefaultmax` | 4096 | 32768 | 接收缓存设置 |
|  | 87380 | 131072 | 同tcp_wmem |
|  | 174760 | 16777216 |  |
| `tcp_mem：mindefaultmax` | 根据内存计算 | 786432 | low：当TCP使用了低于该值的内存页面数时，TCP不会考虑释放内存。即低于此值没有内存压力。理想情况下，这个值应与指定给 tcp_wmem 的第 2 个值相匹配 - 这第 2 个值表明，最大页面大小乘以最大并发请求数除以页大小 131072 * 300 / 4096。  |
|  |  | 1048576 1572864 | pressure：当TCP使用了超过该值的内存页面数量时，TCP试图稳定其内存使用，进入pressure模式，当内存消耗低于low值时则退出pressure状态。理想情况下这个值应该是 TCP 可以使用的总缓冲区大小的最大值 204800 * 300 / 4096。  |
|  |  |  | high：允许所有tcp sockets用于排队缓冲数据报的页面量。如果超过这个值，TCP 连接将被拒绝，这就是为什么不要令其过于保守 512000 * 300 / 4096 的原因了。 在这种情况下，提供的价值很大，它能处理很多连接，是所预期的 2.5 倍；或者使现有连接能够传输 2.5 倍的数据。 我的网络里为192000 300000 732000 |
|  |  |  | 一般情况下这些值是在系统启动时根据系统内存数量计算得到的。 |
| tcp_app_win | 31 | 31 | 保留maxwindow/2^tcp_app_win, mss数量的窗口由于应用缓冲。当为0时表示不需要缓冲。 |
| tcp_adv_win_scale | 2 | 2 | 计算缓冲开销bytes/2^tcp_adv_win_scale如果tcp_adv_win_scale > 0或者bytes-bytes/2^-tcp_adv_win_scale如果tcp_adv_win_scale BOOLEAN>0 |
| tcp_low_latency | 0 | 0 | 允许 TCP/IP 栈适应在高吞吐量情况下低延时的情况；这个选项一般情形是的禁用。但在构建Beowulf 集群的时候,打开它很有帮助 |
| tcp_westwood | 0 | 0 | 启用发送者端的拥塞控制算法，它可以维护对吞吐量的评估，并试图对带宽的整体利用情况进行优化；对于 WAN 通信来说应该启用这个选项。 |
| tcp_bic | 0 | 0 | 为快速长距离网络启用 Binary Increase Congestion；这样可以更好地利用以 GB 速度进行操作的链接；对于 WAN 通信应该启用这个选项。 |
| ip_forward | 0 | － | NAT必须开启IP转发支持，把该值写1 |
| `ip_local_port_range:minmax` | 32768 | 1024 | 表示用于向外连接的端口范围，默认比较小，这个范围同样会间接用于NAT表规模。 |
|  | 61000 | 65000 |  |
| `ip_conntrack_max` | 65535 | 65535 | 系统支持的最大ipv4连接数，默认65536（事实上这也是理论最大值），同时这个值和你的内存大小有关，如果内存128M，这个值最大8192，1G以上内存这个值都是默认65536 |

所处目录/proc/sys/net/ipv4/netfilter/

文件需要打开防火墙才会存在

| 名称 | 默认值 | 建议值 | 描述 |
| :------: | :------: | :------: | :------: | :------: |
| `ip_conntrack_max` | 65536 | 65536 | 系统支持的最大ipv4连接数，默认65536（事实上这也是理论最大值），同时这个值和你的内存大小有关，如果内存128M，这个值最大8192，1G以上内存这个值都是默认65536,这个值受/proc/sys/net/ipv4/ip_conntrack_max限制 |
| `ip_conntrack_tcp_timeout_established` | 432000 | 180 | 已建立的tcp连接的超时时间，默认432000，也就是5天。影响：这个值过大将导致一些可能已经不用的连接常驻于内存中，占用大量链接资源，从而可能导致NAT ip_conntrack: table full的问题。建议：对于NAT负载相对本机的 NAT表大小很紧张的时候，可能需要考虑缩小这个值，以尽早清除连接，保证有可用的连接资源；如果不紧张，不必修改 |
| `ip_conntrack_tcp_timeout_time_wait` | 120 | 120 | time_wait状态超时时间，超过该时间就清除该连接 |
| ip_conntrack_tcp_timeout_close_wait | 60 | 60 | close_wait状态超时时间，超过该时间就清除该连接 |
| ip_conntrack_tcp_timeout_fin_wait | 120 | 120 | fin_wait状态超时时间，超过该时间就清除该连接 |

文件所处目录/proc/sys/net/core/

| 名称 | 默认值 | 建议值 | 描述 |
| :------: | :------: | :------: | :------: | :------: |
| `netdev_max_backlog` | 1024 | 16384 | 每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许送到队列的数据包的最大数目，对重负载服务器而言，该值需要调高一点。 |
| `somaxconn` | 128 | 16384 | 用来限制监听LISTEN队列最大数据包的数量，超过这个数量就会导致链接超时或者触发重传机制。 |
|  |  |  | web应用中listen函数的backlog默认会给我们内核参数的net.core.somaxconn限制到128，而nginx定义的NGX_LISTEN_BACKLOG默认为511，所以有必要调整这个值。对繁忙的服务器,增加该值有助于网络性能 |
| `wmem_default` | 129024 | 129024 | 默认的发送窗口大小（以字节为单位） |
| rmem_default | 129024 | 129024 | 默认的接收窗口大小（以字节为单位） |
| `rmem_max` | 129024 | 873200 | 最大的TCP数据接收缓冲 |
| wmem_max | 129024 | 873200 | 最大的TCP数据发送缓冲 |

## 赋予root权限

```sh
方法一： 修改 /etc/sudoers 文件，找到%wheel一行，把前面的注释（#）去掉
Allows people in group wheel to run all commands
%wheel    ALL=(ALL)    ALL
然后修改用户，使其属于root组（wheel），命令如下：
#usermod -g root tommy
修改完毕，现在可以用tommy帐号登录，然后用命令 sudo su - ，即可获得root权限进行操作。
方法二： 修改 /etc/sudoers 文件，找到root一行，在root下面添加一行，如下所示：
Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
tommy   ALL=(ALL)     ALL
修改完毕，现在可以用tommy帐号登录，然后用命令 sudo su - ，即可获得root权限进行操作。
方法三： 修改 /etc/passwd 文件，找到如下行，把用户ID修改为 0 ，如下所示：
tommy:x:500:500:tommy:/home/tommy:/bin/bash
修改后如下
tommy:x:0:500:tommy:/home/tommy:/bin/bash
保存，用tommy账户登录后，直接获取的就是root帐号的权限。
建议使用方法二，不要轻易使用方法三。
```

## 服务器配置双机信任

```sh
服务器1的IP：192.168.1.1
服务器2的IP：192.168.1.2
在两台服务器上都按照以下步骤操作
# 创建公钥和密钥
ssh-keygen -t rsa
# 将自己的公钥追加到认证公钥
cat ~/.ssh/id_rsa.pub >> authorized_keys
在服务器1上操作
# 将服务器2的公钥追加到认证公钥
ssh 192.168.1.2 cat ~/.ssh/id_rsa.pub >> authorized_keys
在服务器2上操作
# 将服务器1的公钥追加到认证公钥
ssh 192.168.1.1 cat ~/.ssh/id_rsa.pub >> authorized_keys
测试
# 在服务器1上执行下面的命令，如果配置正确的话就无需输入密码也能显示服务器2的网卡信息
ssh 192.168.1.2 ifconfig
```

## 未采用双机热备防止服务中断--float IP

```sh
有两台Linux服务器，其中一台主机（IP：139.24.214.22）对外提供了一定的网络服务，另一台从机（IP：139.24.214.24）能提供相同的服务，但IP地址没有对外部公开。
客户端连接的都是139.24.214.22这个IP地址，如果主机故障，则会使网络服务暂时中断，时间越长造成损失越大，
由于没有采用双机热备份技术,考虑自己用Linux脚本来实现简单的浮动IP技术，当主机故障时从机获得139.24.214.22这个IP，暂时替代主机提供服务，当主机恢复时，从机自动释放这个IP。
思路：
利用单个网卡绑定多个IP地址的技术和crontab自动执行技术
为主机的网卡多绑定一个静态IP，如139.24.214.82,这个地址是便于从机判断的，
为从机的网卡多绑定一个动态IP，127.0.0.1，它在主机故障时将会被脚本修改为139.24.214.22
在从机上添加一个脚本 /root/autoFloatIP.sh，使用crontab技术让这个脚本每分钟执行一次，这个脚本的作用是判断主机的地址82能否Ping通，一旦不正常则将让自己的网卡多余的那个IP地址改为139.24.214.22，如果主机恢复，则将这个地址改回为127.0.0.1

步骤
1. 为主机添加一个静态IP139.24.214.82，由于这个是静态IP，可以采用在图形化界面中设置此IP并保存的办法，或者在/etc/sysconfig/network-scripts目录里面创建一个名为ifcfg-eth0:1的文件，内容为:

DEVICE=eth0:1
IPADDR=139.24.214.82
NETMASK= 255.255.255.0
ONBOOT= yes

2. 在从机上，在/root下建立一个脚本autoFloatIP.sh
用chmod +x autoFloatIP.sh让它可以执行，脚本的内容为

#!/bin/bash
c1=$(ping 139.24.214.82 -c 1|grep Unreachable|wc -l)
if [ $c1 -gt 0 ] ; then
c2=$(ping 139.24.214.82 -c 10|grep Unreachable|wc -l)
if [ $c2 -gt 9 ] ; then
    c3=$(ping 139.24.214.22 -c 10|grep Unreachable|wc -l)
      if [ $c3 -gt 9 ] ; then
         /sbin/ifconfig eth0:1 139.24.214.22 netmask 255.255.254.0
echo "float ip to 22"
      fi
fi
echo "can not connect"
else
c4=$(/sbin/ifconfig|grep 139.24.214.22|wc -l)
if [ $c4 -gt 0 ] ; then
    /sbin/ifconfig eth0:2 127.0.0.1 netmask 255.255.254.0
    echo "reset ip"
fi
echo "connection is ok"
fi

其中关键的命令为
/sbin/ifconfig eth0:1 139.24.214.22 netmask 255.255.254.0
/sbin/ifconfig eth0:2 127.0.0.1 netmask 255.255.254.0
用这个方法来动态修改IP，动态IP在电脑重启会消失

3. 从机上建立crontab
用crontab -e命令
让后加上这样的一行并保存
* * * * * /root/autoFloatIP.sh > /dev/null 2>&1
```

## 升级centos内核&更改为静态ip、网卡名称

```sh
1、导入key（内核3.0以上需要导入key）
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
当然，如果已经修改了repo的gpgcheck=0也可以不导入key
2、安装elrepo的yum源
To install ELRepo for RHEL-7, SL-7 or CentOS-7:
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm (external link)
To make use of our mirror system, please also install yum-plugin-fastestmirror.
To install ELRepo for RHEL-6, SL-6 or CentOS-6:
rpm -Uvh http://www.elrepo.org/elrepo-release-6-8.el6.elrepo.noarch.rpm
3、安装内核
在yum的ELRepo源中，有mainline颁布的，可以这样安装：
yum --enablerepo=elrepo-kernel install  kernel-ml-devel kernel-ml -y
当然也可以安装long term的：
yum --enablerepo=elrepo-kernel  install  kernel-lt -y
更改为静态IP：/etc/sysconfig/network-scripts/ifcfg-ens33
BOOTPROTO="static" #dhcp改为static
ONBOOT="yes" #开机启用本配置
IPADDR=192.168.7.106 #静态IP
GATEWAY=192.168.7.1 #默认网关
NETMASK=255.255.255.0 #子网掩码
DNS1=192.168.7.1 #DNS 配置
修改网卡名称样式为ethx：
1、编辑 grub 配置文件
vim /etc/sysconfig/grub   # 其实是/etc/default/grub的软连接
# 为GRUB_CMDLINE_LINUX变量增加2个参数，具体内容如下(加粗)：
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=cl/root rd.lvm.lv=cl/swap net.ifnames=0 biosdevname=0 rhgb quiet"
2、重新生成 grub 配置文件
grub2-mkconfig -o /boot/grub2/grub.cfg
然后重新启动 Linux 操作系统，通过 ip addr 可以看到网卡名称已经变为 eth0 。
3、修改网卡配置文件
原来网卡配置文件名称为 ifcfg-ens33，这里需要修改为 ethx 的格式，并适当调整网卡配置文件。
mv /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/sysconfig/network-scripts/ifcfg-eth0
# 修改ifcfg-eth0文件如下内容(其它内容不变)
NAME=eth0
DEVICE=eth0
[root@localhost ~]# systemctl restart network.service    # 重启网络服务
注意：ifcfg-ens33 文件最好删除掉，否则重启 network 服务时候会报错}
```

## SSH相关命令和配置

### SSH协议无密码登陆设定：

```sh
在本机上生成RSA密码对：公钥和私钥；将公钥复制到要ssh的远程主机的用户的家目录（以root用户为例）；
1，第一步生成密码对文件

[root@tangsir ~]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
57:67:db:f8:6d:dc:d0:f4:fc:9a:68:95:16:10:ec:ab

[root@tangsir ~]# ls ~/.ssh
id_rsa  id_rsa.pub  known_hosts

2,复制公钥到远程主机
[root@tangsir ~]# ls /root/.ssh
id_rsa  id_rsa.pub  known_hosts
[root@tangsir ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.1.101
21
root@192.168.1.101's password:
Now try logging into the machine, with "ssh 'root@192.168.1.101'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.

[root@tangsir ~]# ssh root@192.168.1.101
Last login: Tue May 21 08:02:43 2013 from 192.168.1.101
不用输入远程主机用户的密码
```

### sshd_config配置详解（/etc/ssh/sshd_config）

```sh
# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

#
# AddressFamily指定sshd应当使用哪种地址族。取值范围是："any"(默认)、"inet"(仅IPv4)、"inet6"(仅IPv6)。
#AddressFamily any

# 指定sshd监听的网络地址，默认监听所有地址。可以使用下面的格式：
# ListenAddress host|IPv4_addr|IPv6_addr
# ListenAddress host|IPv4_addr:port
# ListenAddress [host|IPv6_addr]:port
# 如果未指定 port ，那么将使用 Port 指令的值。
# 可以使用多个 ListenAddress 指令监听多个地址。
#ListenAddress 0.0.0.0
#ListenAddress ::

# HostKey
# 主机私钥文件的位置。如果权限不对，sshd可能会拒绝启动。
# SSH-1默认是 /etc/ssh/ssh_host_key 。
# SSH-2默认是 /etc/ssh/ssh_host_rsa_key 和 /etc/ssh/ssh_host_dsa_key 。
# 一台主机可以拥有多个不同的私钥。"rsa1"仅用于SSH-1，"dsa"和"rsa"仅用于SSH-2。
#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# 指定SSH-2允许使用的加密算法。多个算法之间使用逗号分隔。可以使用的算法如下：
# "aes128-cbc", "aes192-cbc", "aes256-cbc", "aes128-ctr", "aes192-ctr", "aes256-ctr",
# "3des-cbc", "arcfour128", "arcfour256", "arcfour", "blowfish-cbc", "cast128-cbc"
# 默认值是可以使用上述所有算法。
# Ciphers and keying
#RekeyLimit default none

# 指定sshd将日志消息通过哪个日志子系统(facility)发送。有效值是：DAEMON, USER, AUTH(默认), LOCAL0, LOCAL1, LOCAL2, LOCAL3, LOCAL4, LOCAL5, LOCAL6, LOCAL7
# Logging
#SyslogFacility AUTH

# 指定 sshd(8) 的日志等级(详细程度)。可用值如下：
# QUIET, FATAL, ERROR, INFO(默认), VERBOSE, DEBUG, DEBUG1, DEBUG2, DEBUG3
# DEBUG 与 DEBUG1 等价；DEBUG2 和 DEBUG3 则分别指定了更详细、更罗嗦的日志输出。
# 比 DEBUG 更详细的日志可能会泄漏用户的敏感信息，因此反对使用。
#LogLevel INFO

# Authentication:

# 限制用户必须在指定的时限内认证成功，0 表示无限制。默认值是 120 秒。
#LoginGraceTime 2m

# 是否允许 root 登录。可用值如下：
# "yes"(默认) 表示允许。"no"表示禁止。
# "without-password"表示禁止使用密码认证登录。
# "forced-commands-only"表示只有在指定了 command 选项的情况下才允许使用公钥认证登录。同时其它认证方法全部被禁止。这个值常用于做远程备份之类的事情。
#PermitRootLogin prohibit-password

# 指定是否要求sshd在接受连接请求前对用户主目录和相关的配置文件进行宿主和权限检查。强烈建议使用默认值"yes"来预防可能出现的低级错误
#StrictModes yes

# 指定每个连接最大允许的认证次数。默认值是 6 。
# 如果失败认证的次数超过这个数值的一半，连接将被强制断开，且会生成额外的失败日志消息。
#MaxAuthTries 6

#MaxSessions 10

# 是否允许公钥认证。仅可以用于SSH-2。默认值为"yes"。
#PubkeyAuthentication yes

######################################################################################################################

# 存放该用户可以用来登录的 RSA/DSA 公钥。
# 该指令中可以使用下列根据连接时的实际情况进行展开的符号：
# %% 表示'%'、%h 表示用户的主目录、%u 表示该用户的用户名。
# 经过扩展之后的值必须要么是绝对路径，要么是相对于用户主目录的相对路径。
# 默认值是".ssh/authorized_keys"
# Expect .ssh/authorized_keys2 to be disregarded by default in future.
#AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2

######################################################################################################################

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# 是否使用强可信主机认证(通过检查远程主机名和关联的用户名进行认证)。仅用于SSH-1。
# 这是通过在RSA认证成功后再检查 ~/.rhosts 或 /etc/hosts.equiv 进行认证的。
# 出于安全考虑，建议使用默认值"no"
#RhostsRSAAuthentication

# 这个指令与 RhostsRSAAuthentication 类似，但是仅可以用于SSH-2。推荐使用默认值"no"。
# 推荐使用默认值"no"禁止这种不安全的认证方式。
# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no

#HostbasedUsesNameFromPacketOnly
# 在开启 HostbasedAuthentication 的情况下，
# 指定服务器在使用 ~/.shosts ~/.rhosts /etc/hosts.equiv 进行远程主机名匹配时，是否进行反向域名查询。
# "yes"表示sshd信任客户端提供的主机名而不进行反向查询。默认值是"no"。

# 是否在 RhostsRSAAuthentication 或 HostbasedAuthentication 过程中忽略用户的 ~/.ssh/known_hosts 文件。
# 默认值是"no"。为了提高安全性，可以设为"yes"。
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no

# 是否在 RhostsRSAAuthentication 或 HostbasedAuthentication 过程中忽略 .rhosts 和 .shosts 文件。
# 不过 /etc/hosts.equiv 和 /etc/shosts.equiv 仍将被使用。推荐设为默认值"yes"。
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes


# To disable tunneled clear text passwords, change to no here!

# 是否允许使用基于密码的认证。默认为"yes"。
#PasswordAuthentication yes

是否允许密码为空的用户远程登录。默认为"no"
#PermitEmptyPasswords no

# 是否允许质疑-应答(challenge-response)认证。默认值是"yes"
# 所有 login.conf(5) 中允许的认证方式都被支持。
# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Kerberos options

# 是否要求用户为 PasswordAuthentication 提供的密码必须通过 Kerberos KDC 认证，也就是是否使用Kerberos认证。
# 要使用Kerberos认证，服务器需要一个可以校验 KDC identity 的 Kerberos servtab 。默认值是"no"。
#KerberosAuthentication no

# 如果 Kerberos 密码认证失败，那么该密码还将要通过其它的认证机制(比如 /etc/passwd)。
# 默认值为"yes"。
#KerberosOrLocalPasswd yes

# 是否在用户退出登录后自动销毁用户的 ticket 。默认值是"yes"。
#KerberosTicketCleanup yes

# 如果使用了 AFS 并且该用户有一个 Kerberos 5 TGT，那么开启该指令后，
# 将会在访问用户的家目录前尝试获取一个 AFS token 。默认为"no"。
#KerberosGetAFSToken no

# GSSAPI options

# 是否允许使用基于 GSSAPI 的用户认证。默认值为"no"。仅用于SSH-2。
#GSSAPIAuthentication no

是否在用户退出登录后自动销毁用户凭证缓存。默认值是"yes"。仅用于SSH-2。
#GSSAPICleanupCredentials yes


#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes

#AllowAgentForwarding yes

# 是否允许TCP转发，默认值为"yes"。
# 禁止TCP转发并不能增强安全性，除非禁止了用户对shell的访问，因为用户可以安装他们自己的转发器。
#AllowTcpForwarding yes

######################################################################################################################

# GatewayPorts
# 是否允许远程主机连接本地的转发端口。默认值是"no"。
# sshd(8) 默认将远程端口转发绑定到loopback地址。这样将阻止其它远程主机连接到转发端口。
# GatewayPorts 指令可以让 sshd 将远程端口转发绑定到非loopback地址，这样就可以允许远程主机连接了。
# "no"表示仅允许本地连接，"yes"表示强制将远程端口转发绑定到统配地址(wildcard address)，
# "clientspecified"表示允许客户端选择将远程端口转发绑定到哪个地址。
#GatewayPorts no

######################################################################################################################

# 是否允许进行 X11 转发。默认值是"no"，设为"yes"表示允许。
# 如果允许X11转发并且sshd(8)代理的显示区被配置为在含有通配符的地址(X11UseLocalhost)上监听。
# 那么将可能有额外的信息被泄漏。由于使用X11转发的可能带来的风险，此指令默认值为"no"。
# 需要注意的是，禁止X11转发并不能禁止用户转发X11通信，因为用户可以安装他们自己的转发器。
# 如果启用了 UseLogin ，那么X11转发将被自动禁止。
X11Forwarding yes

# 指定sshdX11 转发的第一个可用的显示区(display)数字。默认值是 10 。
# 这个可以用于防止 sshd 占用了真实的 X11 服务器显示区，从而发生混淆
#X11DisplayOffset 10

# sshd是否应当将X11转发服务器绑定到本地loopback地址。默认值是"yes"。
# sshd 默认将转发服务器绑定到本地loopback地址并将 DISPLAY 环境变量的主机名部分设为"localhost"。
# 这可以防止远程主机连接到 proxy display 。不过某些老旧的X11客户端不能在此配置下正常工作。
# 为了兼容这些老旧的X11客户端，你可以设为"no"
#X11UseLocalhost yes
#PermitTTY yes

# 指定sshd是否在每一次交互式登录时打印 /etc/motd 文件的内容。默认值是"yes"。
PrintMotd no

# 指定sshd是否在每一次交互式登录时打印最后一位用户的登录时间。默认值是"yes"。
#PrintLastLog yes

# 指定系统是否向客户端发送 TCPkeepalive消息。默认值是"yes"。这种消息可以检测到死连接、连接不当关闭、客户端崩溃等异常。可以设为"no"关闭这个特性。
#TCPKeepAlive yes

# 是否在交互式会话的登录过程中使用 login(1) 。默认值是"no"。
# 如果开启此指令，那么 X11Forwarding 将会被禁止，因为 login(1) 不知道如何处理 xauth(1) cookies 。
# 需要注意的是，login(1) 是禁止用于远程执行命令的。
# 如果指定了 UsePrivilegeSeparation ，那么它将在认证完成后被禁用。
#UseLogin no

是否让sshd通过创建非特权子进程处理接入请求的方法来进行权限分离。默认值是"yes"。
认证成功后，将以该认证用户的身份创建另一个子进程。
这样做的目的是为了防止通过有缺陷的子进程提升权限，从而使系统更加安全。
#UsePrivilegeSeparation sandbox

# 指定是否允许 sshd(8) 处理 ~/.ssh/environment 以及 ~/.ssh/authorized_keys 中的 environment= 选项。
# 默认值是"no"。如果设为"yes"可能会导致用户有机会使用某些机制(比如 LD_PRELOAD)绕过访问控制，造成安全漏洞。
#PermitUserEnvironment no

# 是否对通信数据进行压缩，还是延迟到认证成功之后再对通信数据进行压缩。
# 可用值："yes", "delayed"(默认), "no"。
#Compression delayed

######################################################################################################################

# 设置一个以秒记的时长，如果超过这么长时间没有收到客户端的任何数据，
# sshd(8) 将通过安全通道向客户端发送一个"alive"消息，并等候应答。
# 默认值 0 表示不发送"alive"消息。这个选项仅对SSH-2有效。
#ClientAliveInterval 0

######################################################################################################################

# sshd在未收到任何客户端回应前最多允许发送多少个"alive"消息。默认值是 3 。
# 到达这个上限后，sshd(8) 将强制断开连接、关闭会话。
# 需要注意的是，"alive"消息与 TCPKeepAlive 有很大差异。
# "alive"消息是通过加密连接发送的，因此不会被欺骗；而 TCPKeepAlive 却是可以被欺骗的。
# 如果 ClientAliveInterval 被设为 15 并且将 ClientAliveCountMax 保持为默认值，
# 那么无应答的客户端大约会在45秒后被强制断开。这个指令仅可以用于SSH-2协议。
#ClientAliveCountMax 3

######################################################################################################################

# 指定sshd是否应该对远程主机名进行反向解析，以检查此主机名是否与其IP地址真实对应。默认值为"yes"。
#UseDNS no

# 指定在哪个文件中存放SSH守护进程的进程号，默认为 /var/run/sshd.pid 文件
#PidFile /var/run/sshd.pid

# 最大允许保持多少个未认证的连接。默认值是 10 。
# 到达限制后，将不再接受新连接，除非先前的连接认证成功或超出 LoginGraceTime 的限制。
#MaxStartups 10:30:100

# 是否允许 tun(4) 设备转发。可用值如下：
# "yes", "point-to-point"(layer 3), "ethernet"(layer 2), "no"(默认)。
# "yes"同时蕴含着"point-to-point"和"ethernet"。
#PermitTunnel no

#ChrootDirectory none
#VersionAddendum none

# 这个特性仅能用于SSH-2，默认什么内容也不显示。"none"表示禁用这个特性。
# no default banner path
#Banner none

######################################################################################################################
# AcceptEnv
# 指定客户端发送的哪些环境变量将会被传递到会话环境中。[注意]只有SSH-2协议支持环境变量的传递。
# 细节可以参考/etc/ssh/ssh_config中的 SendEnv 配置指令。
# 指令的值是空格分隔的变量名列表(其中可以使用'*'和'?'作为通配符)。也可以使用多个 AcceptEnv 达到同样的目的。
# 需要注意的是，有些环境变量可能会被用于绕过禁止用户使用的环境变量。由于这个原因，该指令应当小心使用。
# 默认是不传递任何环境变量。
# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

######################################################################################################################

# 系统内开启ssh服务和sftp服务都是通过/usr/sbin/sshd这个后台程序监听22端口，而sftp服务作为一个子服务，是通过/etc/ssh/sshd_config配置文件中的Subsystem实现的，如果没有配置Subsystem参数，则系统是不能sftp访问的
# override default of no subsystems
Subsystem   sftp    /usr/lib/openssh/sftp-server

# AllowGroups
# 这个指令后面跟着一串用空格分隔的组名列表(其中可以使用"*"和"?"通配符)。默认允许所有组登录。
# 如果使用了这个指令，那么将仅允许这些组中的成员登录，而拒绝其它所有组。
# 这里的"组"是指"主组"(primary group)，也就是/etc/passwd文件中指定的组。
# 这里只允许使用组的名字而不允许使用GID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#AllowGroups<组名1> <组名2> <组名3> ...
##指定允许通过远程访问的组，多个组以空格隔开。当多个用户需要通过ssh登录系统时，可将所有用户加入一个组中

# DenyGroups
# 这个指令后面跟着一串用空格分隔的组名列表(其中可以使用"*"和"?"通配符)。默认允许所有组登录。
# 如果使用了这个指令，那么这些组中的成员将被拒绝登录。
# 这里的"组"是指"主组"(primary group)，也就是/etc/passwd文件中指定的组。
# 这里只允许使用组的名字而不允许使用GID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#DenyGroups<组名1> <组名2> <组名3> ...
##指定禁止通过远程访问的组，多个组以空格隔开。

######################################################################################################################

# AllowUsers
# 这个指令后面跟着一串用空格分隔的用户名列表(其中可以使用"*"和"?"通配符)。默认允许所有用户登录
# 如果使用了这个指令，那么将仅允许这些用户登录，而拒绝其它所有用户。
# 如果指定了 USER@HOST 模式的用户，那么 USER 和 HOST 将同时被检查。# override default of no subsystems
# 这里只允许使用用户的名字而不允许使用UID。相关的 allow/deny 指令按照下列顺序处理：Subsystem    sftp    /usr/lib/openssh/sftp-server
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#AllowUsers<用户名1> <用户名2> <用户名3> ...
##指定允许通过远程访问的用户，多个用户以空格隔开

# DenyUsers
# 这个指令后面跟着一串用空格分隔的用户名列表(其中可以使用"*"和"?"通配符)。默认允许所有用户登录。
# 如果使用了这个指令，那么这些用户将被拒绝登录。
# 如果指定了 USER@HOST 模式的用户，那么 USER 和 HOST 将同时被检查。
# 这里只允许使用用户的名字而不允许使用UID。相关的 allow/deny 指令按照下列顺序处理：
# DenyUsers, AllowUsers, DenyGroups, AllowGroups
#DenyUsers<用户名1> <用户名2> <用户名3> ...
##指定禁止通过远程访问的用户，多个用户以空格隔开

######################################################################################################################

# Match
# 引入一个条件块。块的结尾标志是另一个 Match 指令或者文件结尾。
# 如果 Match 行上指定的条件都满足，那么随后的指令将覆盖全局配置中的指令。
# Match 的值是一个或多个"条件-模式"对。可用的"条件"是：User, Group, Host, Address 。
# 只有下列指令可以在 Match 块中使用：AllowTcpForwarding, Banner,
# ForceCommand, GatewayPorts, GSSApiAuthentication,
# KbdInteractiveAuthentication, KerberosAuthentication,
# PasswordAuthentication, PermitOpen, PermitRootLogin,
# RhostsRSAAuthentication, RSAAuthentication, X11DisplayOffset,
# X11Forwarding, X11UseLocalHost

# 指定TCP端口转发允许的目的地，可以使用空格分隔多个转发目标。默认允许所有转发请求。
# 合法的指令格式如下：
# PermitOpen host:port
# PermitOpen IPv4_addr:port
# PermitOpen [IPv6_addr]:port
# "any"可以用于移除所有限制并允许一切转发请求。

# Example of overriding settings on a per-user basis
#Match User anoncvs
#   X11Forwarding no
#   AllowTcpForwarding no
#   PermitTTY no

#   ForceCommand cvs server
#   ForceCommand
#   强制执行这里指定的命令而忽略客户端提供的任何命令。这个命令将使用用户的登录shell执行(shell -c)。
#   这可以应用于 shell 、命令、子系统的完成，通常用于 Match 块中。
#   这个命令最初是在客户端通过 SSH_ORIGINAL_COMMAND 环境变量来支持的

PermitRootLogin yes

# 指定sshd守护进程监听的端口号，默认为 22 。可以使用多条指令监听多个端口。
# 默认将在本机的所有网络接口上监听，但是可以通过 ListenAddress 指定只在某个特定的接口上监听。
Port 22

```

### SSH端口转发

SSH端口转发将其他TCP端口的网络数据通过SSH链路转发，并自动加密和解密。

### 本地转发实例

```sh
# 一台LDAP server，但限制远程机器连接，此时利用ssh端口转发达到从远程机器测试server的目的
# 命令格式：
ssh -L <local port>:<remote host>:<remote port> <SSH hostname>
# 在LDAP client执行以下命令即可建立ssh的本地端口转发
ssh -L 7001:localhost:389 LDAPServer
![fagure](http://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/image002.jpg)
# 在选择端口号时要注意非管理员帐号是无权绑定 1-1023 端口的，所以一般是选用一个 1024-65535 之间的并且尚未使用的端口号即可
# 然后我们可以将远程机器（LdapClientHost）上的应用直接配置到本机的 7001 端口上（而不是 LDAP 服务器的 389 端口上）
# SSH 同时提供了 GatewayPorts 关键字，我们可以通过指定它与其他机器共享这个本地端口转发。
ssh -g -L <local port>:<remote host>:<remote port> <SSH hostname>
```

### 远程转发实例

```sh
# 假设由于网络或防火墙的原因我们不能用 SSH 直接从 LdapClientHost 连接到 LDAP 服务器（LdapServertHost），但是反向连接却是被允许的。那此时我们的选择自然就是远程端口转发了。
# 命令格式：
ssh -R <local port>:<remote host>:<remote port> <SSH hostname>
# 在LDAP服务器（LdapServertHost）端执行如下命令：
ssh -R 7001:localhost:389 LdapClientHost
![figure](http://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/image003.jpg)
```

### 多主机转发应用实例

```sh
# 本地转发命令中的<remote host>和<SSH hostname>可以是不同的机器
ssh -L <local port>:<remote host>:<remote port> <SSH hostname>
# 看一个涉及到四台机器 (A,B,C,D) 的例子
![figure](http://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/image004.jpg)
# 在 SSH Client(C) 执行下列命令来建立 SSH 连接以及端口转发：
ssh -g -L 7001:<B>:389 <D>
# 然后在我们的应用客户端（A）上配置连接机器（C ）的 7001 端口即可,在上述连接中，（A）<-> (C) 以及 (B)<->(D) 之间的连接并不是安全连接，它们之间没有经过 SSH 的加密及解密。
```

### 动态转发实例

```sh
# 当我们在一个不安全的 WiFi 环境下上网，用 SSH 动态转发来保护我们的网页浏览及 MSN 信息无疑是十分必要的。实际是创建了一个socks代理服务
# 命令格式：
ssh -D <local port> <SSH Server>
![figure](http://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/image005.jpg)
# 此时 SSH 所包护的范围只包括从浏览器端（SSH Client 端）到 SSH Server 端的连接，并不包含从 SSH Server 端 到目标网站的连接。如果后半截连接的安全不能得到充分的保证的话，这种方式仍不是合适的解决方案。
```

### X协议转发实例

```sh
# 可用作远程桌面连接，被远程端是Windows可安装xming作为xserver。ssh client可以选择putty配置ssh访问并建立x-forward（转发）
ssh -X <SSH Server>
```

## linux磁盘详解

```sh
磁盘分区有主分区、扩展分区、逻辑分区，一块硬盘最多可以有四个主分区，其中一个主分区的位置可以用一个扩展分区替换，且一块硬盘只能有一个扩展分区，在这个扩展分区内可以划分多个逻辑分区。
预计超过四个分区，至少一个扩展分区：
3p+1e、2p+1e、1p+1e-----p：主分区、e：扩展分区
最多只能有一个扩展分区，扩展分区不能使用，必须再扩展分区上划分多个逻辑分区，然后格式化(格式化的主要目的是创建文件系统--存储数据的方式)接着才能存放数据或者安装系统

磁盘分区的设备名：
再linux中，通过设备名访问设备，设备名存放在/dev目录
--系统的第一块IDE接口的硬盘名称为/dev/hda
--系统的第二块IDE接口的硬盘名称为/dev/hdb
--系统的第一块SCSI接口的硬盘名称为/dev/sda
--系统的第二块SCSI接口的硬盘名称为/dev/sdb

分区则使用数字编号标识：
--系统的第一块IDE接口的硬盘第一个分区名称为/dev/hda1
--系统的第二块IDE接口的硬盘第六个分区名称为/dev/hda6
--系统的第一块SCSI接口的硬盘第一个分区名称为/dev/sdb1
--系统的第二块SCSI接口的硬盘第一个分区名称为/dev/sdb6

注意：
分区数字编号1-4留给主分区或者扩展分区使用，逻辑分区是在扩展分区的基础上编号从5开始
SCSI/SAS/SATA/USB接口的硬盘的设备名均以/dev/sd开头，不同硬盘编号依次是/dev/sda、/dev/sdb
SAS/SATA是主流硬盘接口，SSD固态硬盘，速度性能SSD>SAS>SATA
```

## Raid0 1 5 10详解

RAID(Redundant Array of Independent Disk 独立冗余磁盘阵列)

A, B, C, D, E and F     代表blocks(块)
p1, p2, and p3          代表parity(奇偶校验)

RAID 0 （又称为Stripe或Striping－－分条）
即Data Stripping数据分条技术。RAID 0可以把多块硬盘连成一个容量更大的硬盘群，可以提高磁 盘的性能和吞吐量。RAID 0没有冗余或错误修复能力，成本低，要求至少两个磁盘，一般只是在那些对数 据安全性要求不高的情况下才被使用,并行操作使同一时间内磁盘读写速度提升

最少需要两块磁盘
数据条带式分布
没有冗余，性能最佳(不存储镜像、校验信息)
不能应用于对数据安全性要求高的场合
![raid 0](http://static.thegeekstuff.com/wp-content/uploads/2010/07/raid-0.png)

|  |  |
| :------: | :------: |
| 容错性 | 没有 |
| 热备盘选项 | 没有 |
| 随机写性能 | 高 |
| 需要的磁盘数 | 只需2个或2*N个（这里应该是多于两个硬盘都可以） |
| 典型应用 | 无故障的迅速读写，要求安全性不高，如图形工作站等 |

RAID 1 （又称为Mirror或Mirroring－－镜像）
RAID 1称为磁盘镜像：把一个磁盘的数据镜像到另一个磁盘上，在不影响性能情况下最大限度的保证系统的可靠性和可修复性上，具有很高的数据冗余能力，但磁盘利用 率为50%，故成本最高，多用在保存关键性的重要数据的场合。RAID 1的操作方式是把用户写入硬盘的数据百分之百地自动复制到另外一个硬盘上

最少需要2块磁盘
提供数据块冗余
性能好
![raid 1](http://static.thegeekstuff.com/wp-content/uploads/2010/07/raid-1.png)

RAID 5 （可以理解为是RAID 0和RAID 1的折衷方案，但没有完全使用RAID 1镜像理念，而是使用了“奇偶校验信息”来作为数据恢复的方式，与下面的RAID10不同,RAID 5 是一种存储性能、数据安全和存储成本兼顾的存储解决方案

最少3块磁盘
数据条带形式分布
以奇偶校验作冗余
适合多读少写的情景，是性能与数据冗余最佳的折中方案
![raid 5](http://static.thegeekstuff.com/wp-content/uploads/2010/07/raid-5.png)

|  |  |
| :------: | :------: |
| 容错性 | 有 |
| 热备盘选项 | 有 |
| 随机写性能 | 低 |
| 需要的磁盘数 | 三个或更多 |
| 可用容量 | （n-1）/n的总磁盘容量（n为磁盘数） |
| 典型应用 | 随机数据传输要求安全性高，如金融、数据库、存储等 |

RAID10也被称为镜象阵列条带。象RAID0一样，数据跨磁盘抽取；象RAID1一样，每个磁盘都有一个镜象磁盘, 所以RAID 10的另一种会说法是 RAID 0+1。RAID10提供100%的数据冗余，支持更大的卷尺寸

最少需要4块磁盘
先按RAID 0分成两组，再分别对两组按RAID 1方式镜像
兼顾冗余(提供镜像存储)和性能(数据条带形分布)
在实际应用中较为常用(尤其是数据库中)
![raid 10](http://static.thegeekstuff.com/wp-content/uploads/2010/08/raid10.png)

RAID总结

| 安全性 | 磁盘利用率 | 成本 | 应用方面 |
| :------: | :------: | :------: | :------: |
| 最差（完全无安全保障） | 最高（100％） | 最低 | 个人用户 |
| 最高（提供数据的百分之百备份） | 差（50％） | 最高 | 适用于存放重要数据，如服务器和数据库存储等领域。 |
| RAID 5<RAID 1 | RAID 5>RAID 1 | RAID 5<RAID 1 | 是一种存储性能、数据安全和存储成本兼顾的存储解决方案。 |
| RAID10＝RAID1 | RAID10＝RAID1（50％） | RAID10＝RAID1 | 集合了RAID0，RAID1的优点，但是空间上由于使用镜像，而不是类似RAID5的“奇偶校验信息”，磁盘利用率一样是50％ |

### Linux文件系统目录介绍

```sh
基本文件系统类型：
--普通文件：如文本文件、c语言源代码、shell脚本等，可以用cat、less、more、vi等来察看内容，用mv来改名；
--目录文件：包括文件名、子目录名及其指针，可以用ls列出目录文件；
--链接文件：是指向一索引节点的那些目录条目，用ls来查看时，链接文件的标志用l开头，而文件后以"->"指向所链接的文件；
--特殊文件：如磁盘、终端、打印机等都在文件系统中表示出来，常放在/dev目录内；
可以用file命令来识别。

.：表示当前目录，也可以用./表示；
..：表示上一级目录，也可以用../表示；
~：代表用户自己的宿主目录；

/bin       保存可执行的二进制文件（可执行的指令）权限对所有用户开放；

/boot(分区大小：100m)     引导启动所需要的文件都在此目录中、包含了内核文件3.7M大小，驱动，插件，模块等文件；
/boot：存放开机启动加载程序的核心文件,包括引导文件、内核文件、驱动、 插件、模块等；(如kernel和grup)
    config-2.6.18-164.el5：系统kernel的配置文件，内核编译完成保存的就是这个配置文件；
    lost+found：说明/boot是一个独立的ext3文件系统；
    vmlinuz-2.6.18-164.el5：系统使用kernel，非常重要；
    grub：多系统启动管理程序grub的目录，里面存放的都是grub在启时所需要的画面、配置及各阶段的配置文件；其中grub.conf是grub配置文件；
    symvers-2.6.18-164.el5.gz
    initrd-2.6.18-164.el5.img：此文件是linux系统启动时的模块应主要来源，initrd的目的就是在kernel加载系统识别cpu和内存等心信息之后，让系统进一步知道还有那些硬件是启动所必须使用的；
    System.map-2.6.18-164.el5：是系统kernel中的变量对应表；(可以理解为是索引文件)

/dev      存放了所有的硬件设备文件；网卡，声卡，硬盘，光驱；tty1,tty2终端，设备文件目录，虚拟文件系统，主要存放所有系统中device的相关信息，不论是使用的或未使用的设备，只要有可能使用到，就会在/dev中建立一个相对应的设备文件；设备文件分为2种类型：字符设备文件和块设备文件(目录中基本上都是设备文件，如硬盘设备文件/dev/sda)
 /dev/console：系统控制台，也就是直接和系统连接的监视器；
 /dev/hd：IDE设备文件；
 /dev/sd：sata、usb、scsi等设备文件；
 /dev/fd：软驱设备文件；
 /dev/tty：虚拟控制台设备文件；
 /dev/pty：提供远程虚拟控制台设备文件；
 /dev/null：所谓"黑洞"，所有写入该设备的信息都将消失，如当想将屏幕上的输出信息隐藏起来时，只要将输出信息输入到/dev/null即可；

/etc：主机、系统或网络配置文件存放目录；
    简单的将/etc目录分为以下几类：
    --基本文件：所有直接放在/etc目录下的文件归类为基本文件；
            aliases：用于设置邮件别名；
            auto.*：代表的是一系列autofs服务所需要的配置文件，这个服务主要是让管理员可以事先定义出一些网络、本机或光驱等默认的路径；
            auto.master：负责规划目录的分配与使用，目前默认提供三种自动挂载模式；
            auto.misc：文件中的配置都以实体连接本机的磁盘驱动器为主；
            auto.net：并不是一个配置文件，而是一个脚本文件，在使用上其实不须做任何调整；；
            auto.smb：与auto.net一样，都是以个脚本文件；
            bashrc：用户登录功能配置，全局配置，对所有用户生效，主要配置别名；
            profile：与系统环境配置或初始化软件的相关配置，全局配置，对所有用户生效，主要配置变量；
            DIR_COLORS：用于配置ls命令的颜色，主要针对tty登录的用户；
            DIR_COLORS.xterm：用于配置ls命令的颜色，主要针对xterm登录的用户；
            fstab：系统启动时自动挂载文件系统的配置文件；
            inittab：启动时系统所需要的第一个配置文件；也即是init进程的配置文件；
            issue：用户本机登录时，看到的欢迎信息；
            issue.net：用户网络登录时，看到的欢迎信息；
            ld.so.conf：包含ld.so.conf.d/*.conf配置；主要是ld.so.conf.d/*.conf目录的作用；
            localtime：系统所使用的时区对应的配置文件；对应的时区文件都存在于/usr/share/zoneinfo/
            motd：登录成功的用户显示的信息对应的配置文件；
            mtab：可以当做是检查当前文件系统挂载情况的配置文件；与mount命令结果一致；
            prelink.conf：定义哪些执行文件和函数库是需要预先连接的；
            securetty：主要是login程序在使用的，只要是列在该文件中的接口，就表示是可以使用的接口，相反，若从列表中删除，则无法使用该接口；
            shells：记录目前系统所拥有shell种类的路径，通过cssh命令使用；
            sudoers：sudo命令对应的配置文件，用于配置权限的分配方式；
            sysctl.conf：主要是帮助用户配置/proc/sys目录下所有文件的值，与sysctl命令对应；
            syslogd.conf：是syslogd服务的配置文件
            host.conf：主机名解析配置文件，主要说明解析的方式及顺序；
            hosts：主机名解析配置文件，主要列出所有需要本地解析的主机名与IP地址的对应关系；
            hosts.allow和hosts.deny：linux网络安全机制TCP Wrapper对应的配置文件；
            nsswitch.conf：主要记录系统应如何查询主机名、密码、用户组、网络等，或是查询顺序的编排；
            resolv.conf：记录DNS服务器地址，用于DNS域名解析；
            services：定义了网络服务的默认端口号；
            xinetd.conf：xinetd的主配置文件，目的是为xinetd.d下的所有子服务建立一个标准的规范使其可以遵循；
            anacrontab：属于一种任务计划软件的配置文件，anacrontab软件和crond其实有点相辅相成，crond负责任务计划，而anacrontab则是负责以"间隔多久"为主要的目标；
            at.deny：该文件属于拒绝列表，只要被记录在其中的用户，就无法使用at所提供的任务计划服务；
            at.allow：与at.deny刚好相反；
            crontab：crontab的主配置文件，crond默认会执行的文件可以参考此配置文件；
            cron.deny：该文件属于拒绝列表，只要被记录在其中的用户，就无法使用crond所提供的任务计划服务；
            cron.allow：与cron.deny刚好相反；
            exports：是NFS服务的主配置文件，主要目的就是将本机的目录共享到网络上，供其他人使用；
            group与gshadow：用户组配置文件，group主要保存用户组信息，gshadow主要保存群组密码；
            login.defs：设置系统在建立账号时所参考的配置；
            passwd：主要保存系统用户账号的信息；
            shadow：linux系统通常包经过"hash"处理后的密码存储在这个文件中；
            protocols：通信协议对应端口号的一个对照表，包含协议名称、协议号码、注释等；
            wgetrc：wget程序对应的配置文件，其中有quota、mail header、重传文件的预设次数、firewall和proxy等相关设置；
            init.d：RHEL中所有服务的默认启动脚本都存放在这里；这个是链接文件，链接到/etc/rc.d/init.d；
            csh.cshrc和csh.login： 用户启动c shells执行的初始化配置文件；
            printcap：linux系统中打印机设备对应的配置文件；
    --服务器目录：如samba、http、vsftpd等服务器配置相关目录；
            cups：linux下的打印机服务器，目录下存放的是打印机服务的配置文件；
            dnsmasq.d：dnsmasq是一种DNS的"轻薄机种"，转为区域或小型网络所设计，拥有比一般DNS更为方便简易的配置；
            httpd：apache网页服务器的配置文件所在目录；
            mail：Mail Server组件的主要配置目录，如sendmail；
            ntp：网络时间服务器的配置目录，其主要配置文件为/etc/ntp.conf；
            openldap：目录明显是LDAP的配置目录，软件名称为OpenLDAP；
            postfix：postfix组件所提供的主要配置文件目录；
            samba：文件共享服务samba的主要配置文件目录；
            smrsh：这是sendmail为了限制用户可使用的命令设计的程序，将原本用户所使用的/bin/sh替换为/usr/sbin/smrsh；
            snmp：简单网络管理软件的配置文件目录，存在snmpd.conf主配置文件；
            squid：这是linux下的代理服务器squid的配置文件目录，主配置文件是squid.conf；
            ssh：SSH服务的主要配置目录，主配置文件是sshd_config；
            vsftpd：vsftpd服务器的主要配置目录，主配置文件是vsftpd.conf；
            xinetd.d：xinetd是一个管理多个服务的daemon，这个目录下列出的服务都是由xinetd进程管理的，其主配置文件是/etc/xinetd.conf；
    --系统目录：如sysconfig、xen或网络配置等与系统运行相关的目录；
            blkid：此目录所存放的其实是一个块设备ID的临时文件，主要是记录系统中所有区块设备的标签名称、硬件的唯一识别码、文件系统的格式等基本信息；
            bluetooth：linux下使用蓝牙设备所需的配置文件；启动蓝牙检测的主要服务仍是/etc/rc.d/init.d/bluetooth，该程序使用的是hcid.conf配置文件；
            cron.X：cron.X的目录都是给cron软件存放其需要任务计划的文件所使用的，按任务计划时间的长短及配置特性分为cron.d、cron.daily、cron.hourly、cron.monthly、cron.weekly五个主要目录；
            dbus-1：D-BUS的主要配置目录，D-BUS也是一种IPC交流的方式；
            default：这里是存放一些系统软件默认值的目录，存放某些软件执行时的基本参数；
            firmware：这个目录所存放的东西是非常底层的信息，是CPU所需的microcode的实体文件；
            foomatic：与打印机相关的配置目录，实现打印一对多的方式，在foomatic中，可以记录多条打印机数据，让用户只在使用前先行配置所有需要使用的打印机即可；
            hal：全名Hardware Abstraction Layer，是linux一种管理硬件的机制，它会帮所有的应用程序或用户搜集所有PCI及USB等硬件信息，因此，用户可以很简单并实时地通过HAL的方式取得硬件的相关数据；
            isdn：ISDN服务的主要配置目录，里面包含可拨号的用户、电话、联机方式等；
            ld.so.conf.d：这个目录是ldconfig所使用的，更准确的说，它是由/etc/ld.so.conf文件所决定的；ldconfig命令的目的在于将系统中的一些函数库预先存放到内存中，让系统使用时可以比以往通过硬盘的读取速度来的更快，这样可以大幅提高系统性能，尤其当要重复读取时更明显；ldconfig要将哪些函数库丢到内存中，则须看/etc/ld.so.conf文件中所记录的信息；
            logrotate.d：此目录对系统管理员来说，是十分重要的一个目录，因为目录中的文件，记录了如何定期备份系统所需要备份的系统或软件日志文件及备份方式，目录是由logrotate组件所提供的，而里面所有文件是由各软件各自产生的；其主要配置文件是/etc/logrotate.conf；
            logwatch：logrotate主要是实现如何备份日志文件，这个目录就是记载如何分析日志文件并告诉用户的软件logwatch的配置目录；
            lsb-release.d：LSB是一个由很多人所执行的项目，其目的是将所有的Linux发行版定义为一些共同的标准；
            lvm：这个目录是LVM的基本配置文件，但配置或操作一般都只需要通过LVM提供的命令，而不会用到这个目录，除非要使用到很高级的配置才会更改此文件；
            makedev.d：MAKEDEV软件对应的配置文件目录，MAKEDEV主要用来产生设备文件，也就是说，在/dev目录下的文件都由这个命令产生的，此目录下的文件主要是针对设备文件的定义或属性，目录中存在的设备文件可以由MAKEDEV来创建，否则需要使用mknod命令了；
            modprobe.d：是modprobe命令的住配置目录，一般系统启动默认要加载的模块放在/etc/modprobe.conf中；
            netplug和netplug.d：这两个目录和网络接口的联机与否由直接关系，因为主要是控制联机时的接口操作；
            opt：此目录原本是定义为存放所有额外安装软件的主机配置文件，但目前并没有被使用到，此目录为空；
            pcmcia：这是PCMCIA的配置文件目录，PCMCIA是笔记本电脑不可或缺的接口，需要即插即用的方式，此接口使用较少；
            pm：由pm-utils组件所提供的目录，pm-utils是一套电源管理的工具软件，其中/usr/lib/pm-utils也是主要目录之一；
            ppp：ppp相关的配置文件都放在这个目录中；
            profile.d：这个目录存放的是系统部分的软件配置，但会按不同的shell执行不同的文件，默认所使用的bash会直接执行该目录下所有扩展名为.sh的文件；
            rc.d：主要用来定义在每一个执行阶段必须要执行哪些系统服务或程序，在目录中主要分为三个重要的部分：
                    --rc.sysinit：系统一开始启动时所遇到的第一个文件，此脚本文件记录服务启动之前所需准备的所有事情，包括启动时看到的欢迎画面；
                    --rcX.d：在rc.sysinit文件之后所要执行的，X是系统启动时的initdefault值，值为几则会转到那个目录下，并执行其中的所有文件，在此目录中，文件一律都由两个英文字母开始K和S，K代表kill，S代表Start；
                    --rc.local：系统初始化过程中最后一个执行的脚本文件，可以将需要开机启动的程序或脚本放置在这个脚本文件中，以实现自动运行的目的；
            readahead.d：是readahead程序的主要配置目录，为了加速操作系统的使用速度，readahead_early和readahead_later这两个进程在系统加载时，直接将日常所需要的一些文件，全部先放到硬盘的高速缓存中；
            redhat-lsb：都lsb-release.d目录都是由程序redhat-lsb所提供的；
            rwtab.d：这个目录是一个在启动时会去参考的目录，主要的文件在/etc/rwtab；这是一个系统初期的备份机制；
            sane.d：这是在系统下要使用扫描仪所需的配置目录，主要配置文件是sane.conf，sane为了方便用户在各式的扫描仪连接时都可以使用，因此，在这一目录中放置了很多种不同类型扫描仪的硬件信息，让系统在检测到扫描仪时可以直接使用；
            setuptool.d：这个目录是"setup"系统配置工具的主要配置目录；
            skel：用于初始化用户宿主目录的配置目录，当建立一个用户时，会把此目录下的所有文件复制一份到用户的宿主目录，作为用户的初始化配置；
            sysconfig：非常重要的系统配置文件的存放目录，里面放置了大量系统启动及运行相关的配置文件；
            sysconfig/network-scripts/ifcfg-eth0：网卡eth0对应的配置文件，设置内容包括设备名称、IP地址、广播地址、网关地址、网段、开机是否激活等参数
            udev：udev程序本身是一套设备的管理机制，udev通过sysfs的文件系统，可以正确地掌握目前系统上存在的硬件设备，以及针对每一个硬件设备做出不同的判断与执行；
            yum和yum.repos.d：这两个都是yum的配置目录，是一套在linux下可以自动帮助用户安装、更新、移除等的管理组件，可用来替代rpm包管理方式，主配置文件是/etc/yum.conf；yum是更新方式及外挂程序的配置目录，yum.repos.d是存放定期更新组件内容的信息；
    --安全性目录：如selinux或pam.d等管理系统安全性的目录；
            audit：这个目录所代表的是一种和目录名称一致的audit安全机制，主要以服务的方式协助管理员持续监控各文件被存取的情况；目录下的audit.rules文件主要是定义一些必要的监控规则；
            pam.d：此目录是Linux-PAM的所有配置文件，配合/lib/security目录中所有觉得函数库，提供Linux下的应用程序认证的机制；
            pam_pkcs11：PAM机制中的一种登录模块，可以让用户通过smart card做登录的操作；
            pki：PKI是一种公开密钥的管理方式，通过这样的管理模式，可以让所有网络传输有更多保障；
            racoon：这个目录是由ipsec-tools组件所提供的，ipsec的主要目的是让系统实现VPN的网路技术，在racoon目录的主配置文件racoon.conf中，定义在ipsec操作中所需要的加密算法种类以及其他细节的配置；
            security：与pam.d目录相辅相成，pam.d中的所有PAM的规则都要用到/lib/security下的PAM函数库，而/etc/security目录中，就是针对这些函数库，提供以配置文件的方式进行细节配置，对希望调整系统安全性部分增加了非常大的方便性；
            selinux：selinux是一个很新的安全性方案，它是一种针对各种文件、目录、设备或daemon等在linux所需使用到的安全性机制，而且其安全性的数据时直接记录在文件系统中；
            wpa_supplicant：这个目录被归类到安全性目录中，是因为其属于无线中安全认证的部分，存在wpa_supplicant.conf配置文件，用户可以在这个目录中加入已知可登陆的AP；
    --X Windows目录：如X11或gdm管理X windows启动或使用上的配置目录；
            alternatives：linux下可辨识扩展名的"文件类型"选项，可以针对同一类型的文件，选出一个默认用户所要使用的程序去执行；/etc/alternatives目录下有所有目前已经定义的程序名称，都以软链接的方式存在，里面每一个文件其实都有定义好的默认执行程序，可以使用alternatives命令查看及修改配置；
            fonts：这个目录就是fontconfig软件的最主要配置目录，其中/etc/fonts/fonts.conf就是对应的配置文件，/etc/fonts目录下的配置都是以XML的方式配置的；
            gconf：这一目录是GConf2的组件所建立的，GConf的作用就是提供GNOME下的应用程序注册的机制，有些类似于windows下的regedit；
            gdm：全名为GNOME Display Manger，也就是协助X Windows启动的管理软件，在GDM中的主配置文件是custom.conf，在X windows下可以利用gdmsetup命令对这个文件进行配置；
            gnome-vfs-2.0：GNOME VFS机制，让GNOME的系统可以知道每一种文件格式要如何开启或浏览，而所有的配置都需要有相对应的函数库；
            gtk-2.0：由gtk+组件提供的目录，主要是提供X Windows窗口的颜色、按钮或图案，包含软件选项的画面、选项的按钮、滚动轴的样式等；
            kde：KDE Desktop Manager的主要配置目录；主配置文件是kdmrc；
            NetworkManager：此目录的目的是让用户不需要做任何操作和配置，只要用户曾经登陆过无线AP，系统就可以记录下来，以后再次登陆时就可以方便的登陆;
            pango：pango是一套协助GTK+将字体描绘出来的函数库，不论任何的字体或语言，都可以通过pango描绘出来；
            rhgb：系统在进入X Windows之前，有一个前置配置的图形接口，这个接口就是rhgb，其主要目的是让系统启动变得漂亮；
            scim：是Linux下目前很好用的输入法；
            sound：GNOME下有许多的应用软件，很多都会有其特殊的声音，这个目录中存放所有声音的命令路径；
            X11：X windows的核心配置目录；该目录下比较重要的文件有prefdm(判断X windows使用哪一个Display Manager)、主配置文件xorg.conf(定了X windows所需使用的键盘、鼠标、显卡等相关硬件设备，重点是关于显卡的配置)、xinit子目录(里面都是一些X windows资源相关的配置)
            xdg：X windows上的菜单画面，就是从这里出来的，所有在X windows中使用的菜单文字及分类，都可以在这个目录下做配置，其下的子目录menu，可以通过配置里面的文件自定义应用程序、系统管理、外观等菜单内容
    --其他目录：针对单一特殊软件的配置或未能按以上分类方式则放在此目录中；
            a2ps.cfg和a2ps-site.cfg：用于将一份文件格式转换为postscript的格式，在某些打印机或要将文件输出成一份标准格式的文件时，它会被用到；
            alsa：主要任务在于提供linux声音及声音的功能，并试着让其性能达到最佳化；
            ghostscript：在linux下要读取Adobe格式文件(如pdf)，最方便的方式就是使用ghostscript命令，这个目录主要用于设置在显示时使用哪种字体作为默认字体；
            gre.d：GRE是Mozilla注册的一种机制，目录中的配置文件gre.conf会注明所使用的Mozilla软件的路径和版本；
            iproute2：iproute2是一套非常强大的网络管理软件，iproute2提供的功能有很多种，此目录中存放一些网络的基本配置值；
            java：这个目录是由jpackage-utils软件提供的，这个目录是这个软件的主要配置目录，除此之外还有maven、jvm、jvm-common都是由jpackage-utils软件产生的，jpackage是一个专门为了提供java程序与函数库所存在的软件；
            mgetty+sendfax：主要用于使用linux架构一台fax server，可以使用mgetty.config来配置需要有关传真接收和发送的操作；
            php.d：主要存放的各软件(如dbase、ldap、mysql等)与php相关的配置文件；
            reader.conf.d：存放smart card配置文件的目录，由程序pcsc-lite提供，这个程序的主配置文件是/etc/reader.conf；
            dumpdates：存放dump命令的执行日期，dump命令可以对ext2/ext3文件系统进行检查备份；

/home      默认存放用户的宿主目录(除了root用户)
 /home/~/.bashrc：提供bash环境中所需使用的别名；
 /home/~/.bash_profile：提供bash环境所需的变量；一般先执.bashrc后，才会再执行.bash_profile；
 /home/~/.bash_history：用户历史命令文件，记录用户曾经输入的所有命令；(默认为1000条，可以通过HISTSIZE变量更改)
 /home/~/.bash_logout：当用户注销的同时，系统会自动执.bash_logout文件，如果管理员需要记录用户注销的一些额外记录动作或其他信息，就可以利用这个机制去完成；

/root     管理员root的宿主目录

/lib      需要共享的函数库与kernel模块，系统kernel启动所使用的函数库，或者当执行一些在/bin和/sbin中的命令时使用的函数库；

/mnt      挂载点目录；一般的自动挂在/media目录(如磁盘分区，网络共享)

/media：移动存储设备默认挂载点；(如光盘)

/opt      常用于放置大型的应用软件；数据库文件；

/proc    只存在于内存中的文件系统，保存操作系统的实时信息；内存信息，CPU信息，虚拟内存信息，电源信息；cpuinfo ,meminfo ,acpi,scsi,vmstat,uptime;每次启动操作系统都会自动创建一个新的/porc文件，来记录系统的实时信息；并不是存放在硬盘上，而是内存镜像中的；虚拟文件系统，此目录是kernel加载后，在内存里面建立的一个虚拟目录，有专属的文件系统，主要提供系统一些实时的信息，此目录下不能建立和删除文件；(某些文件可以修改)
    /proc主要作用可以整理为：
    --整理系统内部的信息；
    --存放主机硬件信息；
    --调整系统执行时的参数；
    --检查及修改网络和主机的参数；
    --检查及调整系统的内存和性能；
    /proc下常用的信息文件有：
    /proc/cpuinfo：cpu的硬件信息，如类型、厂家、型号和性能等
    /proc/devices：记录所有在/dev目录中相关的设备文件分类方式
    /proc/filesystems：当前运行内核所配置的文件系统
    /proc/interrupts：可以查看每一个IRQ的编号对应到哪一个硬件备
    /proc/loadavg：系统"平均负载"，3个数据指出系统当前的工作负载
    /proc/dma：当前正在使用的DMA通道
    /proc/ioports：将目前系统上所有可看到的硬件对应到内存位置的配表的详细信息呈现出来
    /proc/kcore：系统上可以检测到的物理内存，主机内存多大，这个件就有多大
    /proc/kmsg：在系统尚未进入操作系统阶段，把加载kernel和initr的信息先记录到该文件中，后续会将日志信息写入/var/log/messag文件中
    /proc/meminfo：记录系统的内存信息
    /proc/modules：与lsmod命令查看到的模块信息完全一致
    /proc/mtrr：负责内存配置的机制
    /proc/iomem：主要用于储存配置后所有内存储存的明细信息
    /proc/partitions：这个文件可以实时呈现系统目前看到的分区
    /proc/数字目录：数字目录很多，它们代表所有目前正在系统中运行所有程序
    /proc/bus：有关该主机上现有总线的所有信息，如输入设备、PCI口、PCMCIA扩展卡及USB接口信息
    /proc/net目录：存放的都是一些网络相关的虚拟配置文件，都ASCII文件，可以查看(与ifconfig、arp、netstat等有关)
    /proc/scsi：保存系统上所有的scsi设备信息(包括sata和usb设备信息)
    /proc/sys目录：存放系统核心所使用的一些变量，根据不同性质的件而存放在不同的子目录中，可以通过/etc/sysctl.conf文件设置更改其默认值；变量时实时的变更，有很多设置很象是开关，设置后上生效；
    /proc/tty：存放有关目前可用的正在使用的tty设备的信息
    /proc/self：存放到查看/proc的程序的进程目录的符号连接，当2进程查看proc时，这将会是不同的连接；主要便于程序得到它自己的程目录；
    /proc/stat：系统的不同状态信息；
    /proc/uptime：系统启动的时间长度；
    /proc/version：系统核心版本；

/sbin    存放特权级二进制文件（特权级可执行命令）只有root用户可以执行；较危险的命令保存其中；(多数管理命令默认只有管理员可以使用)

/usr：安装除操作系统本身外的一些应用程序或组件，一般可以认为linux系统上安装的应用程序默认都安装在此目录中；
    /usr/bin：一般用户有机会使用到的程序，或者该软件默认就是要所有用户使用才会放在该目录中；
    /usr/sbin：一些系统有可能会用到的系统命令，与/sbin比起来，是一些较次要的文件；
    /usr/etc：自行安装或非系统主要的配置文件目录；
    /usr/games：只要是电脑游戏相关的软件，就都安装到这个目录；
    /usr/include：存放的文件都是一些系统中用户所会使用到的C语header文件，保存的都是".h"的文件；
    /usr/kerberos：kerberos是一种安全机制，让用户可以直接使用持kerberos机制系统上的部分资源；
    /usr/lib：存放一些函数库、执行文件及连接文件，特别的是，存在这里面的文件都是不希望直接被用户或shell脚本所使用的文件，/usr/lib中有非常多的子目录，每一个软件都有其各自所需的函库；
    /usr/libexec：这个目录下的文件及文件夹应该都可以放置/usr/lib下；
    /usr/local：linux系统中安装的共享软件程序最好的方式是安装/usr/local下，按照linux标准目录结构，新建立的软件都应该放/usr/local下；
        /usr/local/bin：存放软件执行文件的目录；
        /usr/local/sbin：同样存放软件执行文件的目录，但此目录门针对系统所使用的文件；
        /usr/local/lib：软件相关的函数库；
        /usr/local/share：当文件性质不好归属时就会放在此，man册就放在这个目录下；
        /usr/local/src：所安装软件的源代码放置在此；
    /usr/share：此目录都是一些共享信息，最常被用到的就/usr/share/man这个目录，/usr/share里的信息时跨平台的；
    /usr/share/doc：放置一些系统帮助文件的地方；
    /usr/share/man：manpage的文件存放目录，也是使用man查看手册时查询的路径；
    /usr/src：主要储存内核源代码的文件；
    /usr/X11R6：存放一些X windows系统的相关文件；

/var    动态文件或数据存放目录，默认日志文件都存放在这个目录下，一般建议把此目录单独划分一个分区；
    /var/account：是linux系统下的审核机制(psacct)对应的目录；
    /var/cache：该目录下的文件时所有程序所产生的缓存数据，也就是当应用程序启动时，会将数据留一份在这个目录中；
    /var/empty：默认是sshd程序用到的这个目录，当建立ssh连接，ssh服务器必须使用该目录下的sshd子目录；
    /var/ftp：ftp服务器软件一般默认会将匿名登陆的用户的宿主目录；
    /var/gdm：gdm所使用的目录，里面存放一些系统当前所占用的console记录及通过gdm执行的X windows记录，只有通过gdm窗口的日志才会存放在此；
    /var/lib：该目录下存放很多与应用程序名称同名的子目录，每个子目录下都是应用执行的状态信息；
    /var/lock：每个服务一开始都会在这个目录下产生一个该服务的空文件，主要是避免服务启动冲突；
    /var/log：常用目录，专门用来存放所有日志文件的目录，里面存放很多系统、软件、用户等相关的日志信息；里面有一些文件是比较常用的；
        lastlog：记录用户最后一次登录的信息，使用lastlog命令读取；
        message：记录系统的几乎所有信息，主要包括启动信息，syslogd服务记录的信息等；
        wtmp：记录所有用户登陆及注销的信息，使用last命令读取；
        secure：记录登录系统访问数据的文件，如ssh pop3 telnet ftp等都会记录在此文件中
        /var/log/httpd/access_log：httpd访问日志
        /var/log/httpd/error_log：httpd错误日志
        btmp：记录失败的用户登录
        utmp： 纪录当前登录的每个用户
        xferlog：ftp会话日志
        boot.log：记录开机或一些服务启动时所显示的启动和关闭信息
        /var/log/maillog或/var/log/mail/*：记录邮件访问或往来的用户信息
        cron： 记录crontab例行性服务的内容
        dmesg：开机引导日志信息
        sudolog：纪录使用sudo发出的命令
        sulog： 纪录使用su命令的使用
    /var/named：bind软件实现的DNS服务器的区域数据文件都存放在这个目录下；
    /var/nis和/var/yp：都是NIS服务机制所使用的目录，nis主要记录所有网络中每一个client的连接信息；yp是linux的nis服务的日志文件存放的目录；
    /var/run：此目录中的大部分文件都记载目前系统正在执行程序的PID值，每一个文件都是以个独立的PID记录；此目录下存放一个特殊文件utmp，此文件记录目前谁在使用系统，必须使用utmpdump命令才能看到其中的内容；
    /var/spool：里面主要都是一些临时存放，随时会被用户所调用的数据；打印机、邮件、代理服务器等假脱机目录存放在该目录下；
    /var/tmp：专门为了一些应用程序在安装或执行时，需要在重启后使用的某些文件时，能将该文件暂时存放在这个目录中，完成后再行删除；
    /var/www：apache网页服务器的宿主目录；

/lost+found:存放了一些系统出错的检查结果；异常断电情况，死机；默认目录是空的,一般会使用fsck进行文件修复，而这些被修复或救回的文件，就会被放在这个目录下

/misc：自动挂载服务目录，对应autofs服务；

/srv：默认为空，主要用于存放一些软件的配置文件，某些软件可能会把配置文件默认存放在这个目录下，多数都是/etc目录下，此目录没有被具体的定义；

/temp  临时文件存放点；此目录有特殊的权限位粘滞位：1777

/tftpboot：远程启动tftpserver的根目录，这个目录只有安装了tftp-server软件后才会产生；

备注：
/sbin  /usr/sbin是root账户可以执行命令的存放路径
/bin  /usr/bin对于普通用户可以执行命令的存放路径
bin代表binnary ；sbin 代表super binnary；
/usr目录的重要性是和windows系统上的windows目录的重要性是一样的，都是核心文件存放的位置；

如果要做系统级备份的话，/etc ,/boot是必须要备份的目录；
备份分为了系统备份和用户备份两种；
```

### 添加硬盘或分区流程

```sh
分区指令fdisk--创建文件系统mkfs--挂载mount--写入配置文件/etc/fstab;
检测硬盘是否被识别的命令：dmesg | grep sdb

过程1：fdisk指令操作和查询
fdisk -l /dev/sdb查看硬盘的信息；
fdisk /dev/sdb 对硬盘进行分区操作;
常用的分区命令：
m help
p print the partition table
n add a new partition
t change a partition's system id
w write table to disk and exit
q quit without saving changes
默认的文件系统类型是ext4;id 83 ,system type linux
id=82 /swap
id=83 linux

过程2：格式化操作
#mkfs.ext4
#mkfs -t ext4
#windows操作系统当中的文件系统是NTFS,fat32。前者支持磁盘配额和文件压缩，后者不支持；
案例：mkfs.ext4 /dev/sdb1

过程3：文件系统挂载操作
mkdir /test/cisco
mount /dev/sdb7 /test/cisco
在目录/test/cisco中写入的内容存储到/dev/sdb7分区中；

过程4：写入启动自动挂载文件/etc/fatab
#添加分区让系统启动时自动挂载：/etc/fstab文件；
文件中的每行由六个部分组成：含义如下
1，物理分区/卷标
2，挂载点,
3，文件系统,
4，缺省设置,
5，表示文件系统是否需要dump备份（dump是备份工具），一般设为1时表示需要，设为0时将被dump所忽略
6，该数字用于决定在系统启动时进行磁盘检查的顺序，0不进行检查，1优先，2其次。对于根分区应设为1，其它分区设为2

/dev/sdb1                     /cisco                ext4          defaults  1  2
LABLE=network             /cisco              ext4          defaults  1  2

查看分区的卷标：e2lable /dev/sdb1
设置分区的卷标：e2lable /dev/sdb1 network
```

### linux系统引导流程解析

```sh
系统引导流程：
首先，PC，server，any device ,它们的第一步都是一样的；
固件firmware (CMOS/BIOS)  post加电自检
对于固件的理解，是介于软件和硬件之间的软件代码，但同时烧录到硬件当中；CMOS固化在硬件中，作为固件的平台，BIOS是管理CMOS的管理界面；

接着，自举程序bootloader (GRUB),载入内核；
第一步的最后要读取硬盘中的MBR，包含了BootLoader,在MBR中还包含了分区表等信息；

内核的工作，驱动硬件，启动初始进程init;在kernel中大部分的东东都是动程序；init启动后读取inittab文件，执行缺省默认的运行级别，从而继引导过程；在linux系统中，init是第一个存在的进程，它的PID恒为一，但也必须向一个更高级的功能负责：PID为零的内核调度器，从而获得cpu时间；

关键点：对于父子进程的关系。首先，父进程中断，子进程同样会中断；如果父进程中断后，子进程没有中断，这种关系形象称之为孤儿进程，系统发现后会将孤儿进程指向父进程init;如果父进程正常，而子进程中断的话，这样滴关系称之为僵尸进程；这两种进程在系统中不允许出现的；

备注：linux中的运行级别类似于windows 平台中的F8功能；
单用户模式（级别1）类似windos平台中F8的安全模式，区别是没有图形界面，只有命令输入；
运行级别2 和 3的区别就是2级别没有NFS功能，同时都是多用户模式；
查看当前的运行级别：runlevel
运行级别的切换：init 0 1 2 3 4 5  S 或者 telinit0 1 2 3 4 5

扩展：实际大家通过命令ls -l `which telinit`得知telinit名是init命令的一个软连接而已；

grep -v "^#" /etc/inittabl #提取有效行，作为分析数据
文件格式分析：（字段组成结构如下所示：）
id:runlevels：action:process
id 标示符,一般都是两位字母或者数字；
runlevels:指定运行级别，可以指定多个；例如::代表0-6
action:指定运行状态；
process:指定要运行的脚本或者命令；

action取值：
initdefault表示系统缺省的启动运行级别；
sysinit；系统启动时执行process中指定的命令；
wait:执行process中指定命令，并等到结束后在运行其他的命令
one:执行process中指定的命令，不需要等待结束；
ctrlaltdel:按下组合键执行process指定的命令；(关机的操作)


实例1：
id:5:initdefault:
id:3:initdefault:
#initdefault表示系统缺省的启动运行级别；

案例2：
si::sysinit:/etc/rc.d/rc.sysinit
#运行状态sysinit表示系统启动时执行process中指定的指令；
#只要系统启动就会执行脚本/etc/rc.d/rc.sysinit（任何运行级别）
#脚本的主要作用：完成系统服务程序启动，设置系统时钟，加载字体，检查加载系统，生成系统启动信息日志，设置系统环境变量；
我们可以将一些脚本或者命令写入/etc/rc.d/rc.sysinit中，重启后在任何启动级别中执行操作；
##############

先后顺序：
1，id:3:initdefault：
2，l3:3:wait:/etc/rc.d/rc
3,/etc/rc.d/rc3.d
分析：
判断默认的运行级别调用/etc/rc.d/rc脚本，执行相关运行级别目录中的服务程序，完成相应运行级别的初始化配置；之后找到/etc/rc.d/rc3.d脚本，完成系统后续的引导过程，其作用是分别存放对应于运行级别的服务程序脚本的符号链接，链接到init.d目录中的相应脚本；
ls /etc/rc.d/r3.d 里面包含了两种文件很重要，一种大写S开头，另一种是大写的K开头；
sys19syslog
S  start   K  kill  number 表示启动的顺序，数字越小优先启动；如果数字相同，那么就会按照创建的时间；

Firmware
   |
BootLoader
   |
Kernel
   |
 init
   |
/etc/inittab
   |
initdefault
   |
/etc/rc.d/rc.sysinit
   |
/etc/rc.d/rc
   |
/etc/rc.d/rcN.d
   |
Login(username ,password)

启动系统首先固件的加电自检；典型为PC中cmos/bios作用是在物层次面上检测所有的硬件是否正常工作；如果硬件正常的话，会读取硬盘的上存放物理数据的第一个位置MBR，MBR主要是存放自举程序BootLoader，在linux系统中我们大多数采用的是GRUB，GRUB的主要作用就是载入内核；载入内核后，两个作用，第一是在linux操作系统层面上是驱动硬件和启动init进程；然后读取/etc/inittab配置文件，配置文件会判断系统缺省的运行级别然后执行脚本/etc/rc.d/rc.sysinit加载基本的系统服务；接着
根据设置的缺省系统运行级别（id:3,5:initdefault:）执行/etc/rc.d/rc脚本，此脚本会判断initdefault，然后启动对应的运行级别目录下面的程序/etc/rc.d/rcN.d，最后出现登陆界面了，输入正确的用户名和密码即可登录系统继而管理服务器；

/etc/rc.d/init.d该目录下存放了各个运行级别的服务程序脚本；ls -ld /etc/rc.d/init.d ; ls -l /etc/rc.d/init.d;
ls -ld /etc/init.d 发现此文件是/etc/rc.d/init.d的软连接；
设置自启动程序：
ls -s
chkconfig
ntsysv

实例：
chkconfig
chkconfig --list
chkconfig --list sshd
chkconfig --levels 2345 sshd on

实例：
ntsysv --level 3图形界面来选择自动启动的服务级别；
扩展：
dmesg | grep eth0 内核驱动硬件的过程中是否识别网卡信息；主要反馈的信息是在kernel中的驱动硬件过程中；在linux操作系统当中所有的日志文件都存放在/var/log目录中；

dmesg | grep USB
```

### GRUB配置文件讲解：

```sh
配置文件位置：/etc/grub.conf是个软连接文件；
原始文件/boot/grub/grub.conf
grep -v "^#" /boot/grub/grub.conf | more
字段：
default定义缺省启动系统,0表示第一个，1表示第二个，以此类推；
-------------------------------------------------
timeout定义了缺省等待时间；默认5秒
-----------------------------------------------
splashimage定义了GRUB启动时的界面图片；
-----------------------------------------
hiddenmenu隐藏菜单；
--------------------------------------
title定义了菜单项名称；
----------------------------------------
root作用是设置GRUB的根设备也就是内核所在的分区
---------------------------------------------
kernel 定义了内核文件所在的位置
---------------------------------------------
initrd命令加载镜像文件以及显示镜像文件的位置；
--------------------------------------------
以上的几个字段选项是重点，加强记忆和操作；


GRUB设置密码


1，使用GRUB自带的grub-md5-crypt命令
#grub-md5-crypt
passowrd:
在图形界面我可以选择复制选项进行复制，在字符终端界面上使用鼠标右键复制加密字符；
编辑/etc/grub.conf配置文件，在hiddenmenu字段下面插入关于插入位置没有严格的要求）
password --md5 加密字符（-md5表示口令是md5加密的）
在插入模式下插入加密的字符串；然后退出保持即可；


2，第二中方式是在GRUB交互命令行界面使用md5crypt命令
#grub
grub>md5crypt
password:
实现效果是一样的；

GRUB的系统修复：（手工修复）故意修改kernel指定的内核文件
grub> cat /grub/grub.conf  为什么没有/boot原因就是grub存放在/boot分区；
grub> 指定内核所在的分区 root (hd0,0)
grub> 指定内核文件所在的位置
grub> 指定镜像文件的位置
grub> boot
手工修复属于命令行操作；如果有人修改了/etc/inittab中的默认启动级别的话，我们完全可以通过grub启动菜单来进行修复：在kernel行直接加入启动级别；

终极修复模式：光盘修复；
光盘引导，F5键，键入linux rescue
案例1：
尝试删除/etc/inittab，之前先做好备份；cp /etc/inittab /etc/inittab.bak
cp /etc/inittab /etc/inittab.back
rm -rf /etc/inittab

案例2：如果忘记了管理员密码，同时也忘记了GRUB的保护密码的话，我们可以使用GRUB的系统修复或者使用光盘的引导修复来解决此问题；
```

### Linux软件包管理

```sh
软件包安装方式如下：
1.二进制软件包管理RPM ,YUM
2.源代码包安装
3.脚本安装 shell or java

RPM软件包命名格式：
首先是软件名，版本号，发行号，以及硬件平台和后缀名

软件包管理指令：

1，卸载软件包
#rpm -e 软件包名称  使用--nodeps强行卸载情况属于有软件包依赖关系；
2，安装软件包
#rpm -ivh 软件包全称(选项v和h分别表示显示详细信息和进度条选项)
--nodeps强制安装软件包选项；

--excludedocs表示安装软件包的同时不安装帮助文档文件；
#rpm -ivh --excludedocs

--prefix PATH 表示将软件包安装到PATH指定的路径下；
#rpm -ivh --prefix=/usr/local

--test表示只对软件包安装进行测试，并不是实际安装；

实现覆盖安装软件包需要使用--replacepkgs选项；
#rpm -ivh --replacepkgs 软件包全称；

3.查询软件包：
#rpm -q 用于软件包是否安装在系统中；
#rpm -qa | grep XXX 查询系统中所有安装的软件包；

4.升级RPM软件包；
#rpm -Uvh 软件包全称；

案例：
1.查询文件隶属的软件包----rpm -qf
rpm -qf /etc/services
rpm -qf /bin/ls
2.查询软件包信息----- rpm -qi、rpm -qip(#尚未安装到到系统中的软件包)
rpm -qi vsftpd
rpm -qi samba
3.查询软件包安装文件 rpm -ql、rpm -qlp
rpm -ql samba
rpm -ql vsftpd
rpm -ql sudo
4.查询软件包帮助文件 rpm -qd
rpm -qd vsftpd
rpm -qd samba
rpm -qd sudo
rpm -qdp 软件包全称（未安装的软件包的帮助文件）
5.查询软件包配置文件 rpm -qc
rpm -qc vsftp
rpm -qc samba

###################

YUM管理软件包机制：
首先，可以自动解决软件包的依赖关系，而且方便软件包的升级；(前提是要接入互联网或者使用镜像文件生成本地YUM源)

YUM的常用选项：
安装：yum install; -y选项自动进行确认操作；
升级：yum update
软件包查询：yum list
软件包信息：yum info
卸载：yum remove
帮助：yum -help ,man yum
检测软件包升级:yum check-update

###############

源代码的安装：（源代码安装包一般都是最新版的软件包）
案例：
#tar -zxvf proftpd-1.3.3d.tar.gz
#cd proftpd.1.3.3d
./configure --prefix=/usr/local/proftpd (配置过程，也就是搜集系统信息，为后续的编译做准备)指定目录管理很方面，大多数的使用习惯都是放在此目录下；
#make 将源代码编译成二进制可执行文件；
#make install  将文件拷贝到相应的文件目录；

源代码的特点：
1，广泛的实用性； 所有的类unix系统都是支持源代码安装；
2，比较灵活；     定制或者改变源代码；

#####################

脚本安装：图形模块化操作，webmin 工具;
#tar -zxvf webmin-1.530.tar.gz
#cd webmin-1.530
#more README
#./setup.sh
URL:www.webmin.com
脚本安装软件包没有固定的格式，，主要查看README,INSTALL这两个帮助文件
```

### Linux_用户、组和权限

#### 一、权限：r, w, x

```sh

1.文件权限：
r：可读，可以使用类似cat等命令查看文件内容；
w：可写，可以编辑或删除此文件；
x: 可执行，eXacutable，可以命令提示符下当作命令提交给内核运行；
2.目录权限：
r: 可以对此目录执行ls以列出内部的所有文件；
w: 可以在此目录创建文件；
x: 可以使用cd切换进此目录，也可以使用ls -l查看内部文件的详细信息；
3.权限三位一体：
rwx:可读可学可执行
r--:只读
r-x:读和执行
---：无权限
4.八进制表示：
0 000 ---：无权限
1 001 --x: 执行
2 010 -w-: 写
3 011 -wx: 写和执行
4 100 r--: 只读
5 101 r-x: 读和执行
6 110 rw-: 读写
7 111 rwx: 读写执行
例如：755：rwxr-xr-x
rw-r-----: 640
660:rw-rw----
rwxrwxr-x:775
```

#### 二、用户和用户组

```sh
1.用户和组的文件路径：
用户：UID, /etc/passwd
组：GID, /etc/group
2.影子口令：（真正口令文件路径）
用户：/etc/shadow
组：/etc/gshadow
3.用户类别：
管理员：0
普通用户：1-65535
系统用户：1-499
一般用户：500-60000
4.用户组类别：
管理员组：0
普通组：1-65535
系统组：1-499
一般组：500-60000
5.用户组类别：
私有组：创建用户时，如果没有为其指定所属的组，系统会自动为其创建一个与用户名同名的组
基本组：用户的默认组
附加组，额外组：默认组以外的其它组
6.解释/etc/passwd中7段意义：（用户名：密码：UID:GID：注释：家目录：默认SHELL）
1）.account: 登录名
2）.password: 密码
3）.UID：
4）.GID：基本组ID
5）.comment: 注释
6）.HOME DIR：家目录
7）.SHELL：用户的默认shell
7.解释/etc/group中4段意义： 组名：密码：GID:以此组为其附加组的用户列表
8.解释/etc/shadow中8段意义：（用户名：密码：最近一次修改密码的时间：最短使用期限：最长使用期限：警告时间：非活动时间：过期时间：）
1）.account: 登录名
2）.encrypted password: 加密的密码，$中间的为salt
9.加密方法：
对称加密：加密和解密使用同一个密码
公钥加密：每个密码都成对儿出现，一个为私钥(secret key)，一个为公钥(public key)
单向加密，散列加密：提取数据特征码，常用于数据完整性校验
1、雪崩效应
2、定长输出
MD5：Message Digest, 128位定长输出
SHA1：Secure Hash Algorithm, 160位定长输出
```

#### Linux用户管理

```sh
用户配置文件：
用于信息文件:/etc/passwd
密码文件：/etc/shadow
用户组文件：/etc/group
用户组密码文件：/etc/gshadow

查看系统中有多少的用户数量，可以是wc -l /etc/passwd，因为每个用户在配置文件中占用了一行的位置空间；cut -d " : " -f 1 /etc/passwd 这条指令也可以的；

新用户信息文件：/etc/skel
登陆信息文件：/etc/motd  登录成功后会看到提示信息（可以作为一个公告栏使用）；
修改/etc/issue文件的话，是在登录之前就可以看到；为了安全起见，防止看到操作系统的版本信息，可以修改/etc/issue文件的内容或者直接删除即可；
/etc/issue.net文件中的信息是作为telnet服务的提示信息

用户信息文件的格式：（冒号分隔字段）
1.用户名：最好不要超过八位，不要使用数字表示；
2.密码：这个字段没有真正的存放密码，只是密码位；在早前系统是存放密码的，现在为了安全性的要求改变了设置；
3.UID：用户标识号；分为三类：超级用户，root，uid为0；普通用户，uid 500-60000，伪用户，uid 1-499

扩展：
什么是伪用户？
伪用户就是系统和程序操作时调用的用户，与系统和程序服务相关；
例如，bin ,daemon,shtudown ,halt等，任何linux操作系统都默认有这些伪用户，是系统自动生成的；
例如，mail,news,games, apache,ftp,mysql， sshd等，与linux系统的进程相关；
而且此类的用户不需要登录或者是无法登录系统当中，同时也可以没有相应的宿主目录；
#########
4.GID缺省组标识号；每个用户至少属于一个用户组；每个用户组可以包括多个用户；同一个用户组的用户享有该组共有的权限；
5.用户描述信息：用户全名信息，写一些针对性的用户；
6.宿主目录：用户登录系统后的缺省目录，存放自己的一些信息；/root； /home/xxx一般用户默认目录

用户密码文件，/etc/shadow文件格式；
# tail -2 /etc/shadow
sabayon:!!:14495:0:99999:7:::
tony:$1$po/zD0$4HSh/Aeae/eJ6dNj1k7Oz1:14495:0:99999:7:::
字段分析：
字段1：用户帐号的名称
字段2：加密的密码字串信息
字段3：上次修改密码的时间（1970/1/1距修改时间的天数）
字段4：密码的最短有效天数，默认值为0
字段5：密码的最长有效天数，默认值为99999
字段6：提前多少天警告用户口令将过期，默认值为7
字段7：在密码过期之后多少天禁用此用户
字段8：帐号失效时间，默认值为 空
字段9：保留字段（未使用)

扩展知识点：
关于建立用户设置密码的过程分析：密码先写入/etc/passwd文件的第二个字段，让后通过指令转到/etc/shadow文件中；
转化的命令是psconv，不过这个过程是自动完成的。回写的命令是pwunconv，执行此命令后密码会在/etc/passwd中的第二个字段X中出现且是加密的，这也说明了密码位保留的原因；

/etc/group文件格式：
组名：登陆用户所属组
组密码：一般不适用
Gid 组标识号
组内用户列表：属于改组的所有用户列表；

修改组的名称：
groupmod -n cisco webmin 将webmin改成cisco;
groupadd -g 199999 webmin  建立组的同时制定组Id ;
grep webmin /etc/group
groupdel 删除组名

groups查看用户所属组；

newgrp 组名 切换组，组密码作用：不是组成员的情况下切换到组；

useradd -D       cat /etc/default/useradd
以上两条命令都可以查看缺省的配置参数；

创建用户：useradd -u 13333 -g webmin  -c "project tangsir" -e 2014-02-01 tangsir

gpasswd 设置组密码及管理组内成员；
-a 添加用户到用户组
-d 从用户组中删除用户
-A 设置用户组管理员
-r 删除用户组密码
-R 禁止用户切换为改组

案例：授权用户alice and  jack 对目录/cisco有写权限；
1,mkdir /cisco
2,groupadd aix
3,usermod -G aix alice
4,gpasswd -a jace aix
5,chgrp aix /cisco
6,chmod g+w /cisco
然后查看目录信息
ls -ld /cisco
grep aix /etc/group
su -alice
touch /cisco/hsrp
success！！！

pwck检测/etc/passwd文件
检测的时候出现提示信息，如果文件没有问题，那么出现的问题是伪用户的信息；
伪用户是不可以登陆系统；

备注：vipw编辑/etc/passwd 文件，与vi编辑/etc/passwd唯一不同的地方是会锁定文件，其他的用户是不可以编辑的；

查看用户信息指令：
id 查看用户Id和组信息；
finger 查看用户的详细信息；
who ,w, 查看当前登陆的用户信息；
su - tangsir ; su tangsir 切换用户的区别在于前者切换后的环境变量就是本身用户所有的变量信息，后者的环境变量是管理员的环境变量信息；在操作的时候会出现奇怪的现象；

扩展知识点：
如何限制用户通过su指令切换到root：(多种方式)
1.首先查看一下su命令的属性信息 ls -l ` which su `
su命令是带有setuid的权限，文件权限是555；
#groupadd sugroup
#chmod 4550 /bin/su
#chgrp sugroup /bin/su
设置以后只有root和sugroup组中的用户可以使用su命令切换到root
添加用户到sugroup组中
#useradd alice
#passwd alice
#usermod -G sugroup alice /  passwd -a alice sugroup

2.默认情况下所有的用户都可以使用su 命令。 Root用户切换至普通用户不需要密码，普通用户切换至root需要输入root的密码，正是由于这样的可操作性，就会有用户不断的尝试密码。因此会对系统的安全构成威胁，这是我们可以通过对pam wheel模块进行限制，只要将允许使用su 命令的用户加入到wheel组中，并在“/etc/pam.d/su”文件中修改配置即可完成~
[root@tom ~]# cat /etc/pam.d/su | grep ^#
#%PAM-1.0
# Uncomment the following line to implicitly trust users in the "wheel" group.
#auth           sufficient      pam_wheel.so trust use_uid
# Uncomment the following line to require a user to be in the "wheel" group.
#auth           required        pam_wheel.so use_uid
将最后一行注释行变成配置文件；
[root@tom ~]# grep wheel /etc/group
wheel:x:10:alice,tangsir

查询密码和用户状态信息：
passwd -S alice 查看用户密码的状态信息；
passwd -d alice 清除用户的密码信息；
passwd -l alice 锁定用户或者通过编辑/etc/shadow中的密码位，在密码位前面加上两个叹号即可；
备注：MD5加密会生成255位的加密字符串，长度是固定的。由于密码位的长度不匹配了，密码无法验证，故而登陆不了系统；
passwd -uf alice 解锁用户
usermod -L alice 禁用用户
usermod -U alice 开启用户

手工添加用户的方法（尤其是数字用户，使用的正常的命令无法创建）
1，建立群组
vim /etc/group
888:x:530:888
2.建立账号属性信息
vim /etc/passwd
888:x:530:530:boss_account:/home/888:/bin/bash
3.同步passwd于shadow --将passwd内容导入shadow文件中
#pwconv
4，设置账号密码信息
passwd 888
5,建立用户根目录
cp -r /etc/skel  /home/888
6,修改根目录属性信息
chown -R 888:888 /home/888
这是最权威的设置方法~~~

扩展知识点：（linux密码破解工具john the ripper）
首先下载完成后上传到/tmp目录中解压
tar -xvf ripper-1.7.9.tar生成john-1.7.9目录
查看redme文件得知要浏览INTALL文件得知安装方法：
cd src ; make ; make linux-x86-sse2---x86架构的32位；
安装结束后新建用户名mama 密码123；将用户的用户信息和密码信息提取出来使用工具unshadow 来合成老式的用户文件（所谓的用户信息中包含密码字符串）
#grep mama /etc/passwd > /test/mama.passwd
#grep mama /etc/shadow > /test/mama.shadow
#/tmp/john-1.7.9/run/unshadow /test/mama.passwd /test/mama.shadow > /test/mama.john
#/tmp/john-1.7.9/run/john /test/mama.john
免费软件下载地址：www.openwall.com/john
注意：运行unshadow和john工具命令是一定要在软件包安装目录中执行；

关于/etc/motd即messageoftoday（布告栏信息），每次用户登录时，/etc/motd文件的内容会显示在用户的终端。系统管理员可以在文件中编辑系统活动消息，例如：管理员通知用户系统何时进行软件或硬件的升级、何时进行系统维护等。如果shell支持中文，还可以使用中文，这样看起来更易于了解。
/etc/motd缺点是，用户登录系统如果是图形界面，这些信息就不会显示。

关于/etc/issue文件的使用方法与/etc/motd文件相差不大，它们的主要区别在于：当一个网络用户或通过串口登录系统上时，/etc/issue的文件内容显示在login提示符之前，而/etc/motd内容显示在用户成功登录系统之后。

issue 内的各代码意义表示如下所示：（管理人员可以自行添加）
/l 显示第几个终端机接口；
/m 显示硬件的等级 (i386/i486/i586/i686...)；
/n 显示主机的网络名称；
/o 显示 domain name；
/r 操作系统的版本 (相当于 uname -r)
/t 显示本地端时间的时间；
/s 操作系统的名称；
/v 操作系统的版本.
```

### 三、管理命令

```sh
1.用户管理命令：useradd, userdel, usermod, passwd, chsh, chfn, finger, id, chage
1).useradd  [options]  USERNAME
-u UID
-g GID（基本组）
-G GID,...  （附加组）
-c "COMMENT"
-d /path/to/directory
-s SHELL
-m -k
-M
-r: 添加系统用户
2).userdel [option] USERNAME
-r: 同时删除用户的家目录
3).id：查看用户的帐号属性信息
-u
-g
-G
-n
4).finger: 查看用户帐号信息
finger USERNAME
5).usermod：修改用户帐号属性
-u UID
-g GID
-a -G GID：不使用-a选项，会覆盖此前的附加组；
-c
-d -m：
-s
-l
-L：锁定帐号
-U：解锁帐号
6).chsh: 修改用户的默认shell
7).chfn：修改注释信息
8).passwd：密码管理
passwd [USERNAME]
--stdin
-l
-u
-d: 删除用户密码
9).pwck：检查用户帐号完整性
2.组管理命令：groupadd, groupdel, groupmod, gpasswd
1).groupadd：创建组
-g GID
-r：添加为系统组
2).groupmod
-g GID
-n GRPNAME
3).groupdel
4).gpasswd：为组设定密码
5).newgrp GRPNAME <--> exit
6).chage：更改密码使用时间
-d: 最近一次的修改时间
-E: 过期时间
-I：非活动时间
-m: 最短使用期限
-M: 最长使用期限
-W: 警告时间
3.权限管理：chown, chgrp, chmod, umask
1).chown: 改变文件属主(只有管理员可以使用此命令)
格式：chown USERNAME file,...
chown USERNAME:GRPNAME file,...
chown USERNAME.GRPNAME file,...
-R: 修改目录及其内部文件的属主
--reference=/path/to/somefile file,...
2).chgrp:改变文件属组
格式：chgrp GRPNAME file,...
-R：递归
--reference=/path/to/somefile file,...改正和somefile文件一样的属组
3).chmod: 修改文件的权限
格式：chmod MODE file,...
-R:递归更改
--reference=/path/to/somefile file,...改正和somefile文件一样的权限
4).修改某类用户或某些类用户权限：u,g,o,a
格式：chmod 用户类别=MODE file,...
5).修改某类用户的某位或某些位权限：u,g,o,a
格式：chmod 用户类别+|-MODE file,...
```

### 四、特殊权限

```sh
特殊权限也是一个三位的：s,s,t
1.SUID: 运行某程序时，相应进程的属主是程序文件自身的属主，而不是动者；
格式：chmod u+s FILE  ，chmod u-s FILE
注意：如果FILE本身原来就有执行权限，则SUID显示为s；否则显示S；
2.SGID: 运行某程序时，相应进程的属组是程序文件自身的属组，而不是动者所属的基本组；
格式：chmod g+s FILE  ， chmod g-s FILE
注意：如果FILE本身原来就有执行权限，则SUID显示为s；否则显示S
3.Sticky: 在一个公共目录，每个都可以创建文件，删除自己的文件，但能删除别人的文件；
格式：chmod o+t DIR  ， chmod o-t DIR
注意：如果FILE本身原来就有执行权限，则SUID显示为t；否则显示T
五、umask：遮罩码
文件默认权限：666-umask
文件夹默认权限：777-umask
特殊权限默认为0
默认遮罩码：umask=0022
更改遮罩码：umask 0023
注意：文件默认不能具有执行权限，如果算得的结果中有执行权限，则将权限加1
```

### Linux进程管理

```sh
查看用户信息命令w,who;
w命令显示的字符格式如下所示：
user，TTY,from,login, idle,jcpu,pcpu,what

load average:分别显示系统在过去的1,5,15分钟内的平均负载程序；
user:登陆用户，
tty:登陆终端，是本地登陆还是远程登陆（pts/0,1,2,3）
from：显示用户从何处登陆系统，如果显示“:0”显示代表了该用户是从Xwindows下，打开文本模式窗口登陆的；
idle:用户闲置的时间，这是一个计时器，一旦用户执行任何操作，该计时器便会被重置；
jcpu：以终端代号来区分，该终端所有相关的进程执行时，所消耗的CPU时间会显示在这里；
pcpu:CPU执行程序耗费的时间；
what：用户正在执行的操作；

进程的优先级（priority）：nice , renice
首先，执行程序的优先级,
格式：nice -n command
例如：nice --5 /etc/rc.d/init.d/vsftpd start
其次是改变正在运行中的进程的优先级
格式：renice n pid
例如：renice -20 pid
#优先级的取值范围（-20,19）即使指定优先级为-30,最终的优先级还是-20，数值越小，优先级越大；因此-20的优先级是最大的；

nohup命令使进程在用户退出登录后仍旧继续执行，nohup命令执行后的数据和错误信息默认存储到文件nohup.out中
格式：nohup program &
实例：nohup find /test -name cisco* > /tmp/find.cisco.20130508 &

进程的终止和挂起；
ctrl+z, ctrl+c分别表示挂起和终止；
查看后台或者挂起的命令jobs
进程的恢复：
fg恢复进程到前台继续运行
bg恢复进程到后台继续运行

#计划任务

计划任务是linux系统管理当中非常重要的一个环节，一定要理解深刻，灵活运用；涉及到的命令如下：
at#安排作业在某一时刻执行一次；
batch#安排作业在系统负载不重时执行一次；#与at命令不同的是batch命令要检测系统的负载情况；
cron#安排周期性运行的作业；

一次性计划任务：at,batch
at的命令格式及参数；
at 【-f 文件名】 时间
at -d or atrm 删除队列中的任务
at -l or atq 查看队列中的任务

指定时间的方式分为绝对计时方法和相对计时方法；
hh:mm:today
hh:mm:tomorrow
hh:mm:星期
hh:mm:MM/DD/YY;MMDDYY;DD.MM.YY

now +n minutes
now +n hours
now +n days

ps -le | grep atd
/etc/rc.d/init.d/atd start

实例：指定今天下午17:30执行某命令；此时为下午14:30,2013.5.8
多个书写方式：（仅供参考学习）
at 5:30pm
at 17:30
at 17:30 today
at now +3 hours
at now +180 minutes
at 17:30 05.08.13
at 17:30 05/08/13
使用ctrl+d提交任务；
#以上是绝对时间和相对时间的所有格式组合；
#缺省情况下书写的计划任务都存放在/var/spool/目录中
/var/spool/at

AT配置文件
配置文件的作用：限制那些用户可以使用AT命令
#/etc/at.allow
#/etc/at.deny
1.如果/etc/at.allow文件存在，那么只有列在此文件中的用户才可以使用at命令；

2.若/etc/at.allow文件不存在，则检查/etc/at.deny文件是否存在；若/etc/at.deny文件存在，则在此文件中的用户都不能使用at命令。

3.如果两个文件都不存在，只有超级用户root可以使用at命令；

4.如果两个文件都存在而且均为空，则所有用户都可以使用at命令；
###################

crontab指令
作用：用于生成cron进程所需要的crontab文件,保存的位置/var/spool/cron
cat /var/spool/cron/root
配置文件:/etc/crontab

指令用法：
crontab -l -r -e
-l 显示当前的crontab
-r 删除当前的crontab
-e 使用编辑器编辑当前的crontab文件

分，时，日，月，周   命令/脚本
原则：把知道的具体时间添上，不知道的都添上*
注意：并不是所有的计划任务一条规则就可以搞定的，
例如；要实现周一到周五的17:30发送关机广播信息，主要保存文件，然后17:45即刻关机
案例：
30 17 * * 1-5 /usr/bin/wall < /etc/issue.mes
45 17 * * 1-5 /sbin/shutdown -h now
```

### linux上进程有5种状态

```sh
1. R 运行 runnable (on run queue)#运行(正在运行或在运行队列中等待)
2. S 中断 sleeping#中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号)
3. D 不可中断 uninterruptible sleep (usually IO)#不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生)
4. Z 僵死 a defunct (”zombie”) process#僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放)
5. T 停止 traced or stopped#停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行运行)
```

## shell脚本笔记

```sh
# 删除0字节文件
find -type f -size 0 -exec rm -rf {} \;

# 查看进程
按内存从大到小排列
ps -e  -o "%C  : %p : %z : %a"|sort -k5 -nr

# 按cpu利用率从大到小排列
ps -e  -o "%C  : %p : %z : %a"|sort  -nr

# 打印cache里的URL
grep -r -a  jpg /data/cache/* | strings | grep "http:" | awk -F'http:' '{print "http:"$2;}'

# 查看http的并发请求数及其TCP连接状态：
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

# sed在这个文里Root的一行，匹配Root一行，将no替换成yes.
sed -i '/Root/s/no/yes/' /etc/ssh/sshd_config

# 如何杀掉mysql进程：
ps aux|grep mysql|grep -v grep|awk '{print $2}'|xargs kill -9
(从中了解到awk的用途)
pgrep mysql |xargs kill -9

killall -TERM mysqld
kill -9 `cat /usr/local/apache2/logs/httpd.pid`
试试查杀进程PID

# 显示运行3级别开启的服务:
ls /etc/rc3.d/S* |cut -c 15-
(从中了解到cut的用途，截取数据)

# 如何在编写SHELL显示多个信息，用EOF
cat <<EOF   ##开始
+--------------------------------------------------------------+
|         === Welcome to Tunoff services ===                   |
+--------------------------------------------------------------+
EOF
##结束

# for 的巧用(如给mysql建软链接)
cd /usr/local/mysql/bin
for i in *
do ln /usr/local/mysql/bin/$i /usr/bin/$i
done

# 取IP地址：
ifconfig eth0|sed -n '2p'|awk '{print $2}'|cut -c 6-30
或者:
ifconfig eth0 |grep "inet addr:" |awk '{print $2}'|cut -c 6-
或者
ifconfig  | grep 'inet addr:'| grep -v '127.0.0.1' | cut -d: -f2 | awk '{ print $1}'
或者：
ifconfig eth0 | sed -n '/inet /{s/.*addr://;s/ .*//;p}'
Perl实现获取IP的方法:
ifconfig -a | perl -ne 'if ( m/^\s*inet (?:addr:)?([\d.]+).*?cast/ ) { print qq($1\n); exit 0; }'

# 内存的大小:
free -m |grep "Mem" | awk '{print $2}'

# 查看连接某服务端口最多的的IP地址
netstat -an -t | grep ":80" | grep ESTABLISHED | awk '{printf "%s %s\n",$5,$6}' | sort

# 查看Apache的并发请求数及其TCP连接状态：
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

# 因为同事要统计一下服务器下面所有的jpg的文件的大小,写了个shell给他来统计.原来用xargs实现,但他一次处理一部分,搞的有多个总和....,下面的命令就能解决啦.
find / -name *.jpg -exec wc -c {} \;|awk '{print $1}'|awk '{a+=$1}END{print a}'
CPU的数量（多核算多个CPU，
cat /proc/cpuinfo |grep -c processor
）越多，系统负载越低，每秒能处理的请求数也越多。

# CPU负载
cat /proc/loadavg
检查前三个输出值是否超过了系统逻辑CPU的4倍。

mpstat 1 1
检查%idle是否过低(比如小于5%)

# 内存空间 # free
检查free值是否过低  也可以用
cat /proc/meminfo

# swap空间  # free
检查swap used值是否过高  如果swap used值过高，进一步检查swap动作是否频繁：
vmstat 1 5
观察si和so值是否较大

# 磁盘空间  # df -h
检查是否有分区使用率(Use%)过高(比如超过90%)  如发现某个分区空间接近用尽，可以进入该分区的挂载点，用以下命令找出占用空间最多的文件或目录：
du -cks * | sort -rn | head -n 10

# 磁盘I/O负载  # iostat -x 1 2
检查I/O使用率(%util)是否超过100%

# 网络负载  # sar -n DEV
检查网络流量(rxbyt/s, txbyt/s)是否过高

# 网络错误  # netstat -i
检查是否有网络错误(drop fifo colls carrier)  也可以用命令：
cat /proc/net/dev

# 网络连接数目
netstat -an | grep -E “^(tcp)” | cut -c 68- | sort | uniq -c | sort -n

# 进程总数  # ps aux | wc -l
检查进程个数是否正常 (比如超过250)

# 可运行进程数目
列给出的是可运行进程的数目，检查其是否超过系统逻辑CPU的4倍

# 用户
who | wc -l
检查登录用户是否过多 (比如超过50个)
也可以用命令：uptime

# 系统日志
cat /var/log/rflogview/*errors
检查是否有异常错误记录  也可以搜寻一些异常关键字，例如：
grep -i error /var/log/messages
grep -i fail /var/log/messages
egrep -i 'error|warn' /var/log/messages 查看系统异常

# 打开文件数目
lsof | wc -l
检查打开文件总数是否过多

# 杀掉80端口相关的进程
lsof -i :80|grep -v "PID"|awk '{print "kill -9",$2}'|sh

# 清除僵死进程。
ps -eal | awk '{ if ($2 == "Z") {print $4}}' | kill -9

# tcpdump 抓包 ，用来防止80端口被人攻击时可以分析数据
tcpdump -c 10000 -i eth0 -n dst port 80 > /root/pkts

# 然后检查IP的重复数 并从小到大排序 注意 "-t\ +0"  中间是两个空格
less pkts | awk {'printf $3"\n"'} | cut -d. -f 1-4 | sort | uniq -c | awk {'printf $1" "$2"\n"'} | sort -n -t\ +0

# 查看有多少个活动的php-cgi进程
netstat -anp | grep php-cgi | grep ^tcp | wc -l

# 利用iptables对应简单攻击
netstat -an | grep -v LISTEN | awk ‘{print $5}’ |grep -v 127.0.0.1|grep -v 本机ip|sed  “s/::ffff://g”|awk ‘BEGIN { FS=”:” } { Num[$1]++ } END { for(i in Num) if(Num>8) { print i} }’ |grep ‘[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}’|  xargs -i[] iptables -I INPUT -s [] -j DROP
Num>8部分设定值为阀值，这条句子会自动将netstat -an 中查到的来自同一ip的超过一定量的连接的列入禁止范围。   基中本机ip改成你的服务器的ip地址

# 怎样知道某个进程在哪个CPU上运行？
ps -eo pid,args,psr

# 查看硬件制造商
dmidecode -s system-product-name

# perl如何编译成字节码，这样在处理复杂项目的时候会更快一点？
perlcc -B -o webseek webseek.pl

# 统计var目录下文件以M为大小,以列表形式列出来。
find /var -type f | xargs ls -s | sort -rn | awk '{size=$1/1024; printf("%dMb %s\n", size,$2);}' | head

# 查找var目录下文件大于100M的文件，并统计文件的个数
find /var -size +100M -type f | tee file_list | wc -l

# sed 查找并替换内容
sed -i "s/varnish/LTCache/g" `grep "Via" -rl /usr/local/src/varnish-2.0.4`
sed -i "s/X-Varnish/X-LTCache/g" `grep "X-Varnish" -rl /usr/local/src/varnish-2.0.4`

# 查看服务器制造商
dmidecode -s system-product-name

# wget 模拟user-agent抓取网页
wget -m -e robots=off -U "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6" http://www.example.com/

# 统计目录下文件的大小（按M打印显示）
du $1 --max-depth=1 | sort -n|awk '{printf "%7.2fM ----> %s\n",$1/1024,$2}'|sed 's:/.*/\([^/]\{1,\}\)$:\1:g'

# 关于CND实施几个相关的统计
统计一个目录中的目录个数
ls -l | awk '/^d/' | wc -l
统计一个目录中的文件个数
ls -l | awk '/^-/' | wc -l
统计一个目录中的全部文件数
find ./ -type f -print | wc -l
统计一个目录中的全部子目录数
find ./ -type d -print | wc -l
统计某类文件的大小:
find ./ -name "*.jpg" -exec wc -c {} \;|awk '{print $1}'|awk '{a+=$1}END{print a}'

# 查找占用磁盘IO最多的进程
wget -c http://linux.web.psi.ch/dist/scientific/5/gfa/all/dstat-0.6.7-1.rf.noarch.rpm
dstat -M topio -d -M topbio

# 去掉第一列（如行号代码）
awk '{for(i=2;i<=NF;i++) if(i!=NF){printf $i" "}else{print $i} }' list

# 输出256中色彩
for
i in {0..255}; do echo -e "\e[38;05;${i}m${i}"; done | column -c 80 -s ' '; echo -e "\e[m"

# 查看机器支持内存
机器插内存情况：
dmidecode |grep -P "Maximum\s+Capacity"
机器最大支持内存：
dmidecode |grep -P "Maximum\s+Capacity"

# 查看PHP-CGI占用的内存总数：
total=0; for i in `ps -C php-cgi -o rss=`; do total=$(($total+$i)); done; echo "PHP-CGI Memory usage: $total kb"
如果希望在成功地执行一个命令之后再执行另一个命令，或者在一个命令失败后再执行另一个命令，&&和||可以完成这样的功能。相应的命令可以是系统命令或shell脚本。
Shell还提供了在当前shell或子shell中执行一组命令的方法，即使用（）和{}

# 杀掉hello这个进程，使用下面这个命令就能直接实现。
ps -ef |grep hello |awk '{print $2}'|xargs kill -9

# 使用&&
使用&&的一般形式为：
命令1 && 命令2
这种命令执行方式相当地直接。&&左边的命令（命令1）返回真(即返回0，成功被执行）后，&&右边的命令（命令2）才能够被执行；换句话说，“如果这个命令执行成功&&那么执行这个命令”。
这里有一个使用&&的简单例子：
cp justing.doc justing.bak  && echo “if you are seeing this then cp was OK”
if you are seeing this then cp  was OK
在上面的例子中，&&前面的拷贝命令执行成功，所以&&后面的命令（echo命令）被执行。
再看一个更为实用的例子：
mv /apps/bin /apps/dev/bin  && rm -r /apps/bin
在上面的例子中，/apps/bin目录将会被移到/apps/dev/bin目录下，如果它没有被成功执行，就不会删除/apps/bin目录。
在下面的例子中，文件quarter_end.txt首先将被排序并输出到文件quarter.sorted中，只有这一命令执行成功之后，文件quarter.sorted才会被打印出来：
sort quarter_end.txt >  quarter.sorted && lp quarter.sorted
```

### 杀死进程命令例子

```sh
pgrep firefox | xargs kill -s 9
kill -s 9 `pgrep firefox`
pkill -9 firefox
killall -9 firefox（完整进程名称）
```

### 统计并杀死僵尸进程例子

```sh
统计僵尸进程数目
ps -ef | grep defunct | grep -v grep | wc -l
或
ps -eo ppid,stat | grep Z | wc -l

杀死僵尸进程
ps -eo ppid,stat | grep Z | cut -d” ” -f2 | xargs kill -9
或
kill -HUP `ps -A -ostat,ppid | grep -e ’^[Zz]‘ | awk ’{print $2}’`
或
ps -A -ostat,ppid | awk '/[zZ]/{print $2}'
或
kill $(ps -A -ostat,ppid | awk '/[zZ]/{print $2}' | sort -u)
```

### 进程按rss排序

```sh
ps p 22763  -L -o pcpu,pid,tid,time,tname,cmd,pmem,rss --sort rss
```

### 进程按第1列pcpu排序、tid为线程号

```sh
ps p 26653 -L -o pcpu,tid |sort -k1 -r -n|less
```

### 按进程消耗内存多少排序

```SH
ps -eo rss,pmem,pcpu,vsize,args | sort -k 1 -r -n | less

命令解释：
ps是linux,unix显示进程信息的, -e 是显示所有进程, -o是定制显示信息的格式

rss: resident set size, 表示进程占用RAM(内存)的大小，单位是KB

pmem: %M, 占用内存的百分比

pcpu：%C，占用cpu的百分比

vsize:表示进程占用的虚拟内存的大小，KB

args：进程名(command)

sort命令对ps结果进行排序

-k1 :按第一个参数 rss进行排序

-r：逆序

-n：numeric，按数字来排序
```

ps -mp pid -o THREAD,tid,time
其次将需要的线程ID转换为16进制格式：
printf "%x\n" tid
打印线程的堆栈信息：
jstack pid |grep tid -A 30

```sh
由于ps的输出是按PID号的顺序显示的，若要实现按照某一项使用量排序，需要把某项放入最前面。

(按照内存使用量从大到小排序)
ps -auxww|awk '{print $5,$1,$11}'|sort -r|more

按虚拟内存从大到小排列进程:
ps -eo "%C%p%z%a"|sort -k3 -nr

按实际使用内存百分比排序
ps -eo user,pid,size,pmem,vsize,command|sort -k4 -nr|more

查看并发访问用户的前10位
netstat -anp|grep 80|grep ESTAB|awk '{print $5}'|awk -F ':' '{print $1}'|sort |uniq -c|sort -rn|head -n 10

对cpu访问量高进程排序
ps -eo user,pid,size,pmem,vsize,command,%cpu|sort -k7 -nr|more`

杀死某一进程,杀死Nginx进程(杀死某一进程)
ps -ef|grep -v grep |grep nginx|awk '{print $2}'
或
for i in `ps aux | grep nginx | grep -v grep | awk {'print $2'}` ; do kill $i; done

清空Linux Buffer Cache
sync && echo 3 > /proc/sys/vm/drop_caches
```

### Linux中查看所有正在运行的进程

```sh
ps aux或ps -ef
任务：查看系统中的每个进程。
ps -A
ps -e
任务：查看非root运行的进程
ps -U root -u root -N
任务：查看用户vivek运行的进程
ps -u vivek
top命令提供了运行中系统的动态实时视图。在命令提示行中输入top：
top
pstree以树状显示正在运行的进程。树的根节点为pid或init。如果指定了用户名，进程树将以用户所拥有的进程作为根节点。
pstree
任务：使用ps列印进程树
ps -ejH
ps axjf
任务：获得线程信息
输入下列命令：
ps -eLf
ps axms
任务：获得安全信息
输入下列命令：
ps -eo euser,ruser,suser,fuser,f,comm,label
ps axZ
ps -eM
任务：将进程快照储存到文件中。输入下列命令：
top -b -n1 > /tmp/process.log
你也可以将结果通过邮件发给自己：
top -b -n1 | mail -s 'Process snapshot' you@example.com
任务：查找进程
使用pgrep命令。pgrep能查找当前正在运行的进程并列出符合条件的进程ID。例如显示firefox的进程ID：
pgrep firefox
下面命令将显示进程名为sshd、所有者为root的进程。
pgrep -u root sshd
```

### Linux VMware Tools安装步骤简易版

```sh
1. 在CD-ROM虚拟光驱中选择使用ISO镜像，这个ISO是你安装VMware workstation 的目录下的Linux.iso，不是你的Linux OS 镜像文件。VMware Tools一般都在这个文件里。
2. 以管理员身份进入Linux，root账号
3. 挂载光驱：执行mount /dev/cdrom /mnt/cdrom，这时如果进入/mnt 目录下，你会发现一个文件：VMwareTools-8.8.4-743747.tar.gz 有的虚拟机上估计如果提示如下错误，挂载点不存在。，
mount /dev/cdrom /mnt/cdrom
mount: mount point /mnt/cdrom dose not exist
请直接执行此命令：
mount /dev/cdrom /opt
cd /opt
或者应该可以使用自动挂载，直接进入
cd /misc/cd
4. copy 此文件到临时文件夹
cp /mnt/mVMwareTools-8.8.4-743747.tar.gz /tmp
5. 卸载CDROM，执行 umount /dev/cdrom
6. 进入tmp文件目录并解压此文件包
cd /tmp
tar zxvf vmware-linux-tools.tar.gz
解压默认到vmware-tools-distrib目录下：此时你可以使用ls -ll 查看文件夹下的文件
7. 进入vmware-tools-distrib，安装vmware tools.
./vmware-install.pl  执行安装，
8. 大约5分钟左右安装完成。 执行init 6重启ok。
利用xshell的MODEM传输文件需安装lrzsz
```

### 恢复rm误删的文件

```sh
一、下载及安装软件
extundelete 主页：http://extundelete.sourceforge.net/
下载地址：http://nchc.dl.sourceforge.net/project/extundelete/extundelete/0.2.0/extundelete-0.2.0.tar.bz2
ubuntu用户可直接安装: apt-get install extundelete
./configure && make && make install    # 如果提示找不到ext2fs库，使用 yum -y install e2fsprogs* 安装
二、数据恢复
1.卸载需要恢复文件的分区
fuser -k /mnt/test/               <-- 结束使用某分区的进程树
umount /mnt/test                  <-- 卸载分区
2.使用extundelete查看分区上存在的文件
extundelete --inode 2 /dev/sdb1    # --inode 为查找某i节点中的内容，使用2则说明为搜索，如果需要进入目录搜索，只须要指定目录I节点即可
extundelete --restore-inode 13 /dev/sdb1    # --restore-inode 恢复指定的I节点文件，默认全将恢复出来的文件放在当前路径 RECOVERED_FILES/ 目录下，文件名为 file.I节点号
ls RECOVERED_FILES/
mount /dev/sdb1 /mnt/test/
mv RECOVERED_FILES/file.13 /mnt/test/resolv.conf
mv RECOVERED_FILES/file.14 /mnt/test/hosts
cat /mnt/test/hosts            # 查看被恢复出来的文件 是否与源文件一致
cat /mnt/test/resolv.conf
```

### linux常用日志文件默认路径

```sh
IP登录日志默认路径：/var/log/wtmp
命令：last -f /var/log/wtmp
```

### 增加1G的swap空间

```sh
#创建1G文件
dd if=/dev/zero of=/opt/swap bs=1k count=1024000
mkswap /opt/swap    # 将创建的文件用作交换分区
swapon /opt/swap    # 开启swap
```



### Linux备份

```sh
系统的潜在威胁
1，系统硬件故障
2，软件故障
3，电源故障
4，用户的误操作
5，人为破坏
6，缓存中的内容没有及时的写入磁盘
7，自然灾害

备份介质的选择：
硬盘（文件存储服务器），光盘，磁带机，可移动存储设备；
备注：一般在选择备份介质时，要从可靠性，速度和介质价格之间进行权衡；

Linux备份策略：

1.完全备份
    每隔一段时间对系统进行一次完全的备份，这样在备份时间间隔内，一旦系统发生了故障使得数据丢失时，就可以用上一次的备份数据恢复得到上一次备份时的情况。

2.增量备份:
     首先进行一次完全备份，然后每隔一段较短的时间进行一次备份，但是仅仅备份每个短时间内更改或者更新的内容。

备份过程中考虑的因素：
1，备份
2，备份分区，ro,umount直接不挂载；
3, 压缩 bzip2
4, 校验 md5sum -c
5, 加密 GnupG

备份常用的指令：
备份目录：cp -Rpu 备份目录  目标目录
-p保持备份目录及文件的属性（默认的属性）
-u增量备份；

案例:
mount -o remount,ro /backup
cp /etc/inittab /backup/inittab_20130511.bak #出现报错信息；
mount -o  remount,rw /backup#解决问题的命令，亦可读写的方式挂载；

#远程备份可用SCP命令；

tar备份命令：
首先，备份/etc目录，可同时打包多个目录
tar -zcf /backup/sys_20130510.tar.gz /etc /boot

如何对/etc目录下指定文件进行备份？
tar -zcf backup_user_20130510.tar.gz /etc/passed /etc/shadow /etc/group /etc/gshadow

如何查看查看备份包文件？
tar -ztf /backup/user_20130510.tar.gz

如何恢复/etc目录？
tar -zxf /backup/sys_20130510.tar.gz
备注：默认还原到打包文件源目录，-C选项可以指定具体的还原目录；

只恢复备份中的指定文件
tar -zxf backup_user_20130510.tar.gz /etc/passwd

备注：在备份过程中添加时间值是非常有必要额；
```

### Linux测试硬盘读写速度

```sh
time有计时作用，dd用于复制，从if读出，写到of。if=/dev/zero不产生IO，因此可以用来测试纯写速度。同理of=/dev/null不产生IO，可以用来测试纯读速度。bs是每次读或写的大小，即一个块的大小，count是读写块的数量。

1.测/目录所在磁盘的纯写速度：
time dd if=/dev/zero bs=1024 count=1000000 of=/1Gb.file

2.测/目录所在磁盘的纯读速度：
dd if=/kvm/ftp/other/1Gb.file bs=64k |dd of=/dev/null

3.测读写速度（这是什么）：
dd if=/vat/test of=/oradata/test1 bs=64k

理论上复制量越大测试越准确。
```

### 获取8位随机字符串

```sh
方法一：echo $RANDOM |md5sum |cut -c 1-8
方法二：openssl rand -base64 4
方法三：cat /proc/sys/kernel/random/uuid |cut -c 1-8
```

### 获取8位随机数字

```sh
方法一：echo $RANDOM |cksum |cut -c 1-8
方法二：openssl rand -base64 4 |cksum |cut -c 1-8
方法三：date +%N |cut -c 1-8
cksum:打印CRC校检和统计字节
```

### 定义一个颜色输出字符串函数

```sh
方法一：
function echo_color(){
 if [$1 == "green"];then
  echo -e "\033[32;40m$2\033[0m"
 elif [$1 == "red"0];then
  echo -e "\\033[31;40m$2\033[0m"
 fi
}

方法二：
function echo_color(){
 case $1 in
  green)
   echo -e "\033[32;40m$2\033[0m"
   ;;
  red)
   echo -e "\033[31;40m$2\033[0m"
   ;;
  *)
   echo "Example:echo_color red string"
  esac
}
function关键字定义一个函数，可加或不加。
```

### 批量创建用户

```sh
#!/bin/bash
DATE=$(date+%F_%T)
USER_FILE=user.txt
echo_color(){
 if [$1 == "green"];then
  echo -e "\033[32;40m$2\033[0m"
 elif [$1 == "red"0];then
  echo -e "\\033[31;40m$2\033[0m"
 fi
}
# 如果用户文件存在并大于0就备份
 if [-s $USER_FILE];then
  mv $USER_FILE ${USER_FILE}-${DATE}.bak
  echo_color green "$USER_FILE exist,rename ${USER_FILE}-${DATE}.bak"
 fi
 echo
```

### 简单检测某个进程是否存在的bash小脚本。代码如下

```sh
#!/bin/bash
ps_out=`ps -ef | grep $1 | grep -v 'grep' | grep -v $0`
result=$(echo $ps_out | grep "$1")
if [[ "$result" != "" ]];then
 echo "Running"
else
 echo "Not Running"
fi
举例使用
比如我们启动了进程SimpleHTTPServer,我们想检测这个进程是否存在，可以这样。
代码如下:
# ./checkRunningProcess.sh 'SimpleHTTPServer' Running
```

###　shell变量的子串的删除/替换

```sh
${#string}返回$string的长度
${string:position}在$string中，从$position位置之后开始提取子串
${string:position:length}在$string中，从$position位置之后开始提取$length长度的子串
${string#substring}从变量$string开头开始删除最短匹配$substring子串
${string##substring}从变量$string开头开始删除最长匹配$sunstring子串
${string%substring}从变量$string结尾开始删除最短匹配$substring子串
${string%%substring} 从变量$string结尾开始删除最长匹配$substring子串
注意：在进行#或##匹配时，$string的首字符必须是被删除子串$substring的第一个字符，不然无法匹配删除；
在进行%或者%%匹配时，$string的最后一个字符必须是被删除子串$substring的最后一个字符，不然无法进行删除操作；
${parameter/parttern/string} 用string来替换第一个匹配的pattern
${parameter/#parttern/string} 从开头匹配parameter变量中的pattern，匹配上后就用string来替换匹配的pattern
${parameter/%pattern/string} 从结尾开始匹配parameter变量中的pattern，匹配上后就用string替换匹配的pattern
${parameter//pattern/string} 用string来替换parameter变量中所有匹配的pattern
```

### 清除大于5天的文件

```sh
#!/bin/sh
find /usr/local/tomcat/logs -name 'catalina.*.log' -mtime +5 -print0 | xargs -0 rm -f
find /usr/local/tomcat/logs -name 'localhost_access_log.*.txt' -mtime +5 -print0 | xargs -0 rm -f
```

### shell采集系统cpu 内存 磁盘 网络信息

### cpu信息采集

```sh
cpu使用率采集算法
通过/proc/stat文件采集并计算CPU总使用率或者单个核使用率。以cpu0为例，算法如下：
1. cat /proc/stat | grep ‘cpu0’得到cpu0的信息
2. cpuTotal1=user+nice+system+idle+iowait+irq+softirq
3. cpuUsed1=user+nice+system+irq+softirq
4. sleep 30秒
5. 再次cat /proc/stat | grep ‘cpu0’ 得到cpu的信息
6. cpuTotal2=user+nice+system+idle+iowait+irq+softirq
7. cpuUsed2=user+nice+system+irq+softirq
8. 得到cpu0 在30秒内的单核利用率：(cpuUsed2 – cpuUsed1) * 100 / (cpuTotal2 – cpuTotal1)
相当于使用top –d 30命令，把user、nice、system、irq、softirq五项的使用率相加。

shell代码：

a=(`cat /proc/stat | grep -E "cpu\b" | awk -v total=0 '{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }END{print total,used}'`)
sleep 30
b=(`cat /proc/stat | grep -E "cpu\b" | awk -v total=0 '{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }END{print total,used}'`)
cpu_usage=(((${b[1]}-${a[1]})*100/(${b[0]}-${a[0]})))

cpu负载
采集算法：
读取/proc/loadavg得到机器的1/5/15分钟平均负载，再乘以100。

shell代码：

cpuload=(`cat /proc/loadavg | awk '{print $1,$2,$3}'`)
load1=${cpuload[0]}
load5=${cpuload[1]}
load15=${cpuload[2]}

```

### 内存采集

```sh
应用程序使用内存采集算法：
读取/proc/meminfo文件，(MemTotal – MemFree – Buffers – Cached)/1024得到应用程序使用内存数。

shell代码：

awk '/MemTotal/{total=$2}/MemFree/{free=$2}/Buffers/{buffers=$2}/^Cached/{cached=$2}END{print (total-free-buffers-cached)/1024}'  /proc/meminfo

MEM使用量采集算法：
读取/proc/meminfo文件，MemTotal – MemFree得到MEM使用量。

shell代码：

awk '/MemTotal/{total=$2}/MemFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo

SWAP使用大小采集算法：
通过/proc/meminfo文件，SwapTotal – SwapFree得到SWAP使用大小。

shell代码：

awk '/SwapTotal/{total=$2}/SwapFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo

```

### 磁盘信息采集

```sh

disk io

1、IN：平均每秒把数据从硬盘读到物理内存的数据量
采集算法：
读取/proc/vmstat文件得出最近240秒内pgpgin的增量，把pgpgin的增量再除以240得到每秒的平均增量。
相当于vmstat 240命令bi一列的输出。

shell代码：

a=`awk '/pgpgin/{print $2}' /proc/vmstat`
sleep 240
b=`awk '/pgpgin/{print $2}' /proc/vmstat`
ioin=$(((b-a)/240))

2、OUT：平均每秒把数据从物理内存写到硬盘的数据量
采集算法：
读取/proc/vmstat文件得出最近240秒内pgpgout的增量，把pgpgout的增量再除以240得到每秒的平均增量。
相当于vmstat 240命令bo一列的输出。

shell代码：

a=`awk '/pgpgout/{print $2}' /proc/vmstat`
sleep 240
b=`awk '/pgpgout/{print $2}' /proc/vmstat`
ioout=$(((b-a)/240))

```

### 网络信息采集

```sh
流量

以https://www.centos.bz/为例，eth0是内网，eth1外网，获取60秒的流量。
机器网卡的平均每秒流量
采集算法：
读取/proc/net/dev文件，得到60秒内发送和接收的字节数（KB），然后乘以8，再除以60，得到每秒的平均流量。

shell代码：

traffic_be=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $3,$10}/eth1/{print $3,$11}' /proc/net/dev`)
sleep 60
traffic_af=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $3,$10}/eth1/{print $3,$11}' /proc/net/dev`)
eth0_in=$(( (${traffic_af[0]}-${traffic_be[0]})/60 ))
eth0_out=$(( (${traffic_af[1]}-${traffic_be[1]})/60 ))
eth1_in=$(( (${traffic_af[2]}-${traffic_be[2]})/60 ))
eth1_out=$(( (${traffic_af[3]}-${traffic_be[3]})/60 ))

包量

机器网卡的平均每秒包量
采集算法：
读取/proc/net/dev文件，得到60秒内发送和接收的包量，然后除以60，得到每秒的平均包量。

shell代码：

packet_be=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $4,$12}/eth1/{print $4,$12}' /proc/net/dev`)
sleep 60
packet_af=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $4,$12}/eth1/{print $4,$12}' /proc/net/dev`)
eth0_in=$(( (${packet_af[0]}-${packet_be[0]})/60 ))
eth0_out=$(( (${packet_af[1]}- ${packet_be[1]})/60 ))
eth1_in=$(( (${packet_af[2]}- ${packet_be[2]})/60 ))
eth1_out=$(( (${packet_af[3]}- ${packet_be[3]})/60 ))

```

```sh
#!/bin/sh
#
# ShowTuxPerf
# A simple SH to display PC performance on a Linux OS
#
# Syntaxe: sudo ./showtuxperf.sh
#
# Nicolas Hennion -GPL
#
VERSION="0.1"
dmidecode -s system-product-name
dmidecode -s system-version
lscpu - cat /proc/cpuinfo
lspci | grep VGA
uname -a

```

### 系统管理的常用shell命令

```sh

显示消耗内存/CPU最多的十个进程
ps aux | sort -nk +4 | tail
ps aux | sort -nk +3 | tail

查看linux服务器物理CPU的个数
cat /proc/cpuinfo | grep "physical id" | sort | uniq  | wc -l

查看linux服务器逻辑CPU的个数
cat /proc/cpuinfo | grep "processor " | wc -l

精简开机启动服务器
for server in `chkconfig --list|egrep -v 'crond|network|rsyslog|sshd|iptables'|awk '{print $1}'`;do chkconfig $server off; done
关闭selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
setenforce 0

禁止root用户远程登录
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

修改最大连接数 ulimit（方法不只一种）
echo '* - noproc 65535' >> /etc/security/limits.conf

echo '* - nofile 65535' >> /etc/security/limits.conf

修改默认DNS
echo "nameserver 8.8.8.8" > /etc/resolv.conf
echo "nameserver 8.8.4.4" >> /etc/resolv.conf

去除上次登录的信息
touch ~/.hushlogin

禁止使用Ctrl+Alt+Del快捷键重启服务器
方法一：
      sed -i "s/start on control-alt-delete/#start on control-alt-delete/g"                   /etc/init/control-alt-delete.conf
方法二：
      vim /etc/inittab
      #ca::ctrlaltdel:/sbin/shutdown -t3 -r now
      init q  （使用此命令使之生效)

限制用户登录的tty终端
vim /etc/inittab   （在tty终端前加#号，注释掉就可以）

禁止root用户登录的终端
vim /etc/securetty （加#号注释）


禁止除root外的用户从tty1终端登录系统
①   vim /etc/pam.d/login
Account  required  pam_access.so  (增加此认证)
②   vim /etc/security/access.conf
-:ALL EXCEPT root:tty1  (去掉#号)
- : root : 192.168.12.0/24  172.16.0.0/8（禁止root用户从这两个网段远程登录）
```

```sh

top -d 1 -n 1 -b |awk -F '[ ,.%k]+' '/^Cpu/{printf "UPU_USAGE %.f%%\t",100-$11}/^Mem/{printf "MEM_USAGE %.f%%\n",($4-$8)/$2*100}'
top -d 1 -n 1 -b |awk -F '[ ,.%k]+' '/^Cpu/{printf "CPU_USAGE %.f%%\t",100-$11}/^Mem/{printf "MEM_USAGE %.f%%\t",($4-$8)/$2*100;now=strftime("%D %T");print now}'

```
