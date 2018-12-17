# DNS服务器

## DNS-bind安装和配置

```sh

https://ftp.isc.org/
http://www.isc.org/downloads/
wget https://www.isc.org/downloads/file/bind-9-10-6-p1/?version=tar-gz
https://www.isc.org/downloads/file/bind-9-10-6-p1/?version=win-32-bit
https://www.isc.org/downloads/file/bind-9-10-6-p1/?version=win-64-bit
tar -zxvf
./configure --with-openssl --enable-threads --with-libxml2
make && make install
touch /etc/named.conf
named -V
named -g
```

> 问题

解决bind9 dumping master file tmp-xxxx open permission denied问题

解决方法：

对于Redhat Linux，只要在slave那台的dns主机上修改/etc/sysconfig/named加上ENABLE_ZONE_WRITE=yes，重启named即可

对于Ubuntu系统，修改/etc/apparmor.d/usr.sbin.named，查找/etc/bind/** r，修改成/etc/bind/** rw，然后重启apparmor：/etc/init.d/apparmor rstart或者reload配置：cat /etc/apparmor.d/usr.sbin.named | sudo apparmor_parser -r
