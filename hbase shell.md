

# 一,具体实践

## 1.1 基本操作

### 1.进入HBase客户端命令行

~~~shell
[root@node1 hbase]$ bin/hbase shell
~~~

### 2.查看帮助命令

~~~shell
hbase(main):001:0> help
# 查看指定命令帮助
help "命令名"
~~~

### 3.查看当前数据库中有哪些表

~~~shell
hbase(main):002:0> list
~~~

## 1.2 表的操作

### 1.创建表

~~~shell
hbase(main):002:0> create 'student','info'
~~~

### 2.插入数据到表

~~~shell
hbase(main):003:0> put 'student','1001','info:sex','male'
hbase(main):004:0> put 'student','1001','info:age','18'
hbase(main):005:0> put 'student','1002','info:name','Janna'
hbase(main):006:0> put 'student','1002','info:sex','female'
hbase(main):007:0> put 'student','1002','info:age','20'
~~~

### 3.扫描查看表数据

~~~shell
hbase(main):008:0> scan 'student'
hbase(main):009:0> scan 'student',{STARTROW => '1001', STOPROW  => '1001'}
hbase(main):010:0> scan 'student',{STARTROW => '1001'}
~~~

### 4.查看表结构

~~~shell
hbase(main):011:0> describe 'student'
~~~

### 5.更新指定字段的数据

~~~shell
hbase(main):012:0> put 'student','1001','info:name','Nick'
hbase(main):013:0> put 'student','1001','info:age','100'
~~~

### 6.查看“指定行”或“指定列族:列”的数据

~~~shell
hbase(main):014:0> get 'student','1001'
hbase(main):015:0> get 'student','1001','info:name'

~~~

### 7.统计表数据行数

~~~shell
hbase(main):021:0> count 'student'

~~~

### 8.删除数据

删除某rowkey的全部数据：

~~~shell
hbase(main):016:0> deleteall 'student','1001'

~~~

删除某rowkey的某一列数据：

~~~shell
hbase(main):017:0> delete 'student','1002','info:sex'

~~~

### 9.清空表数据

~~~shell
hbase(main):018:0> truncate 'student'

~~~

**提示**：清空表的操作顺序为先disable，然后再truncate。

### 10.删除表

首先需要先让该表为disable状态：

~~~shell
hbase(main):019:0> disable 'student'

~~~

然后才能drop这个表：

~~~shell
hbase(main):020:0> drop 'student'

~~~

**提示**：如果直接drop表，会报错：ERROR: Table student is enabled. Disable it first.

### 11.变更表信息

将info列族中的数据存放3个版本：

~~~shell
hbase(main):022:0> alter 'student',{NAME=>'info',VERSIONS=>3}
hbase(main):022:0> get 'student','1001',{COLUMN=>'info:name',VERSIONS=>3}

~~~

# 二,官方命令介绍

## shell中所有命令

~~~shell
Group name: general
  Commands: status, table_help, version, whoami

Group name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, locate_region, show_filters

Group name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

Group name: dml
  Commands: append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve

Group name: tools
  Commands: assign, balance_switch, balancer, balancer_enabled, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, compact_mob, compact_rs, flush, is_in_maintenance_mode, major_compact, major_compact_mob, merge_region, move, normalize, normalizer_enabled, normalizer_switch, split, splitormerge_enabled, splitormerge_switch, trace, unassign, wal_roll, zk_dump

Group name: replication
  Commands: add_peer, append_peer_tableCFs, disable_peer, disable_table_replication, enable_peer, enable_table_replication, get_peer_config, list_peer_configs, list_peers, list_replicated_tables, remove_peer, remove_peer_tableCFs, set_peer_tableCFs, show_peer_tableCFs, update_peer_config

Group name: snapshots
  Commands: clone_snapshot, delete_all_snapshot, delete_snapshot, list_snapshots, restore_snapshot, snapshot

Group name: configuration
  Commands: update_all_config, update_config

Group name: quotas
  Commands: list_quotas, set_quota

Group name: security
  Commands: grant, list_security_capabilities, revoke, user_permission

Group name: procedures
  Commands: abort_procedure, list_procedures

Group name: visibility labels
  Commands: add_labels, clear_auths, get_auths, list_labels, set_auths, set_visibility

Group name: rsgroup
  Commands: add_rsgroup, balance_rsgroup, get_rsgroup, get_server_rsgroup, get_table_rsgroup, list_rsgroups, move_servers_rsgroup, move_tables_rsgroup, remove_rsgroup

~~~

## help:查看帮助命令

输入“help”命令，例如:help "get" (引号是必须的)   查看指定命令帮助

~~~shell
hbase(main):001:0> help

~~~

## list:查看当前数据库中有哪些表

list: 列出hbase中的所有表。可以使用正则表达式参数来过滤输出 

~~~shell
hbase> list
hbase> list 'abc.*'
hbase> list 'ns:abc.*'
hbase> list 'ns:.*'

~~~

## create:创建表

创建一个表。传递一个表名、一组列族规范(至少一个)和表的配置(可选)。

列规范可以是一个简单的字符串(名称)，也可以是一个字典(字典在下面的主帮助输出中进行了描述)，其中必须包含name属性

> ns:namespace
>
> t:table
>
> r:rowkey
>
> f:colume family
>
> c:colume

 创建一个具有名称空间=ns1和表限定符=t1的表 

~~~shell
hbase> create 'ns1:t1', {NAME => 'f1', VERSIONS => 5}

~~~

 创建一个名称空间为默认，表限定符为t1的表

~~~shell
hbase> create 't1', {NAME => 'f1'}, {NAME => 'f2'}, {NAME => 'f3'}
# 以上简略地说，如下:
hbase> create 't1', 'f1', 'f2', 'f3'
hbase> create 't1', {NAME => 'f1', VERSIONS => 1, TTL => 2592000, BLOCKCACHE => true}
hbase> create 't1', {NAME => 'f1', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '10'}}
hbase> create 't1', {NAME => 'f1', IS_MOB => true, MOB_THRESHOLD => 1000000, MOB_COMPACT_PARTITION_POLICY => 'weekly'}

~~~

 表配置选项可以放在最后。 

~~~shell
hbase> create 'ns1:t1', 'f1', SPLITS => ['10', '20', '30', '40']
hbase> create 't1', 'f1', SPLITS => ['10', '20', '30', '40']
hbase> create 't1', 'f1', SPLITS_FILE => 'splits.txt', OWNER => 'johndoe'
hbase> create 't1', {NAME => 'f1', VERSIONS => 5}, METADATA => { 'mykey' => 'myvalue' }
# 可选择使用SPLITALGO ("HexStringSplit", "UniformSplit" or classname)将表预先分割为NUMREGIONS
hbase> create 't1', 'f1', {NUMREGIONS => 15, SPLITALGO => 'HexStringSplit'}
hbase> create 't1', 'f1', {NUMREGIONS => 15, SPLITALGO => 'HexStringSplit', REGION_REPLICATION => 2, CONFIGURATION => {'hbase.hregion.scan.loadColumnFamiliesOnDemand' => 'true'}}
hbase> create 't1', {NAME => 'f1', DFS_REPLICATION => 1}

~~~

 您还可以保留对所创建表的引用 

~~~shell
hbase> t1 = create 't1', 'f1'

~~~

 它为你提供对名为't1'的表的引用，然后您可以在上面调用方法。

## put:插入(更新)数据到表

 将单元格“值”放在指定的表/行/列和时间戳坐标(可选)。 

 若要将单元格值放入标有时间'ts1'的列'c1'下的行'r1'的表'ns1:t1'或't1'中，请执行以下操作: 

> put '表名','rowkey','列簇:列名','value'

~~~shell
hbase> put 'ns1:t1', 'r1', 'c1', 'value'
hbase> put 't1', 'r1', 'c1', 'value'
hbase> put 't1', 'r1', 'c1', 'value', ts1
hbase> put 't1', 'r1', 'c1', 'value', {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> put 't1', 'r1', 'c1', 'value', ts1, {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> put 't1', 'r1', 'c1', 'value', ts1, {VISIBILITY=>'PRIVATE|SECRET'}

~~~

 同样的命令也可以在表引用上运行。假设您有一个对表“t1”的引用t，那么相应的命令将是 

~~~shell
hbase> t.put 'r1', 'c1', 'value', ts1, {ATTRIBUTES=>{'mykey'=>'myvalue'}}

~~~

## scan:扫描查看表数据

 扫描一个表;传递表名和扫描仪规范的字典(可选)。  

 扫描仪的规格可能包括一个或多个:TIMERANGE( 时间范围 ), FILTER( 过滤器 ), LIMIT, STARTROW( 起始行 ), STOPROW(结束行), ROW PREFIX FILTER(行前缀过滤器),TIMESTAMP(时间戳),MAXLENGTH or COLUMNS, CACHE or RAW, VERSIONS, ALL_METRICS or METRICS

如果没有指定列，则扫描所有列。

要扫描列族的所有成员，将限定符留空，如'col_family'。

过滤器可以通过两种方式指定:

1. 使用filterString——有关这方面的更多信息可以在附加到HBASE-4176 JIRA的过滤器语言文档中找到

2. 使用筛选器的整个包名。

如果您希望看到有关扫描执行的指标，ALL_METRICS布尔值应该设置为true。或者，如果您希望只看到度量的一个子集，那么可以定义度量数组来只包含您关心的度量的名称。

~~~shell
hbase> scan 'hbase:meta'
hbase> scan 'hbase:meta', {COLUMNS => 'info:regioninfo'}
hbase> scan 'ns1:t1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
hbase> scan 't1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
hbase> scan 't1', {COLUMNS => 'c1', TIMERANGE => [1303668804, 1303668904]}
hbase> scan 't1', {REVERSED => true}
hbase> scan 't1', {ALL_METRICS => true}
hbase> scan 't1', {METRICS => ['RPC_RETRIES', 'ROWS_FILTERED']}
hbase> scan 't1', {ROWPREFIXFILTER => 'row2', FILTER => "(QualifierFilter (>=, 'binary:xyz')) AND (TimestampsFilter ( 123, 456))"}
hbase> scan 't1', {FILTER =>org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}
hbase> scan 't1', {CONSISTENCY => 'TIMELINE'}

~~~

 设置操作属性 

~~~shell
hbase> scan 't1', { COLUMNS => ['c1', 'c2'], ATTRIBUTES => {'mykey' => 'myvalue'}}
hbase> scan 't1', { COLUMNS => ['c1', 'c2'], AUTHORIZATIONS => ['PRIVATE','SECRET']}

~~~

## desc/describe:查看表结构

~~~shell
hbase(main):011:0> describe 'student'
hbase(main):011:0> desc 'student'

~~~

## get:获取指定行或单元格内容

 获取指定行或单元格内容;传递表名、行和 列、时间戳、时间范围和版本的字典(可选) 

~~~shell
hbase> get 'ns1:t1', 'r1'
hbase> get 't1', 'r1'
hbase> get 't1', 'r1', {TIMERANGE => [ts1, ts2]}
hbase> get 't1', 'r1', {COLUMN => 'c1'}
hbase> get 't1', 'r1', {COLUMN => ['c1', 'c2', 'c3']}
hbase> get 't1', 'r1', {COLUMN => 'c1', TIMESTAMP => ts1}
hbase> get 't1', 'r1', {COLUMN => 'c1', TIMERANGE => [ts1, ts2], VERSIONS => 4}
hbase> get 't1', 'r1', {COLUMN => 'c1', TIMESTAMP => ts1, VERSIONS => 4}
hbase> get 't1', 'r1', {FILTER => "ValueFilter(=, 'binary:abc')"}
hbase> get 't1', 'r1', 'c1'
hbase> get 't1', 'r1', 'c1', 'c2'
hbase> get 't1', 'r1', ['c1', 'c2']
hbase> get 't1', 'r1', {COLUMN => 'c1', ATTRIBUTES => {'mykey'=>'myvalue'}}
hbase> get 't1', 'r1', {COLUMN => 'c1', AUTHORIZATIONS => ['PRIVATE','SECRET']}
hbase> get 't1', 'r1', {CONSISTENCY => 'TIMELINE'}
hbase> get 't1', 'r1', {CONSISTENCY => 'TIMELINE', REGION_REPLICA_ID => 1}

~~~

## alter:变更表信息

改变一个表。如果`hbase.online.schema.update`属性设置为false，则必须禁用该表

如果`hbase.online.schema.update`属性设置为true，可以更改表而不需要先禁用它们

更改启用的表会导致一些问题，所以要谨慎使用，并在生产环境中使用之前进行测试。

可以使用alter命令添加、修改或删除列族或更改表配置选项。

列族的工作方式与“创建”命令类似。

列族规范可以是名称字符串，也可以是具有name属性的字典。

字典是在“帮助”命令的输出中描述的，没有参数。

**举例**

 从当前值更改或添加表“t1”中的“f1”列族，以保持最多5个单元格版本 

~~~shell
hbase> alter 't1', NAME => 'f1', VERSIONS => 5

~~~

可以对多个列族进行操作 

~~~shell
hbase> alter 't1', 'f1', {NAME => 'f2', IN_MEMORY => true}, {NAME => 'f3', VERSIONS => 5}

~~~

删除表'ns1:t1'中的'f1'列族 

~~~shell
hbase> alter 'ns1:t1', NAME => 'f1', METHOD => 'delete'
hbase> alter 'ns1:t1', 'delete' => 'f1'

~~~

您还可以更改表范围的属性，如MAX_FILESIZE、READONLY、MEMSTORE_FLUSHSIZE、持久性等。

这些可以放在最后;例如，将一个区域的最大大小更改为128MB

~~~shell
hbase> alter 't1', MAX_FILESIZE => '134217728'

~~~

 可以通过设置表协处理器属性来添加表协处理器 

~~~shell
hbase> alter 't1',
    'coprocessor'=>'hdfs:///foo.jar|com.foo.FooRegionObserver|1001|arg1=1,arg2=2'

~~~

 由于可以为一个表配置多个协处理器，因此将自动将序列号附加到属性名以惟一地标识它 

 为了让框架理解如何加载协处理器类，协处理器属性必须与下面的模式匹配 

~~~
[coprocessor jar file location] | class name | [priority] | [arguments]

~~~

 您还可以为指定表和列族进行配置设置 

~~~shell
hbase> alter 't1', CONFIGURATION => {'hbase.hregion.scan.loadColumnFamiliesOnDemand' => 'true'}
hbase> alter 't1', {NAME => 'f2', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '10'}}

~~~

 您还可以删除表范围属性 

~~~shell
hbase> alter 't1', METHOD => 'table_att_unset', NAME => 'MAX_FILESIZE'
hbase> alter 't1', METHOD => 'table_att_unset', NAME => 'coprocessor$1'

~~~

 您还可以设置region副本数

~~~shell
hbase> alter 't1', {REGION_REPLICATION => 2}

~~~

可以在一个命令中进行多次修改

~~~shell
hbase> alter 't1', {NAME => 'f1', VERSIONS => 3 },{MAX_FILESIZE => '134217728' }, {METHOD => 'delete', NAME => 'f2' },OWNER => 'johndoe', METADATA => { 'mykey' => 'myvalue' }

~~~

## delete:删除数据

在指定的表/行/列和可选的时间戳坐标上放置一个delete单元格值。 

删除操作必须与被删除的单元格的坐标完全匹配。 

当扫描时，删除单元格将取消旧版本。

在表t1中从标有时间'ts1'的列'c1'下'行'r1'中删除一个单元格

~~~shell
hbase> delete 'ns1:t1', 'r1', 'c1', ts1
hbase> delete 't1', 'r1', 'c1', ts1
hbase> delete 't1', 'r1', 'c1', ts1, {VISIBILITY=>'PRIVATE|SECRET'}

~~~

同样的命令也可以在表引用上运行。

假设您有一个对表“t1”的引用t，那么相应的命令将是

~~~shell
hbase> t.delete 'r1', 'c1',  ts1
hbase> t.delete 'r1', 'c1',  ts1, {VISIBILITY=>'PRIVATE|SECRET'}

~~~

## deleteall:删除所有单元格

 删除给定行中的所有单元格;传递一个表名、行，也可以传递一个列和时间戳 

~~~shell
hbase> deleteall 'ns1:t1', 'r1'
hbase> deleteall 't1', 'r1'
hbase> deleteall 't1', 'r1', 'c1'
hbase> deleteall 't1', 'r1', 'c1', ts1
hbase> deleteall 't1', 'r1', 'c1', ts1, {VISIBILITY=>'PRIVATE|SECRET'}

~~~

同样的命令也可以在表引用上运行。假设你有一个参考t到表“t1”，对应的命令是

~~~shell
hbase> t.deleteall 'r1'
hbase> t.deleteall 'r1', 'c1'
hbase> t.deleteall 'r1', 'c1', ts1
hbase> t.deleteall 'r1', 'c1', ts1, {VISIBILITY=>'PRIVATE|SECRET'}

~~~

## drop/drop_all: 删除指定的表

drop 删除指定的表。必须首先禁用表 

~~~shell
hbase> drop 't1'
hbase> drop 'ns1:t1'

~~~

drop_all 删除所有与给定正则表达式匹配的表 

~~~shell
hbase> drop_all 't.*'
hbase> drop_all 'ns:t.*'
hbase> drop_all 'ns:.*'

~~~

## count:统计表数据行数

计算表中的行数。返回值是行数。

这个操作可能需要很长时间(运行'$HADOOP_HOME/bin/hadoop jar hbase.jar rowcount'来运行一个计数mapreduce作业)。

默认情况下，每1000行显示当前计数。

计数间隔可以指定。 

默认情况下，在计数扫描时启用扫描缓存。 

默认缓存大小为10行。 

如果您的行数量比较少，您可能需要增加这个参数 

~~~shell
hbase> count 'ns1:t1'
hbase> count 't1'
hbase> count 't1', INTERVAL => 100000
hbase> count 't1', CACHE => 1000
hbase> count 't1', INTERVAL => 10, CACHE => 1000

~~~

同样的命令也可以在表引用上运行。

假设您有一个对表“t1”的引用t，那么相应的命令将是如下

~~~shell
hbase> t.count
hbase> t.count INTERVAL => 100000
hbase> t.count CACHE => 1000
hbase> t.count INTERVAL => 10, CACHE => 1000

~~~

## truncate:清空表数据

禁用、删除和重新创建指定的表。 

## status: 显示集群状态 

可以是“”、“简单”、“详细”或“复制”。

默认是'summary'

~~~shell
hbase> status
hbase> status 'simple'
hbase> status 'summary'
hbase> status 'detailed'
hbase> status 'replication'
hbase> status 'replication', 'source'
hbase> status 'replication', 'sink'

~~~

## table_help: 表引用命令的帮助 

从0.96开始，您还可以获得对表的引用，您可以在该表上调用命令。

例如，您可以创建一个表，并通过

~~~shell
hbase> t = create 't', 'cf'

~~~

 如果您已经创建了表，您可以获得对它的引用 

~~~shell
hbase> t = get_table 't'

~~~

你可以通过引用做一些事情,如同put命令

 将列族为“cf”、限定符为“q”和值为“v”的行“r”放入表t中 

~~~shell
hbase> t.put 'r', 'cf:q', 'v'

~~~

为了读取数据，可以扫描表 

~~~shell
hbase> t.scan

~~~

它将读取表t中的所有行 

本质上，任何接受表名的命令也可以通过表引用来执行。

其他命令包括:get、delete、deleteall、get_all_columns、get_counter、count、incr。这些函数以及标准的JRuby对象方法也可以通过选项卡完成

有关如何使用这些命令的更多信息，也可以输入 

~~~shell
hbase> t.help 'scan'

~~~

这将输出更多关于如何使用该命令的信息 

您还可以直接对表执行一般管理操作;例如启用、禁用、刷新和删除等操作只需输入以下命令即可 

~~~shell
hbase> t.enable
hbase> t.flush
hbase> t.disable
hbase> t.drop

~~~

 请注意，删除表之后，对它的引用将变得无用，并且没有定义进一步的用法 

## whoami: 显示当前的hbase用户 

~~~shell
hbase(main):017:0> whoami
root (auth:SIMPLE)
    groups: root

~~~

## disable/disable_all : 禁用指定表 

~~~shell
hbase> disable 't1'
hbase> disable 'ns1:t1'
~~~

禁用所有与给定正则表达式匹配的表

~~~shell
hbase> disable_all 't.*'
hbase> disable_all 'ns:t.*'
hbase> disable_all 'ns:.*'
~~~

##  enable/enable_all:启用指定表

~~~shell
hbase> enable 't1'
hbase> enable 'ns1:t1'
~~~

 启用所有与给定正则表达式匹配的表 

~~~shell
hbase> enable_all 't.*'
hbase> enable_all 'ns:t.*'
hbase> enable_all 'ns:.*'
~~~

## exists:查看指定表是否存在

~~~shell
hbase> exists 't1'
hbase> exists 'ns1:t1'
~~~

## append:追加值

 在指定的表/行/列坐标处附加一个单元格'值' 

~~~shell
hbase> append 't1', 'r1', 'c1', 'value', ATTRIBUTES=>{'mykey'=>'myvalue'}
hbase> append 't1', 'r1', 'c1', 'value', {VISIBILITY=>'PRIVATE|SECRET'}
~~~

## incr:递增单元格值

在指定的表/行/列坐标上递增单元格'值' 

将表'ns1:t1'或't1'中的列'c1'下的行'r1'中的单元格值增加1(可省略,不写为1)或10 

~~~shell
hbase> incr 'ns1:t1', 'r1', 'c1'
hbase> incr 't1', 'r1', 'c1'
hbase> incr 't1', 'r1', 'c1', 1
hbase> incr 't1', 'r1', 'c1', 10
hbase> incr 't1', 'r1', 'c1', 10, {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> incr 't1', 'r1', 'c1', {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> incr 't1', 'r1', 'c1', 10, {VISIBILITY=>'PRIVATE|SECRET'}
~~~
