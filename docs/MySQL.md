# **MySQL**

```markdown
数据定义语言(DDL ，Data Defintion Language)语句：数据定义语句，用于定义不同的数据段、数据库、表、列、索引等。常用的语句关键字包括create、drop、alter等
数据操作语言(DML ， Data Manipulation Language)语句：数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据的完整性。常用的语句关键字主要包括insert、delete、update和select等
数据控制语言(DCL， Data Control Language)语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括grant、revoke等
```

## 安装mysql

```markdown
MySQL 5.7.6 and later:
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';

MySQL 5.7.5 and earlier:
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('MyNewPass');

flush privileges
从任何主机上使用root用户，密码：youpassword（你的root密码）连接到mysql服务器：
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'youpassword' WITH GRANT OPTION;


```

## MySQL配置文件

```markdown

[client]
#password   = your_password
port        = 3306
socket        = /tmp/mysql.sock

[mysqld]
user        = mysql
port        = 3306
socket        = /tmp/mysql.sock
datadir = /www/server/data
#default_storage_engine = MyISAM
default_storage_engine = InnoDB
#skip-external-locking
#loose-skip-innodb
key_buffer_size = 8M
max_allowed_packet = 100G
table_open_cache = 32
sort_buffer_size = 256K
join_buffer_size = 256k
net_buffer_length = 4K
read_buffer_size = 128K
read_rnd_buffer_size = 256K
myisam_sort_buffer_size = 4M
thread_cache_size = 4
thread_concurrency = 2
thread_stack = 192k
query_cache_size = 4M
query_cache_limit = 1M
query_cache_min_res_unit = 2k
long_query_time = 1
tmp_table_size = 8M
max_head_table_size = 2M
binlog_cache_size = 1M
max_binlog_cache_size = 1M
max_binlog_size = 2M
external-locking = FALSE
bulk_insert_buffer_size = 1M
lower_case_table_names = 1
slave-skip-errors = 1032,1062
replicate-ignore-db = mysql


#skip-networking
#skip-name-resolve
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535
back_log = 600
table_cache = 614

log-bin=mysql-bin   # 注意要在[mysqld]中配置
binlog_format=mixed
server-id   = 1     # 主从复制多实例中id均不能相同也要在[mysqld]中配置
expire_logs_days = 10

default_storage_engine = InnoDB
innodb_data_home_dir = /www/server/data
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /www/server/data
innodb_buffer_pool_size = 16M
innodb_additional_mem_pool_size = 2M
innodb_log_file_size = 5M
innodb_log_buffer_size = 8M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_file_io_threads = 4
innodb_thread_concurrency = 8
innodb_log_files_in_group = 3
innodb_max_dirty_pages_pct= 90
innodb_file_per_table = 0

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

```

## mysql相关命令

### mysql

```markdown
Usage: mysql [OPTIONS] [database]
  -?, --help          Display this help and exit.
  -I, --help          Synonym for -?
  --auto-rehash       Enable automatic rehashing. One doesn't need to use
                      'rehash' to get table and field completion, but startup
                      and reconnecting may take a longer time. Disable with
                      --disable-auto-rehash.
                      (Defaults to on; use --skip-auto-rehash to disable.)
  -A, --no-auto-rehash
                      No automatic rehashing. One has to use 'rehash' to get
                      table and field completion. This gives a quicker start of
                      mysql and disables rehashing on reconnect.
  --auto-vertical-output
                      Automatically switch to vertical output mode if the
                      result is wider than the terminal width.
  -B, --batch         Don't use history file. Disable interactive behavior.
                      (Enables --silent.)
  --bind-address=name IP address to bind to.
  --binary-as-hex     Print binary data as hex
  --character-sets-dir=name
                      Directory for character set files.
  --column-type-info  Display column type information.
  -c, --comments      Preserve comments. Send comments to the server. The
                      default is --skip-comments (discard comments), enable
                      with --comments.
  -C, --compress      Use compression in server/client protocol.
  -#, --debug[=#]     This is a non-debug version. Catch this and exit.
  --debug-check       This is a non-debug version. Catch this and exit.
  -T, --debug-info    This is a non-debug version. Catch this and exit.
  -D, --database=name Database to use.
  --default-character-set=name
                      Set the default character set.
  --delimiter=name    Delimiter to be used.
  --enable-cleartext-plugin
                      Enable/disable the clear text authentication plugin.
  -e, --execute=name  Execute command and quit. (Disables --force and history
                      file.)
  -E, --vertical      Print the output of a query (rows) vertically.
  -f, --force         Continue even if we get an SQL error.
  --histignore=name   A colon-separated list of patterns to keep statements
                      from getting logged into syslog and mysql history.
  -G, --named-commands
                      Enable named commands. Named commands mean this program's
                      internal commands; see mysql> help . When enabled, the
                      named commands can be used from any line of the query,
                      otherwise only from the first line, before an enter.
                      Disable with --disable-named-commands. This option is
                      disabled by default.
  -i, --ignore-spaces Ignore space after function names.
  --init-command=name SQL Command to execute when connecting to MySQL server.
                      Will automatically be re-executed when reconnecting.
  --local-infile      Enable/disable LOAD DATA LOCAL INFILE.
  -b, --no-beep       Turn off beep on error.
  -h, --host=name     Connect to host.
  -H, --html          Produce HTML output.
  -X, --xml           Produce XML output.
  --line-numbers      Write line numbers for errors.
                      (Defaults to on; use --skip-line-numbers to disable.)
  -L, --skip-line-numbers
                      Don't write line number for errors.
  -n, --unbuffered    Flush buffer after each query.
  --column-names      Write column names in results.
                      (Defaults to on; use --skip-column-names to disable.)
  -N, --skip-column-names
                      Don't write column names in results.
  --sigint-ignore     Ignore SIGINT (CTRL-C).
  -o, --one-database  Ignore statements except those that occur while the
                      default database is the one named at the command line.
  --pager[=name]      Pager to use to display results. If you don't supply an
                      option, the default pager is taken from your ENV variable
                      PAGER. Valid pagers are less, more, cat [> filename],
                      etc. See interactive help (\h) also. This option does not
                      work in batch mode. Disable with --disable-pager. This
                      option is disabled by default.
  -p, --password[=name]
                      Password to use when connecting to server. If password is
                      not given it's asked from the tty.
  -P, --port=#        Port number to use for connection or 0 for default to, in
                      order of preference, my.cnf, $MYSQL_TCP_PORT,
                      /etc/services, built-in default (3306).
  --prompt=name       Set the mysql prompt to this value.
  --protocol=name     The protocol to use for connection (tcp, socket, pipe,
                      memory).
  -q, --quick         Don't cache result, print it row by row. This may slow
                      down the server if the output is suspended. Doesn't use
                      history file.
  -r, --raw           Write fields without conversion. Used with --batch.
  --reconnect         Reconnect if the connection is lost. Disable with
                      --disable-reconnect. This option is enabled by default.
                      (Defaults to on; use --skip-reconnect to disable.)
  -s, --silent        Be more silent. Print results with a tab as separator,
                      each row on new line.
  -S, --socket=name   The socket file to use for connection.
  --ssl-mode=name     SSL connection mode.
  --ssl               Deprecated. Use --ssl-mode instead.
                      (Defaults to on; use --skip-ssl to disable.)
  --ssl-verify-server-cert
                      Deprecated. Use --ssl-mode=VERIFY_IDENTITY instead.
  --ssl-ca=name       CA file in PEM format.
  --ssl-capath=name   CA directory.
  --ssl-cert=name     X509 cert in PEM format.
  --ssl-cipher=name   SSL cipher to use.
  --ssl-key=name      X509 key in PEM format.
  --ssl-crl=name      Certificate revocation list.
  --ssl-crlpath=name  Certificate revocation list path.
  --tls-version=name  TLS version to use, permitted values are: TLSv1, TLSv1.1
  -t, --table         Output in table format.
  --tee=name          Append everything into outfile. See interactive help (\h)
                      also. Does not work in batch mode. Disable with
                      --disable-tee. This option is disabled by default.
  -u, --user=name     User for login if not current user.
  -U, --safe-updates  Only allow UPDATE and DELETE that uses keys.
  -U, --i-am-a-dummy  Synonym for option --safe-updates, -U.
  -v, --verbose       Write more. (-v -v -v gives the table output format).
  -w, --wait          Wait and retry if connection is down.
  --connect-timeout=# Number of seconds before connection timeout.
  --max-allowed-packet=#
                      The maximum packet length to send to or receive from
                      server.
  --net-buffer-length=#
                      The buffer size for TCP/IP and socket communication.
  --select-limit=#    Automatic limit for SELECT when using --safe-updates.
  --max-join-size=#   Automatic limit for rows in a join when using
                      --safe-updates.
  --secure-auth       Refuse client connecting to server if it uses old
                      (pre-4.1.1) protocol. Deprecated. Always TRUE
  --server-arg=name   Send embedded server this as a parameter.
  --show-warnings     Show warnings after every statement.
  -j, --syslog        Log filtered interactive commands to syslog. Filtering of
                      commands depends on the patterns supplied via histignore
                      option besides the default patterns.
  --plugin-dir=name   Directory for client-side plugins.
  --default-auth=name Default authentication client-side plugin to use.
  --binary-mode       By default, ASCII '\0' is disallowed and '\r\n' is
                      translated to '\n'. This switch turns off both features,
                      and also turns off parsing of all clientcommands except
                      \C and DELIMITER, in non-interactive mode (for input
                      piped to mysql or loaded using the 'source' command).
                      This is necessary when processing output from mysqlbinlog
                      that may contain blobs.
  --connect-expired-password
                      Notify the server that this client is prepared to handle
                      expired password sandbox mode.

Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf
The following groups are read: mysql client
The following options may be given as the first argument:
--print-defaults        Print the program argument list and exit.
--no-defaults           Don't read default options from any option file,
                        except for login file.
--defaults-file=#       Only read default options from the given file #.
--defaults-extra-file=# Read this file after the global files are read.
--defaults-group-suffix=#
                        Also read groups with concat(group, suffix)
--login-path=#          Read this path from the login file.

Variables (--variable-name=value)
and boolean options {FALSE|TRUE}  Value (after reading options)
--------------------------------- ----------------------------------------
auto-rehash                       FALSE
auto-vertical-output              FALSE
bind-address                      (No default value)
binary-as-hex                     FALSE
character-sets-dir                (No default value)
column-type-info                  FALSE
comments                          FALSE
compress                          FALSE
database                          (No default value)
default-character-set             utf8
delimiter                         ;
enable-cleartext-plugin           FALSE
vertical                          FALSE
force                             FALSE
histignore                        (No default value)
named-commands                    FALSE
ignore-spaces                     FALSE
init-command                      (No default value)
local-infile                      FALSE
no-beep                           FALSE
host                              (No default value)
html                              FALSE
xml                               FALSE
line-numbers                      TRUE
unbuffered                        FALSE
column-names                      TRUE
sigint-ignore                     FALSE
port                              3306
prompt                            mysql>
quick                             FALSE
raw                               FALSE
reconnect                         TRUE
socket                            /var/run/mysqld/mysqld.sock
ssl                               TRUE
ssl-verify-server-cert            FALSE
ssl-ca                            (No default value)
ssl-capath                        (No default value)
ssl-cert                          (No default value)
ssl-cipher                        (No default value)
ssl-key                           (No default value)
ssl-crl                           (No default value)
ssl-crlpath                       (No default value)
tls-version                       (No default value)
table                             FALSE
user                              (No default value)
safe-updates                      FALSE
i-am-a-dummy                      FALSE
connect-timeout                   0
max-allowed-packet                16777216
net-buffer-length                 16384
select-limit                      1000
max-join-size                     1000000
secure-auth                       TRUE
show-warnings                     FALSE
plugin-dir                        (No default value)
default-auth                      (No default value)
binary-mode                       FALSE
connect-expired-password          FALSE

```

### mysqladmin

```markdown
create databasename：创建一个新数据库
drop databasename：删除一个数据库及其所有表
extended-status：给出服务器的一个扩展状态消息
flush-hosts：清空所有缓存的主机
flush-logs：清空所有日志
flush-tables：清空所有表
flush-privileges：再次装载授权表(同reload)
kill id,id,...：杀死mysql线程
password 新口令：将老密码改为新密码
ping：检查mysqld是否活着
processlist：显示服务其中活跃线程列表
reload：重载授权表
refresh：清空所有表并关闭和打开日志文件
shutdown：关掉服务器
status：给出服务器的简短状态消息
variables：打印出可用变量
version：得到服务器的版本信息
```

### mysqlshow

```markdown
-h：MySQL服务器的ip地址或主机名
-u：连接MySQL服务器的用户名
-p：连接MySQL服务器的密码
--count：显示每个数据表中数据的行数
-k：显示数据表的索引
-t：显示数据表的类型
-i：显示数据表的额外信息
```

### mysqlimport

```markdown
-D：导入数据前清空表
-f：出现错误时继续处理剩余的操作
-h：MySQL服务器的ip地址或主机名
-u：连接MySQL服务器的用户名
-p：连接MySQL服务器的密码
```

### mysqldump

```markdown
--add-drop-table：在每个创建数据库表语句前添加删除数据库表的语句
--add-locks：备份数据库表时锁定数据库表
--all-databases：备份MySQL服务器上的所有数据库
--comments：添加注释信息
--compact：压缩模式，产生更少的输出
--complete-insert：输出完成的插入语句
--databases：指定要备份的数据库
--default-character-set：指定默认字符集
--force：当出现错误时仍然继续备份操作
--host：指定要备份数据库的服务器
--lock-tables：备份前，锁定所有数据库表
--no-create-db：禁止生成创建数据库语句
--no-create-info：禁止生成创建数据库库表语句
--password：连接MySQL服务器的密码
--port：MySQL服务器的端口号
--user：连接MySQL服务器的用户名
```

```bash
mysqldump -u linuxde -p smgp_apps_linuxde > linuxde.sql     # 导出整个数据库，格式：mysqldump -u 用户名 -p 数据库名 > 导出的文件名
mysqldump -u linuxde -p smgp_apps_linuxde users > linuxde_users.sql     # 导出一个表，格式：mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
mysqldump -u linuxde -p -d --add-drop-table smgp_apps_linuxde > linuxde_db.sql      # 导出一个数据库结构


```

### MySQL慢日志

```sql

慢日志开启方式和存储格式：
前期准备-查询未使用索引的查询：show variables like '%log_queri%';
记录未使用索引的查询：set global log_queries_not_using_indexes=on;
查询慢日志开启状态：show variables like 'slow_query_log';
开启慢日志：set global slow_query_log=on;
把大于10毫秒的查询记录到日志里：show variables like 'long_query_time';
查看慢日志位置：show variables like 'slow_query_log_file%';
查看mysql各内存的分配情况：show variables like 'innodb_%_size';
查看innodb_buffer_pool的运行状态：show engine innodb status;
通过语句查看MySQL数据储存位置：show global variables like "%datadir%";
为了防止查询缓存经常被掏空(Potentially Undersized)，最好是修改系统变量，改变变量之前，先查看变量声
明的当前值：show variables like 'query_cache%';

```

```sql

授权用户从远程登录:
1、改表法。可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登
入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"
mysql -u root -pvmware;
mysql>use mysql;
mysql>update user set host = '%' where user = 'root';
mysql>select host, user from user;
2、授权法。例如，你想myuser使用mypassword从任何主机连接到mysql服务器的话
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
如果你想允许用户myuser从ip为192.168.1.3的主机连接到mysql服务器，并使用mypassword作为密码
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword' WITH GRANT
OPTION;
【下面这一句一定要执行，否则还是无法登陆】
mysql>flush privileges ;
如果root用户无法从本地登陆，这个时候就执行如下
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost.localdomain' IDENTIFIED BY '密码' WITH GRANT
OPTION;
查看查询缓存系统属性：show variables like '%_cache_%';
设置查询缓存类型：query_cache_type
0或OFF：不启动缓存查询结果
1或ON：缓存除了以SELECT SQL_NO_CACHE开头的所有查询结果
2或DEMAND：只缓存以SELECT SQL_NO_CACHE开头的查询结果
不缓存大于该值的结果。默认值是1048576(1MB)。：query_cache_limit
缓存查询结果分配的内存的数量。默认值是0，即禁用查询缓存。：query_cache_size
查询缓存分配的最小块的大小(字节)。 默认值是4096(4KB)。：query_cache_min_res_unit
query_cache_wlock_invalidate
0或OFF：当查询结果位于查询缓存中，客户端对MyISAM（默认存储引擎）表进行WRITE锁定时，其他客
户端可以对该表查询
1或ON：锁定生效时，查询缓存内所有对该表进行的查询变得非法，强制访问该表的其他客户端进行等
待
查看最大连接数：show variables like '%CONNECTION%';
MySQL建表语句大全
1. 创建表（含有约束）
/**创建组表**/
create table t_group(
id int(11) auto_increment primary key,
name varchar(50),
value varchar(255)
)engine=InnoDB default charset=utf8;
/**创建用户表**/
create table t_user(
id int(11) auto_increment primary key,
name varchar(50) unique,
value varchar(255) default '0',
email varchar(100) not null,
sex varchar(1) check(user_sex=1 or user_sex=2),
group_id int(11),
create_time timestamp,
constraint foreign key(group_id) references t_group(id) on delete cascade
)engine=InnoDB default charset=utf8;
/**查看表信息**/
desc t_group;
show create table t_group;
desc t_user;
show create table t_user;
自动增长：auto_increment 创建主键：primary key 唯一约束: unique 非空约束：not null
检查约束: check(user_sex=1 or user_sex=2)
创建外键：constraint foreign key(group_id) references t_group(id) on delete cascade
2、创建表（不含约束）
/**创建组表**/
create table t_group(
id int(11),
name varchar(50),
value varchar(255)
)engine=InnoDB default charset=utf8;
/**创建用户表**/
create table t_user(
id int(11),
name varchar(50),
value varchar(255) default '0',
email varchar(100),
sex varchar(1),
group_id int(11),
create_time timestamp
)engine=InnoDB default charset=utf8;
/**查看表信息**/
desc t_group;
show create table t_group;
desc t_user;
show create table t_user;
3. 创建约束
/**创建主键约束**/
alter table t_group add constraint group_id_pk primary key(id);
alter table t_user add constraint user_id_pk primary key(id);
/**自动增长**/
alter table t_group modify id int(11) auto_increment;
alter table t_user modify id int(11) auto_increment;
/**创建外键约束**/
alter table t_user add constraint user_group_id_fk foreign key(group_id) references t_group(id);
/**创建唯一约束**/
alter table t_group add constraint group_name_uk unique(name);
alter table t_user add constraint user_name_uk unique(name);
/**创建非空约束**/
alter table t_user add constraint user_email_nk check(email is not null);
/**创建检查约束**/
alter table t_user add constraint user_gender_ck check(user_sex=1 or user_sex=2);
4. 删除约束
/**删除唯一约束**/
alter table t_user drop unique key user_name_uk;
/**删除外键约束**/
alter table t_user drop foreign key user_group_id_fk;
5. 修改表字段
/**增加字段**/
alter table t_group add description varchar(255);
alter table t_user add description varchar(255);
/**修改字段**/
alter table t_group modify description varchar(20);
alter table t_user modify description varchar(20);
/**删除字段**/
alter table t_group drop description;
alter table t_user drop description;
6.删除表
drop table t_user;
drop table t_group;



```bash

MySQL数据库备份和还原的常用命令

备份MySQL数据库的命令

mysqldump -hhostname -uusername -ppassword databasename > backupfile.sql

备份MySQL数据库为带删除表的格式
备份MySQL数据库为带删除表的格式，能够让该备份覆盖已有数据库而不需要手动删除原有数据库

mysqldump -–add-drop-table -uusername -ppassword databasename > backupfile.sql

直接将MySQL数据库压缩备份

mysqldump -hhostname -uusername -ppassword databasename | gzip > backupfile.sql.gz

备份MySQL数据库某个(些)表

mysqldump -hhostname -uusername -ppassword databasename specific_table1 specific_table2 > backupfile.sql

同时备份多个MySQL数据库

mysqldump -hhostname -uusername -ppassword –databases databasename1 databasename2 databasename3 > multibackupfile.sql

仅仅备份数据库结构

mysqldump –no-data –databases databasename1 databasename2 databasename3 > structurebackupfile.sql

备份服务器上所有数据库

mysqldump –all-databases > allbackupfile.sql

还原MySQL数据库的命令

mysql -hhostname -uusername -ppassword databasename < backupfile.sql

还原压缩的MySQL数据库

gunzip < backupfile.sql.gz | mysql -uusername -ppassword databasename

将数据库转移到新服务器

mysqldump -uusername -ppassword databasename | mysql –host=*.*.*.* -C databasename

```

下面简单描述MySQL Repl ica tion的复制原理过程
1)在Slave服务器上执行startslave命令开启主从复制开关， 开始进行主从复制
2)此时 Slave服务器的1/0线程会通过在Master上已经授权的复制用户权限请求连接Master服务器， 并请求从指定binlog日志文件的指定
位置（日志文件名和位置就是在配置主从复制服务时执行changemaster命令指定的）之后开始发送binlog日志内容
3) Master服务器接收到来自Slave服务器的1/0线程的请求后， 其上负责复制的l/0线程会根据Slave服务器的1/0线程请求的信息分批读
取指定binlog日志文件指定位登之后的binlog日志信息， 然后返回给Slave端的1/0线程。 返回的信息中除了binlog日 志内容外， 还有
在Master服务器端记录的新的binlog文件名称， 以及在新的binlog中的下一个指定更新位置
4)当Slave服务器的1/0线程获取到Master服务器上1/0线程发送的日志内容、日志文件及位置点后， 会将binlog日志内容依次写到Slave
端自身的Relay Log (即中继日志）文件(MySQL-relay-bin.xxxxxx)的最末端， 并将新的binlog文件名和位置记录到 master-info文件中
以便下一次读取Master端新biolog日志时能够告诉Master服务器 从新binlog日志的指定文件及位置开始请求新的binlog日志内容
5) Slave服务器端的SQL线程会实时检测本地RelayLog中1/0线程新增加的日志内容， 然后及时地把Relay Log文件中的内容解析成SQL语句
并在自身Slave服务器上按解析SQL语句的位置顺序执行应用这些SQL语句， 并在relay- log.info中记录当前应用中继日志的文件名及位置点
经过了上面的过程， 就可以确保在Master端和Slave端执行了同样的SQL语句。当复制状态正常时， Master端和Slave端的数据是完全一样的
当然， MySQL的复制机制也有一些特殊情况， 具体请参考官方的说明

MySQL主从复制原理重点小结
□	主从复制是异步的逻辑的SQL语句级的复制
D复制时， 主库有一个VO线程， 从库有两个线程， 即1/0和SQL线程
□	实现主从复制的必要条件是主库要开启记录binlog功能
D作为复制的所有MySQL节点的server-id都不能相同
□ binlog文件只记录对数据库有更改的SQL语句（来自主数据库内容的变更）， 不记录任何查询（如select、show)语句