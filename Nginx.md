# Nginx安装

## centos安装

To set up the yum repository for RHEL/CentOS, create the file named /etc/yum.repos.d/nginx.repo with the following contents:

```repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/OS/OSRELEASE/$basearch/
gpgcheck=0
enabled=1
Replace “OS” with “rhel” or “centos”, depending on the distribution used, and “OSRELEASE” with “6” or “7”, for 6.x or 7.x versions, respectively.

对于Debian / Ubuntu，为了验证nginx存储库签名并在安装nginx软件包时消除有关丢失PGP密钥的警告，有必要将用于签署nginx软件包和存储库的密钥添加到apt程序密钥环中。请从我们的网站下载此密钥，并apt 使用以下命令将其添加到程序密钥环：
http://nginx.org/keys/nginx_signing.key

sudo apt-key add nginx_signing.key
对于Debian，用Debian分发 代号取代代号，并将以下内容附加到文件的末尾： /etc/apt/sources.list

deb http://nginx.org/packages/debian/ codename nginx
deb-src http://nginx.org/packages/debian/ codename nginx
对于Ubuntu，用Ubuntu分发 代号取代代号，并将以下内容附加到文件的末尾： /etc/apt/sources.list

deb http://nginx.org/packages/ubuntu/ codename nginx
deb-src http://nginx.org/packages/ubuntu/ codename nginx
对于Debian / Ubuntu，然后运行以下命令：

apt-get update
apt-get install nginx

Mainline版本的预构建包

rpm --import nginx_signing.key
利用rpm包安装
wget http://nginx.org/packages/mainline/centos/7/x86_64/RPMS/nginx-1.13.9-1.el7_4.ngx.x86_64.rpm
rpm -ivh nginx-1.13.9-1.el7_4.ngx.x86_64.rpm
systemctl enable nginx
systemctl start nginx

```

2.从源代码构建nginx

```sh
http://nginx.org/download/nginx-1.12.2.tar.gz
构建使用configure命令进行配置。它定义了系统的各个方面，包括nginx允许用于连接处理的方法。最后它创建一个Makefile。该configure命令支持以下参数：

--prefix=path - 定义一个将保留服务器文件的目录。这个相同的目录也将被用于所有相关的路径 configure（除了源资源的路径）和nginx.conf配置文件。它/usr/local/nginx默认设置为目录。

--sbin-path=path - 设置一个nginx可执行文件的名字。该名称仅在安装过程中使用。默认情况下，该文件被命名 prefix/sbin/nginx。

--conf-path=path - 设置nginx.conf配置文件的名称。如果需要的话，nginx可以始终使用不同的配置文件启动，只要在命令行参数中指定即可 。默认情况下，该文件被命名 。 -c fileprefix/conf/nginx.conf

--pid-path=path - 设置将存储主进程的进程ID的nginx.pid文件的名称。安装完成后，可以nginx.conf使用pid指令始终在配置文件中 更改文件名 。默认情况下，该文件被命名 prefix/logs/nginx.pid。

--error-log-path=path - 设置主要错误的名称，警告和诊断文件。安装之后，可以nginx.conf使用error_log指令始终在配置文件中 更改文件名 。默认情况下，该文件被命名 prefix/logs/error.log。

--http-log-path=path - 设置HTTP服务器的主要请求日志文件的名称。安装完成后，可以nginx.conf使用access_log指令随时在配置文件中 更改文件名 。默认情况下，该文件被命名 prefix/logs/access.log。

--build=name - 设置一个可选的nginx构建名称。

--user=name - 设置工作进程将使用其凭据的非特权用户的名称。安装完成后，可以nginx.conf使用user指令始终在配置文件中 更改名称 。默认的用户名是nobody。

--group=name - 设置工作进程将使用其凭据的组的名称。安装完成后，可以nginx.conf使用user指令始终在配置文件中 更改名称 。默认情况下，组名称设置为非特权用户的名称。

--with-select_module
--without-select_module - 启用或禁用构建允许服务器使用该select()方法的模块 。如果平台不支持更合适的方法（如kqueue，epoll或/ dev / poll），则自动构建此模块。

--with-poll_module
--without-poll_module - 启用或禁用构建允许服务器使用该poll()方法的模块 。如果平台不支持更合适的方法（如kqueue，epoll或/ dev / poll），则自动构建此模块。

--without-http_gzip_module - 禁用构建压缩 HTTP服务器响应的模块。zlib库是建立和运行这个模块所必需的。

--without-http_rewrite_module - 禁用构建允许HTTP服务器 重定向请求并更改请求URI的模块。PCRE库需要构建和运行这个模块。

--without-http_proxy_module - 禁用构建HTTP服务器代理模块。

--with-http_ssl_module - 可以构建一个模块，将HTTPS协议支持添加到HTTP服务器。该模块不是默认生成的。OpenSSL库需要构建和运行这个模块。

--with-pcre=path - 设置PCRE库源的路径。图书馆发行（版本4.4 - 8.41）需要从PCRE网站下载 并提取。其余的是由nginx的./configure和 make。在位置指令和 ngx_http_rewrite_module 模块中，该库是正则表达式支持所必需的 。

--with-pcre-jit - 用“即时编译”支持（1.1.12，pcre_jit指令）构建PCRE库 。

--with-zlib=path - 设置zlib库的源的路径。库发行版（版本1.1.3 - 1.2.11）需要从zlib站点下载 并提取。其余的是由nginx的./configure和 make。该库是ngx_http_gzip_module模块所必需的 。

--with-cc-opt=parameters - 设置将被添加到CFLAGS变量的附加参数。在FreeBSD下使用系统PCRE库时， --with-cc-opt="-I /usr/local/include" 应该指定。如果select()需要增加支持的文件数量，也可以在这里指定如下： --with-cc-opt="-D FD_SETSIZE=2048"。

--with-ld-opt=parameters - 设置将在链接期间使用的其他参数。在FreeBSD下使用系统PCRE库时， --with-ld-opt="-L /usr/local/lib" 应该指定。

参数使用示例（所有这些都需要输入一行）：

./configure --sbin-path=/usr/local/nginx/nginx --conf-path=/usr/local/nginx/nginx.conf --pid-path=/usr/local/nginx/nginx.pid --with-http_ssl_module --with-pcre=../pcre-8.41 --with-zlib=../zlib-1.2.11
配置完成后，make && make install安装

验证nginx是否运行：curl -I 127.0.0.1
HTTP/1.1 200 OK
Server: nginx/1.13.8
```

## 关于构建模块

```sh
默认情况下不包括的模块以及第三方模块必须在configure脚本中与其他构建选项一起显式指定。这些模块可以静态链接到NGINX二进制文件（每次NGINX启动时加载它们）或动态加载（只有在NGINX配置文件中包含相关指令时才加载它们。

默认构建的模块
如果您不需要默认构建的模块，可以通过使用脚本--without-<MODULE-NAME>上的选项命名它来禁用它configure，如此示例中禁用Empty GIF模块（应该键入为单行）：
```

默认构建的模块如下：
| 模块名称 | 描述 |
| :------: | :------: |
| http_access_module | 接受或拒绝来自指定客户端地址的请求。 |
| http_auth_basic_module | 通过使用HTTP基本身份验证协议验证用户名和密码来限制对资源的访问。 |
| http_autoindex_module | 处理以正斜杠字符（/）结尾的请求，并生成目录列表。 |
| http_browser_module | 创建其值取决于User-Agent请求标头值的变量。 |
| http_charset_module | 将指定的字符集添加到Content-Type响应标头。可以将数据从一个字符集转换为另一个字符集 |
| http_empty_gif_module | 发出单像素透明GIF。 |
| http_fastcgi_module | 将请求传递给FastCGI服务器。 |
| http_geo_module | 使用取决于客户端IP地址的值创建变量。 |
| http_gzip_module | 使用gzip压缩响应，将传输数据量减少一半或更多。 |
| http_limit_conn_module | 限制每个定义密钥的连接数，特别是来自单个IP地址的连接数。 |
| http_limit_req_module | 限制每个定义密钥的请求处理速率，特别是来自单个IP地址的请求的处理速率。 |
| http_map_module | 创建其值取决于其他变量值的变量。 |
| http_memcached_module | 将请求传递给memcached服务器。 |
| http_proxy_module | 将HTTP请求传递给另一台服务器。 |
| http_referer_module | 在Referer标头中阻止具有无效值的请求。 |
| http_rewrite_module | 使用正则表达式更改请求URI并返回重定向; 有条件地选择配置。需要PCRE库。 |
| http_scgi_module | 将请求传递给SCGI服务器。 |
| http_ssi_module | 在通过它的响应中处理SSI（服务器端包含）命令。 |
| http_split_clients_module | 创建适合A / B测试的变量，也称为拆分测试。 |
| http_upstream_hash_module | 启用通用哈希负载平衡方法。 |
| http_upstream_ip_hash_module | 启用IP哈希负载平衡方法。 |
| http_upstream_keepalive_module | 启用keepalive连接。 |
| http_upstream_least_conn_module | 启用最少连接负载平衡方法。 |
| http_upstream_zone_module | 启用共享内存区域。 |
| http_userid_module | 设置适合客户识别的cookie。 |
| http_uwsgi_module | 将请求传递给uwsgi服务器。 |

## 包括非默认构建的模块

默认情况下不构建许多NGINX模块，必须在configure要构建的命令行中列出
| 模块名称 | 描述 |
| :------: | :------: |
| --with-cpp_test_module | 测试头文件的C ++兼容性。 |
| --with-debug | 启用调试日志。 |
| --with-file-aio | 启用异步I / O. |
| --with-google_perftools_module | 允许使用[Google Performance](https://github.com/gperftools/gperftools)工具库 |
| -- 与-http_addition_module | 在响应之前和之后添加文本。 |
| -- 与-http_auth_request_module | 根据子请求的结果实现客户端授权。 |
| -- 与-http_dav_module | 使用WebDAV协议启用文件管理自动化。 |
| --with-http_degradation_module | 允许在内存大小超过定义的值时返回错误。 |
| -- 与-http_flv_module | 为Flash Video（FLV）文件提供伪流服务器端支持。 |
| -- 与-http_geoip_module | 允许创建其值取决于客户端IP地址的变量。该模块使用MaxMind GeoIP数据库。要编译为单独的动态模块，请将选项更改为-with-http_geoip_module = dynamic。 |
| -- 与-http_gunzip_module | 使用Content-Encoding解压缩响应：gzip用于不支持_zip_ encoding方法的客户端。 |
| -- 与-http_gzip_static_module | 允许使用.gz文件扩展名而不是常规文件发送预压缩文件。 |
| -- 与-http_image_filter_module | 以JPEG，GIF和PNG格式转换图像。该模块需要LibGD库。要编译为单独的动态模块，请将选项更改为--with-http_image_filter_module=dynamic。 |
| -- 与-http_mp4_module | 为MP4文件提供伪流服务器端支持。 |
| -- 与-http_perl_module | 用于在Perl中实现位置和变量处理程序，并将Perl调用插入SSI。需要PERL库。要编译为单独的动态模块，请将选项更改为--with-http_perl_module=dynamic。 |
| -- 与-http_random_index_module | 处理以斜杠字符（'/'）结尾的请求，并在目录中选择一个随机文件作为索引文件。 |
| -- 与-http_realip_module | 将客户端地址更改为在指定的标头字段中发送的地址。 |
| -- 与-http_secure_link_module | 用于检查请求的链接的真实性，保护资源免受未经授权的访问，并限制链接的生命周期。 |
| -- 与-http_slice_module | 允许将请求拆分为子请求，每个子请求返回一定范围的响应。提供更有效的大文件缓存。 |
| -- 与-http_ssl_module | 启用HTTPS支持。需要一个SSL库，如OpenSSL。 |
| -- 与-http_stub_status_module | 提供对基本状态信息的访问。请注意，NGINX Plus客户不需要此模块，因为它们已经提供了扩展状态指标和交互式仪表板。 |
| -- 与-http_sub_module | 通过将一个指定的字符串替换为另一个来修改响应。 |
| -- 与-http_xslt_module | 使用一个或多个XSLT样式表转换XML响应。该模块需要Libxml2和XSLT库。要编译为单独的动态模块，请将选项更改为--with-http_xslt_module=dynamic。 |
| -- 与-http_v2_module | 启用对HTTP / 2的支持。有关详细信息，请参阅NGINX博客上NGINX中的HTTP / 2模块。 |
| -- 用邮件 | 启用邮件代理功能。要编译为单独的动态模块，请将选项更改为--with-mail=dynamic。 |
| -- 与-mail_ssl_module | 为邮件代理服务器提供支持以使用SSL / TLS协议。需要一个SSL库，如OpenSSL。 |
| -- 与流 | 启用TCP和UDP代理功能。要编译为单独的动态模块，请将选项更改为--with-stream=dynamic。 |
| -- 与-stream_ssl_module | 为流代理服务器提供支持以使用SSL / TLS协议。需要一个SSL库，如OpenSSL。 |
| --with-threads | 使NGINX能够使用线程池。有关详细信息，请参阅NGINX Boost Performance 9x中的线程池！在NGINX博客上。 |

## 其他模块

    静态链接模块
    NGINX开源中内置的大多数模块都是静态链接的：它们在编译时内置于NGINX开源中，并静态链接到NGINX二进制文件。 只能通过重新编译NGINX来禁用这些模块。[NGINX Wiki](https://nginx.com/resources/wiki/modules/)中   列出了一些第三方模块
    要使用静态链接的第三方模块编译NGINX Open Source，请--add-module=<PATH>在configure命令中包含选项，   其中<PATH>是源代码的路径（此示例适用于RTMP 模块）：
    ./configure ... --add-module = / usr / build / nginx-rtmp-module

    动态链接模块
    NGINX模块也可以编译为共享对象（* .so文件），然后在运行时动态加载到NGINX Open Source中。这提供了更大的   灵活性，因为可以通过load_module在NGINX配置文件中添加或删除关联指令并重新加载配置来随时加载或卸载模块。 请注意，模块本身必须支持动态链接。
    要使用动态加载的第三方模块编译NGINX Open Source，请--add-dynamic-module=<PATH>在configure命令中 包含选项，其中<PATH>是源代码的路径：
    ./configure ... --add-dynamic-module = <PATH>
    生成的* .so文件将写入前缀/ modules /目录，其中前缀是服务器文件的目录，例如/ usr / local / nginx /。
    要加载动态模块，请load_module在安装后将指令添加到NGINX配置：
    load_module modules / ngx_mail_module.so;

## 初学者指南

```sh
要启动nginx，请运行可执行文件。启动nginx后，可以通过使用-s参数调用可执行文件来控制它。使用以下语法：
nginx -s signal
signal可以是下列之一：
stop - 快速关机
quit - 优雅的关机
reload - 重新加载配置文件
reopen - 重新打开日志文件

例如，要在等待工作进程完成当前请求的服务时停止nginx进程，可以执行以下命令：
nginx -s quit
此命令应在启动nginx的同一用户下执行。

nginx由模块组成，模块由配置文件指定的指令控制，指令分为简单指令和块指令，简单指令包括由空格分隔的名称和参数，以分号结尾，块指令和简单指令相同结构，但不以分号结尾，而是以大括号{}包围的一组附加指令结束，若块指令可以在大括号包含其他指令，则称为上下文。如：events,http,server和location
放置在任何上下文之外的配置文件中的指令都被认为是在主上下文中。在events和http指令驻留在main上下文server中，http和location在server中
```

## 静态内容

```sh
从/data/www目录提供文件，编辑配置文件并使用两个location块在http块内设置server块
将html等静态文件放入/data/www，打开配置文件，默认已包含几个server块实例
http {
  server {
  }
}
通常配置文件包括它监听的端口和服务器名称区分的若干个server块，一旦nginx决定对请求进行处理，它根据块的定义的指令参数测试请求头中指定的URI
将以下location块添加到server块中
location / {
  root /data/ww;
}
此location块指定/与请求中的URI进行比较的“/”前缀。对于匹配请求，URI将添加到root 指令中指定的路径 ，即to /data/www，以形成本地文件系统上所请求文件的路径。如果存在多个匹配location块，则nginx选择具有最长前缀的块。location上面的块提供长度为1的最短前缀，因此只有当所有其他location 块都无法提供匹配时，才会使用此块。
接着添加第二个location块：
location /images/ {
  root /data;
}
它将匹配以/images/开头的请求(location / 也匹配此类请求，但前缀更短)

server块 的结果配置应如下所示：

server {
    location / {
        root /data/www;
    }

    location /images/ {
        root /data;
    }
}
```

## 设置简单的代理服务器

```sh
nginx可以用作代理服务器，即服务器接受请求，将这些请求传递给代理服务器，从中检索响应，然后返回给客户端
我们将配置一个基本代理服务器，它使用来自本地目录的文件处理图像请求，并将所有其他请求发送到代理服务器。在此示例中，将在单个nginx实例上定义两个服务器。

向server nginx的配置文件添加一个以上内容来定义代理服务器，其中包含以下内容：

server {
    listen 8080;
    root /data/up1;

    location / {
    }
}
这将是一个侦听端口8080的简单服务器（之前，listen自使用标准端口80以来未指定该指令）并将所有请求映射到/data/up1本地文件系统上的目录。创建此目录并将index.html文件放入其中。请注意，该root指令放在 server上下文中，当location选择用于提供请求的块不包括自己的root指令时，使用这样的指令
接下来，使用上一节中的服务器配置并对其进行修改以使其成为代理服务器配置。在第一个location块中，将 proxy_pass 指令与参数中指定的代理服务器的协议，名称和端口放在一起（在我们的示例中，它是http://localhost:8080）：

server {
    location / {
        proxy_pass http：//localhost：8080;
    }

    location / images / {
        root / data;
    }
}
我们将修改第二个location 块，该块当前将带有/images/前缀的请求映射到目录下的/data/images文件，以使其与具有典型文件扩展名的图像请求相匹配。修改后的location块看起来像这样：
location ~ \.(gif|jpg|png)$ {
    root /data/images;
}
该参数是一个正则表达式匹配结尾的所有URI .gif，.jpg或.png。应该以正则表达式开头~。相应的请求将映射到该/data/images 目录。

当nginx选择一个location块来提供请求时，它首先检查 指定前缀的位置指令，记住location 最长的前缀，然后检查正则表达式。如果与正则表达式匹配，则nginx选择此项 location，否则，它会选择之前记住的那个。
生成的代理服务器配置如下所示：
server {
    location / {
        proxy_pass http://localhost:8080/;
    }

    location ~ \.(gif|jpg|png)$ {
        root /data/images;
    }
}
此服务器将过滤以.gif.jpg或者.png 将其映射到/data/images目录的请求（通过向root指令的参数添加URI ）并将所有其他请求传递给上面配置的代理服务器
```

## 设置FastCGI代理

```sh
nginx可用于将请求路由到FastCGI服务器，这些服务器运行使用各种框架和编程语言（如PHP）构建的应用程序。

使用FastCGI服务器的最基本的nginx配置包括使用 fastcgi_pass 指令而不是proxy_pass指令，以及fastcgi_param 指令来设置传递给FastCGI服务器的参数。假设可以访问FastCGI服务器localhost:9000。以上一节中的代理配置为基础，将proxy_pass指令替换为指令 fastcgi_pass并将参数更改为 localhost:9000。在PHP中，该SCRIPT_FILENAME参数用于确定脚本名称，该QUERY_STRING 参数用于传递请求参数。结果配置为：
server {
    location / {
        fastcgi_pass  localhost:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param QUERY_STRING    $query_string;
    }

    location ~ \.(gif|jpg|png)$ {
        root /data/images;
    }
}
这将设置一个服务器，该服务器将除静态图像请求之外的所有请求路由到localhost:9000通过FastCGI协议操作的代理服务器
```

## Nginx的conf配置文件详解

```nginx
nginx及其模块的工作方式在配置文件中确定。默认情况下，配置文件被命名nginx.conf 并放在目录 /usr/local/nginx/conf中 /etc/nginx，或 /usr/local/etc/nginx

特定于功能的配置文件
为了使配置更易于维护，我们建议您将其拆分为一组存储在/etc/nginx/conf.d目录中的特定于功能的文件，并使用include主nginx.conf文件中的指令来引用其中的内容
include conf.d/http;
include conf.d/stream;
include conf.d/exchange-enhanced;

一些顶级指令（称为Contexts）将适用于不同流量类型的指令组合在一起
events - 一般连接处理
http - HTTP流量
mail - 邮件流量
stream - TCP和UDP流量

在每个流量处理上下文中，您包含一个或多个server块来定义控制请求处理的虚拟服务器。您可以在server上下文中包含的指令因流量类型而异。

对于HTTP流量（http上下文），每个server指令控制对特定域或IP地址的资源请求的处理。location上下文中的一个或多个server上下文定义了如何处理特定的URI集。
对于邮件和TCP / UDP流量（mail和stream上下文），server指令各自控制到达特定TCP端口或UNIX套接字的流量的处理。
例如：

user nobody; # a directive in the 'main' context

events {
    # configuration of connection processing
}

http {
    # Configuration specific to HTTP and affecting all virtual servers  

    server {
        # configuration of HTTP virtual server 1
        location /one {
            # configuration for processing URIs starting with '/one'
        }
        location /two {
            # configuration for processing URIs starting with '/two'
        }
    }

    server {
        # configuration of HTTP virtual server 2
    }
}

stream {
    # Configuration specific to TCP/UDP and affecting all virtual servers
    server {
        # configuration of TCP virtual server 1
    }
}
```

```nginx

#定义Nginx运行的用户和用户组
user www www;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes auto;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log crit;

#进程文件
pid /var/run/nginx.pid;

#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
worker_rlimit_nofile 65535;

#工作模式与连接数上限
events
{
#参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的多路复用高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
use epoll;
#单个进程最大并发连接数（最大连接数=连接数*进程数）
worker_connections 65535;

# 并发总数是 worker_processes 和 worker_connections 的乘积
# 即 max_clients = worker_processes * worker_connections
# 在设置了反向代理的情况下，max_clients = worker_processes *worker_connections / 4  为什么
# 为什么上面反向代理要除以4，应该说是一个经验值
# 根据以上条件，正常情况下的Nginx Server可以应付的最大连接数为：4* 8000 = 32000
# worker_connections 值的设置跟物理内存大小有关
# 因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
# 而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上以打开的文件数大约是10万左右
# 我们来看看360M内存的VPS可以打开的文件句柄数是多少：
# $ cat /proc/sys/fs/file-max
# 输出 34336
# 32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，样就在操作系统可以承受的范围之内
# 所以，worker_connections 的值需根据 worker_processes 进程数和系统可以打开的最大文件总数进行适当地进行设置
# 使得并发总数小于操作系统可以打开的最大文件数目
# 其实质也就是根据主机的物理CPU和内存进行配置
# 当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的作进程需要消耗系统资源。
# ulimit -SHn 65535

multi_accept on;
}

#设定http服务器
http
{
include mime.types; #文件扩展名与文件类型映射表
default_type application/octet-stream; #默认文件类型
#charset utf-8; #默认编码
server_names_hash_bucket_size 128; #服务器名字的hash表大小
client_header_buffer_size 32k; #上传文件大小限制
large_client_header_buffers 4 64k; #设定请求缓存
client_max_body_size 8m; #设定请求缓存
sendfile on; #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数（zero copy 方式）来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
autoindex on; #开启目录列表访问，合适下载服务器，默认关闭。
tcp_nopush on; #防止网络阻塞
tcp_nodelay on; #防止网络阻塞
keepalive_timeout 120; #长连接超时时间，单位是秒

#FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
fastcgi_connect_timeout 300;
fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
fastcgi_buffer_size 64k;
fastcgi_buffers 4 64k;
fastcgi_busy_buffers_size 128k;
fastcgi_temp_file_write_size 256k;
fastcgi_intercept_errors on;

#gzip模块设置
gzip on; #开启gzip压缩输出
gzip_min_length 1k; #最小压缩文件大小
gzip_buffers 4 16k; #压缩缓冲区
gzip_http_version 1.1; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
gzip_comp_level 2; #压缩等级
gzip_types text/plain application/x-javascript text/javascript text/css application/xml;
#压缩类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
gzip_vary on;
#limit_zone crawler $binary_remote_addr 10m; #开启限制IP连接数的时候需要使用
gzip_proxied   expired no-cache no-store private auth;
gzip_disable   "MSIE [1-6]\.";
limit_conn_zone $binary_remote_addr zone=perip:10m;
limit_conn_zone $server_name zone=perserver:10m;

server_tokens off;
access_log off;

upstream www.19950128.com {
#upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
server 192.168.80.121:80 weight=3;
server 192.168.80.122:80 weight=2;
server 192.168.80.123:80 weight=3;
}

#虚拟主机的配置
server
{
  #监听端口
  listen 80;
  #域名可以有多个，用空格隔开
  server_name www.19950128.com;
  index index.html index.htm index.php;
  root /data/www/;

  #error_page   404   /404.html;
  include enable-php.conf;

  location ~ .*\.(php|php5|)?$
  {
  fastcgi_pass 127.0.0.1:9000;
  fastcgi_index index.php;
  include fastcgi.conf;
  }
  #图片缓存时间设置
  location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
  {
  expires 10d;
  }
  #JS和CSS缓存时间设置
  location ~ .*\.(js|css)?$
  {
  expires 1h;
  }
  # 定义错误提示页面
  error_page   500 502 503 504 /50x.html;
  location = /50x.html {
  }
  #日志格式设定
  log_format access '$remote_addr - $remote_user [$time_local] "$request" '
  '$status $body_bytes_sent "$http_referer" '
  '"$http_user_agent" $http_x_forwarded_for';
  #定义本虚拟主机的访问日志
  access_log /var/log/nginx/access.log;

  #对 "/" 启用反向代理
  location / {
  proxy_pass http://127.0.0.1:88;
  proxy_redirect off;
  proxy_set_header X-Real-IP $remote_addr;
  #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  #以下是一些反向代理的配置，可选。
  proxy_set_header Host $host;
  client_max_body_size 10m; #允许客户端请求的最大单文件字节数
  client_body_buffer_size 128k; #缓冲区代理缓冲用户端请求的最大字节数，
  proxy_connect_timeout 90; #nginx跟后端服务器连接超时时间(代理连接超时)
  proxy_send_timeout 90; #后端服务器数据回传时间(代理发送超时)
  proxy_read_timeout 90; #连接成功后，后端服务器响应时间(代理接收超时)
  proxy_buffer_size 4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
  proxy_buffers 4 32k; #proxy_buffers缓冲区，网页平均在32k以下的设置
  proxy_busy_buffers_size 64k; #高负荷下缓冲大小（proxy_buffers*2）
  proxy_temp_file_write_size 64k;
  #设定缓存文件夹大小，大于这个值，将从upstream服务器传
  }

  #设定查看Nginx状态的地址
  location /NginxStatus {
  stub_status on;
  access_log on;
  auth_basic "NginxStatus";
  auth_basic_user_file conf/htpasswd;
  #htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
  }

  #本地动静分离反向代理配置
  #所有jsp的页面均交由tomcat或resin处理
  location ~ .(jsp|jspx|do)?$ {
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_pass http://127.0.0.1:8080;
  }
  #所有静态文件由nginx直接读取不经过tomcat或resin
  location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$
  {
    expires 15d;
  }
  location ~ .*.(js|css)?$
  {
    expires 1h;
  }

#禁止访问 .htxxx 文件
  location ~ /.ht {
  deny all;
  }
}
}

```

nginx.conf配置补充扩展：

```conf

#静态文件，nginx自己处理
location ~ ^/(images|javascript|js|css|flash|media|static/ {
    #过期30天，静态文件不怎么更新，过期可以设大一点，
    #如果频繁更新，则可以设置得小一点。
    expires 30d;
}

#PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
location ~* \.php$ {
    fastcgi_index   index.php;
    fastcgi_pass    127.0.0.1:9000;
    include         fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME       $document_root$fastcgi_script_name;
    fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
  }

```

## nginx日志相关命令

```sh

nginx日志最好实现每天定时切割下，特别是在访问量比较大的时候，方便查看与处理，如果没切割，可以用sed直接切割，

切割日志

查找7月17日访问log导出到17.log文件中：

cat gelin_web_access.log | egrep "17/Jul/2017" | sed  -n '/00:00:00/,/23:59:59/p' > /tmp/17.log
查看访问量前10的IP

awk '{print $1}' 17.log | sort | uniq -c | sort -nr | head -n 10
查看访问前10的URL

awk '{print $11}' gelin_web_access.log | sort | uniq -c | sort -nr | head -n 10
查询访问最频繁的URL

awk '{print $7}' gelin_web_access.log | sort | uniq -c | sort -n -k 1 -r | more
查询访问最频繁的IP

awk '{print $1}' gelin_web_access.log | sort | uniq -c | sort -n -k 1 -r | more
根据访问IP统计UV

awk '{print $1}' gelin_web_access.log | sort | uniq -c | wc -l
统计访问URL统计PV

awk '{print $7}' gelin_web_access.log | wc -l
根据时间段统计查看日志

cat gelin_web_access.log | sed -n '/17\/Jul\/2017:12/,/17\/Jul\/2017:13/p' | more


```