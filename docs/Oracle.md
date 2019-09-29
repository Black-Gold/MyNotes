### oracle 内存调优办法

```oracle

当项目的生产环境出现性能问题，我们如何通过判断那些参数需要调整呢？

3.1 检查ORACLE实例的Library Cache命中率:

标准:一般是大于99％

检查方式:
select 1-(sum(reloads)/sum(pins)) "Library cache Hit Ratio" from v$librarycache;

处理措施:
如果Library cache Hit Ratio的值低于99%，应调高shared_pool_size的大小。通过sqlplus连接数据库执行如下命令，调整shared_pool_size的大小：
SQL>alter system flush shared_pool;
SQL>alter system set shared_pool_size=设定值 scope=spfile;

3.2 检查ORACLE实例的Data Buffer（数据缓冲区）命中率:

标准:一般是大于90%

检查方式:
select 1 - (phy.value / (cur.value + con.value)) "HIT RATIO" from v$sysstat cur, v$sysstat con, v$sysstat phy where cur.name = 'db block gets' and con.name = 'consistent gets' and phy.name = 'physical reads';

处理措施:
如果HIT RATIO的值低于90%，应调高db_cache_size的大小。通过sqlplus连接数据库执行如下命令
调整db_cache_size的大小
SQL>alter system set db_cache_size=设定值 scope=spfile

3.3 检查ORACLE实例的Dictionary Cache命中率:

标准:一般是大于95%

检查方式:
select 1 - (sum(getmisses) / sum(gets)) "Data Dictionary Hit Ratio" from v$rowcache;

处理措施:
如果Data Dictionary Hit Ratio的值低于95%，应调高shared_pool_size的大小。通过sqlplus连接数据库执行如下命令，调整shared_pool_size的大小：
SQL>alter system flush shared_pool;
SQL>alter system set shared_pool_size=设定值 scope=spfile;

3.4  检查ORACLE实例的Log Buffer命中率:

标准:一般是小于1%

检查方式:
select (req.value * 5000) / entries.value "Ratio" from v$sysstat req, v$sysstat entries where req.name = 'redo log space requests' and entries.name = 'redo entries';

处理措施:
如果Ratio高于1％，应调高log_buffer的大小。通过sqlplus连接数据库执行如下命令，调整log_buffer的大小：
SQL>alter system set log_buffer=设定值 scope=spfile;

3.5 检查undo_retention:

标准:undo_retention 的值必须大于max(maxquerylen)的值

检查方式:
col undo_retention format a30
select value "undo_retention" from v$parameter where name='undo_retention';
select max(maxquerylen) From v$undostat Where begin_time>sysdate-(1/4);

处理措施:
如果不满足要求，需要调高undo_retention 的值。通过sqlplus 连接数据库执行如下命
令，调整undo_retention 的大小：
SQL>alter system set undo_retention= 设定值 scope=spfile;

```