# MySQL安装

```mysql
数据定义语言(DDL ，Data Defintion Language)语句：数据定义语句，用于定义不同的数据段、数据库、表、列、索引等。常用的语句关键字包括create、drop、alter等
数据操作语言(DML ， Data Manipulation Language)语句：数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据的完整性。常用的语句关键字主要包括insert、delete、update和select等
数据控制语言(DCL， Data Control Language)语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括grant、revoke等



```

## MySQL配置文件

```conf

[client]
#password   = your_password
port        = 3306
socket        = /tmp/mysql.sock

[mysqld]
port        = 3306
socket        = /tmp/mysql.sock
datadir = /www/server/data
default_storage_engine = MyISAM
#skip-external-locking
#loose-skip-innodb
key_buffer_size = 8M
max_allowed_packet = 100G
table_open_cache = 32
sort_buffer_size = 256K
net_buffer_length = 4K
read_buffer_size = 128K
read_rnd_buffer_size = 256K
myisam_sort_buffer_size = 4M
thread_cache_size = 4
query_cache_size = 4M
tmp_table_size = 8M

#skip-networking
#skip-name-resolve
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
binlog_format=mixed
server-id   = 1
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
2、授权法。例如，你想myuser使用mypassword从任何主机连接到mysql服务器的话。
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
0或OFF：不启动缓存查询结果。
1或ON：缓存除了以SELECT SQL_NO_CACHE开头的所有查询结果。
2或DEMAND：只缓存以SELECT SQL_NO_CACHE开头的查询结果。
不缓存大于该值的结果。默认值是1048576(1MB)。：query_cache_limit
缓存查询结果分配的内存的数量。默认值是0，即禁用查询缓存。：query_cache_size
查询缓存分配的最小块的大小(字节)。 默认值是4096(4KB)。：query_cache_min_res_unit
query_cache_wlock_invalidate
0或OFF：当查询结果位于查询缓存中，客户端对MyISAM（默认存储引擎）表进行WRITE锁定时，其他客
户端可以对该表查询。
1或ON：锁定生效时，查询缓存内所有对该表进行的查询变得非法，强制访问该表的其他客户端进行等
待。
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



```sql

MySQL数据库备份和还原的常用命令

备份MySQL数据库的命令

mysqldump -hhostname -uusername -ppassword databasename > backupfile.sql

备份MySQL数据库为带删除表的格式
备份MySQL数据库为带删除表的格式，能够让该备份覆盖已有数据库而不需要手动删除原有数据库。

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