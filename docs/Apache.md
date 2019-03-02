# *Apache&PHP-FPM*

## apachectl选型

* configtest：检查设置文件中的语法是否正确；
* fullstatus：显示服务器完整的状态信息；
* graceful：重新启动Apache服务器，但不会中断原有的连接；
* help：显示帮助信息；
* restart：重新启动Apache服务器；
* start：启动Apache服务器；
* status：显示服务器摘要的状态信息；
* stop：停止Apache服务器。

php离线安装：

```sh
cannot find config.m4.
make sure that you run '/usr/local/php/bin/phpize' in the top level source directory of the module
其实报这个错的原因是，在执行phpize时，一定要在需要扩展编译的php模块目录中进行/usr/local/php/bin/phpize 这样才不会报错。
安装php-ldap模块
php-ldap模块作用就是实现ldap认证，因此需要安装
1、安装软件包解决依赖
yum install openldap
yum install openldap-devel
2、拷贝库文件
cp -frp /usr/lib64/libldap* /usr/lib/
3、编译安装php-ldap模块
cd /usr/local/src/php-7.0.21/ext/ldap/　　(源码包路径)
/usr/local/php/bin/phpize 　　　　　　　　(php安装路径)
./configure --with-php-config=/usr/local/php/bin/php-config  (php安装路径)
make
make install
4、然后在php的php.ini的配置文件添加http://120.76.120.248

开启PHP缓存 - 修改php.ini中：
realpath_cache_size = 4096k
realpath_cache_ttl = 120

开启NGINX的GZIP并隐藏版本号 - 修改nginx.conf中：
server_tokens    off;
gzip              on;
gzip_min_length  20k;
gzip_buffers   4 16k;
gzip_comp_level    4;
gzip_types         text/plain text/css text/javascript application/x-javascript application/xml application/x-httpd-php;
server {
        listen    80 default;
        return    444;
}
```