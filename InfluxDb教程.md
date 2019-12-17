# InFluxDB教程

* `InFluxDB`是一种时间序列数据库（设计的根源是：时间是有意义的），`InFluxDB`中的所有数据都有一个`time`列，用于存储时间戳`timestamp`
* `InFluxDB`优先考虑`create`和`read`数据的性能而不是`update`和`delete`，`InfluxDB`不是一个完整的`CRUD`数据库，更像是一个`CR-ud`数据库。

***

## <mark>InfluxDB概念介绍</mark>

### <font color=Orange>关键概念</font>

#### <font color=Brown>database</font>

> * InFluxDB的数据库，作为一个逻辑上的容器，它包含了以下内容
>
> 	1. users
> 	2. measurement
> 	3. retention policy
> 	4. continuous queries

#### <font color=Brown>measurement</font>

> * measurement与传统数据库的table类似，作为一个逻辑上的容器，它包含以下内容
>	1. tag（可选）
> 	2. field（必选）
> 	3. time（必选）

#### <font color=Brown>retention policy</font>

> * `retention policy`描述了`InFluxDB`保存数据的时间（DURATION）以及这份数据在集群中的副本数目（单集群下副本数目设置无效）。相关属性如下：
>   1. `duration`属性决定`influxdb`中数据保留多久。在`duration`之前的数据会被自动删除掉
>   2. `replication factor`属性决定数据在集群中保存的副本数
> * 单个`measurement`可以有不同的`retention policy`
> * `InFluxDB`默认使用名为`autogen`的`retention policy`，它具有无限的持续时间和复制因子设置为1

#### <font color=Brown>field</font>

> * `field`由`field key`和`field value`组成，`field key`都是字符串（表头），他们存储元数据，`field value`就是你存储的数据。`field set`是不同的`field key`和`field value`组成的集合。
>
>   ```python
>   # 举例说明field set，以下是field set
>   field1 = 1, field2 = 1
>   field1 = 1, field2 = 2
>   field1 = 2, field2 = 1
>   field1 = 2, field2 = 2
>   ```
>
> * `field`是必选的

#### <font color=Brown>tag</font>

> * `tag`由`tag key`和`tag value`组成，`tag key`作为表头，`tag key`和`tag value`都作为字符串存储，并记录在元数据中。`tag set`是不同的`tag key`和`tag value`组成集合。
>
>   ```python
>   # 举例说明tag set，以下是tag set
>   tag1 = 1, tag2 = 1
>   tag1 = 1, tag2 = 2
>   tag1 = 2, tag2 = 1
>   tag1 = 2, tag2 = 2
>   ```
>
> * `tag`是可选的
>
> * `tag`加了索引，因此在使用`tag`作为过滤器进行查询时，性能会比`field`高很多

#### <font color=Brown>timestamp</font>

> * `timestamp`对应`time`列，InFluxDB所有数据都带这一列，它以RFC3339格式存储着UTC日期和时间
> * `time`是必选的

#### <font color=Brown>point</font>

> * `point`就是一条数据，它包含了：
>   1. measurement
>   2. tag set
>   3. field set
>   4. timestamp
> * 一个`point`由他的`series`和`timestamp`唯一标志

#### <font color=Brown>series</font>

> * `series`作为一个逻辑上的容器，它包含以下内容
>   1. measurement
>   2. retention policy
>   3. tag set
> * `series`可以理解为一组跟`retention policy`关联的数据

### <font color=Orange>术语介绍</font>

#### <font color=Brown>aggregation</font>

> * 一个InfluxQL函数，返回一堆数据的聚合结果，可以查看`InfluxDB`目前支持的聚合函数和即将支持的聚合函数以了解更多

#### <font color=Brown>batch</font>

> * 一批用换行符分割、符合`influxdb line protocol`的`point`,可以只使用一条HTTP请求将这些数据写入`influxdb`数据库。这大大降低了HTTP开销。`influxdb`建议`batchsize`控制在5000~10000个point

#### <font color=Brown>continuous query(CQ)</font>

> * `influxdb`定期自动执行的`influxQL`的查询语句。语句中必须包含`SELECT`关键词和`GROUP BY time()`关键词

#### <font color=Brown>InfluxDB line protocol</font>

> * 一种文本格式，向InfluxDB数据库写入points时需遵循该语法规范

#### <font color=Brown>metastore</font>

> * 包含了系统状态的内部信息。具体包含
>   1. user信息
>   2. databases
>   3. retention policies
>   4. shard metadata
>   5. continuous queries
>   6. subscriptions

#### <font color=Brown>node</font>

> * 一个独立的`influxdb`服务程序

#### <font color=Brown>now()</font>

> * 本地服务器的当前时间戳，精确到纳秒

#### <font color=Brown>schema</font>

> * `influxDb`中数据的组织方式

#### <font color=Brown>selector</font>

> * `influxQL`函数，从特定范围的`points`中返回一个`point`

#### <font color=Brown>series cardinality</font>

> * `series`的基数（集合元素的个数）

#### <font color=Brown>series key</font>

> * `series key`唯一标志一个`series`,它包含
>   1. measurement
>   2. tag set
>   3. field key

#### <font color=Brown>shard</font>

> * 一个`shard`包含经过编码和压缩后的数据，每个`shard`包含一组特定的`series`（同一指定时间段）
>
> * `shard`和`retention policy`相关联（决定单个shard保存的数据时间段长度），每个`retention policy`下会存在许多`shard`，每个`shard`存储一个指定时间段内的数据，例如`07:00~08:00`的数据录入`shard0`
>
>   ```
>   shard group0
>    ├─ shard1
>    │  └─ series1
>    ├─ shard2
>    └─ shard3
>   ```

#### <font color=Brown>shard group</font>

> * `shard group`是`shard`逻辑上的容器，每个`shard` 从属于一个（且只能从属于一个）`shard group`，`shard group`由`retention policy`和时间来组织的，一般一个`retention policy`关联一个`shard group`。这个`shard group`包含对应`retention policy`所跨越时间的所有`shard`

#### <font color=Brown>shard duration</font>

> * `shard duration`决定了每个`shard group`跨越多少时间，由`retention policy`中的`SHARD DURATION`指定

#### <font color=Brown>subscription</font>

> * `subscription`允许`Kapacitor`以订阅的方式从`InfluxDb`接收数据。当`Kapacitor`配置为使用`influxDb`时，`subscription`将自动将每次写入从`InfluxDb`推送到`Kapacitor`

#### <font color=Brown>TSM(Time Structured Merge tree)</font>

> * `influxdb`专用的数据存储格式，比现有的`B+`、`LSM`数据结构有更高的压缩率和吞吐量

#### <font color=Brown>user</font>

> * `influxDb`由两种类型的用户：
>   1. admin用户对所有数据库都有读写权限，并且有管理查询和管理用户的全部权限。
>   2. 非admin用户有针对database的可读，可写或者二者兼有的权限。
> * 当认证开启之后，InfluxDB只执行使用有效的用户名和密码发送的HTTP请求。

#### <font color=Brown>WAL(Write Ahead Log)</font>

> * 为了降低对持久层访问的频率，`influxDB`将新的`point`写入到`wal`单元保存在`cache`中，直到保存到数据量足够大触发刷新到持久层。

### <font color=Orange>与SQL比较</font>

* `InfluxDB`中：
  1. `measurement`类似SQL数据库的`table`
  2. `tag`类似SQL数据库里索引的列
  3. `field`类似SQL数据库里没有索引的列
  4. `point`类似SQL数据库的行
  5. `continuous query`和`retention policy`类似SQL数据库中定期执行的存储过程
* `InfluxDB`是时序数据库，用于存储大量的时间序列数据，并对这些数据进行快速的实时分析。这就像一个SQL数据库表，其中主键是由系统预先设置的，并且始终是时间。但严格来说时序不是SQL数据库的目的。
* `InfluxDB`允许你的`measurement`可以动态的增加`field`
* `InfluxDB`使用一种类似`SQL`的`InfluxQL`语言
* 由于优先考虑`create`和`read`数据的性能而不是`update`和`delete`，`InfluxDB`不是一个完整的`CRUD`数据库，更像是一个`CR-ud`数据库。

### <font color=Orange>InfluxDB的设计简介</font>

*  `InfluxDB`是一个时间序列数据库。针对这种用例进行优化需要进行一些权衡，主要是以牺牲功能为代价来提高性能。以下列出了一些权衡过的设计见解：
   1. `删除/更新`是罕见操作，`删除/更新`功能受到很大限制。从而增加查询和写入性能
   2. 按时间递增的顺序写入数据会很高效，随机时间或时间不按升序写入数据时，性能会低很多。
   3. 简化冲突提高写入性能：写入相同数据不会冲突，大部分情况使用第一次的数据，极少数情况会直接覆盖。
   4. 可以处理大量的读取和写入
   5. 能够写入和查询数据比具有强一致性更重要：`InfluxDB`在高负载情况下，仍能完成多个客户端的写入/查询请求。但高负载情况下，查询返回的结果可能不包含最近的`point`
   6. `InfluxDB`表是`Schema-less`的：这意味着`InfluxDB`可以随时添加`field`,使不连续数据的管理更加方便，但这也意味着`InfluxDB`不支持表间的连接操作

### <font color=Orange>Schema设计</font>

* 哪些情况使用`tag`？哪些情况使用`field`？
  > 1. 经常用来筛选查询的字段，建议使用`tag`
  > 2. 经常对它使用`GROUP BY()`的字段，建议使用`tag`
  > 3. 经常对它使用`InfluxQL function`的字段，建议使用`field`
  > 4. 如果你存储的值不是字符串，就需要放到`field`中，`tag value`只能是字符串
  
* 避免使用的`schema`

  > 1. 不要有太多的`series`，因为`series`基数高是许多数据库高内存占用的主要原因。通过将基数大的数据存储为`field`而非`tag`是降低`series`基数的办法
  >
  > 2. 用`tag`区分数据比使用更多的`measurement`更好
  >
  > 3. 不要把多条信息放到一个`tag`里，将具有多条信息的单个`tag`拆分为多个单独的`tag`将简化查询并减少对正则表达式的需求
  >
  > 4. `shard group`的保留时间的管理，InfluxDB将数据存储在shard group中。shard group由存储策略（RP）管理，并存储具有特定时间间隔内的时间戳的数据。 该时间间隔的长度称为shard group的duration。默认情况下，shard group的duration是由RP的duration决定，也可以自己配置shard group DURATION：
  >
  >    ```
  >    RP DURATION    shard group DURATION
  >    小于2天         1个小时
  >    2天到6个月      1天
  >    大于6个月       7天 
  >    ```
  >
  >    当InfluxDB强制执行RP时，它会丢弃整个shard group，而不是单个数据点。通常，较短的shard group duration允许系统有效地丢弃数据，较长的shard group duration可以改善压缩，提高写入速度，并减少每个shard group的固定迭代器开销。
  >
  >    `InfluxDB`建议shard group的配置如下:
  >
  >    > * 是你一般查询时间范围的两倍
  >    > * 每个shard group里至少有10万个数据点
  >    > * 每个shard group里面的每个series至少有1000个数据点

### <font color=Orange>存储引擎</font>

* 属于高阶知识点，这里不做介绍

***

## <mark>InfluxQL介绍</mark>

* 类似SQL的查询语言

### <font color=Orange>数据查询</font>

#### <font color=Brown>SELECT语句</font>

> * SELECT语句介绍
>   
>   1. 包括`SELECT子句`和`FROM子句`
>   
>   2. 不能只查询`tag`字段
>   
>   ```sql
>   --SELECT语句包含一个 SELECT子句 和一个 FROM子句
>   SELECT <field_key>[,<field_key>,<tag_key>] FROM <measurement_name>[,<measurement_name>]
>   
>   ------------------------------------SELECT子句介绍介绍---------------------------------
>   SELECT * 	  					--返回所有field和tag
>   SELECT "field1"					--返回field1
>   SELECT "field1","field2" 		--返回多个指定的field
>   SELECT "field1","tag1"   		--返回多个指定的field和tag
>   --注意：SELECT "tag1","tag2"     --只查询tag是不允许的
>   SELECT "item"::field,"item"::tag --返回两个item(一个是field，一个是tag)，::用来指定item是field                                     还是tag
>   SELECT *::field				    --查询所有field
>   SELECT ("field1"*2)+4            --查询字段经过基本运算后的值
>   
>   -------------------------------------FROM子句介绍-------------------------------------
>   FROM "measurement1"						--从measurement1查询
>   FROM "measurement1","measurement2"	  	  --从多个指定的measurement查询
>   FROM "database1"."autogen"."measurement1" --从指定的measurement1（限定数据库、保留策略）
>   FROM "database1".."measurement1"		 --从指定的measurement1（限定数据库、默认保留策略）
>   ```

#### <font color=Brown>WHERE子句</font>

> * `WHERE子句`基于`fields`、`tags`和`timestamps`筛选数据
>
> * 在WHERE子句中用单引号表示字符串，使用双引号字符串和无引号字符串查询将不会返回任何数据
>
> * 不能使用`OR`来指定筛选多个时间段
>
>   ```sql
>   --在WHERE子句中用单引号表示字符串，使用双引号字符串和无引号字符串查询将不会返回任何数据
>   
>   ----------------------------------------field筛选-----------------------------------------
>   --支持string、bool、float、int类型比较
>   --支持运算符：=、<>、!=、>、>=、<、<=
>   SELECT * FROM "measurement1" WHERE "field" + 2 > 11.9
>   
>   ----------------------------------------tag筛选------------------------------------------
>   --支持的运算符：=、<>、!=
>   SELECT * FROM "measurement1" WHERE "tag" = 'key1'
>   
>   ----------------------------------------timestamp筛选---------------------------------------含GROUP BY time()子句的查询语句，默认查询时间范围是16770921到当前时刻
>   --不含GROUP BY time()子句的查询语句，默认查询时间范围是16770921到22620411
>   SELECT * FROM "measurement1" WHERE time > now() - 7d
>   ```

#### <font color=Brown>GROUP BY子句</font>

> * `GROUP BY`子句按指定的`tags`或时间间隔将查询结果分组
>
> * 下面是按`tag`分组：
>
>   ```sql
>   GROUP BY * 				--对结果中的所有tag作分组
>   GROUP BY "tag1"			--对指定tag1作分组
>   GROUP BY "tag1","tag2"	 --对指定的tag1和tag2作分组
>   
>   --例子：MEAN是求均值，对所有tag作分组，并查询每个分组的Pnl均值
>   > SELECT MEAN("Pnl") FROM "measurement1" GROUP BY *
>   ```
>
> * 下面是按时间间隔分组：
>
> * `GROUP BY time()`使用系统预设的时间间隔起始点（一般是00:00:00,不取决于WHERE子句限定的时间范围）,并且每个时间间隔区间都是左闭右开的。可以使用`GROUP BY time(time_interval,offset_interval)`中的`offset_interval`来调整时间间隔的起始点，如5m表示往👉偏移5m,-5m表示往👈偏移5m。
>
>   ```sql
>   --基本语法如下，SELECT子句中需全部使用聚合函数；WHERE子句指定时间范围（默认时间范围是16770921到当前时刻）；time()指定分组间隔；tag是可选项；fill()是可选项，它会更改不含数据的时间间隔的返回值
>   SELECT <function>(<field_key>) FROM_clause WHERE <time_range> GROUP BY time(<time_interval>),[tag_key] [fill(<fill_option>)]
>   
>   --例子:时间间隔为12分钟，并且还对tag1作分组，使用0来填充不含数据的返回值
>   SELECT COUNT("Pnl") FROM "measurement1" WHERE time >= '2015-08-18T00:00:00Z' AND time <= '2015-08-18T00:30:00Z' GROUP BY time(12m,-5m),"tag1",fill(0)
>   ```
>
> * `fill()`必须位于`GROUP BY`子句末尾，它的参数可以是：
>
>   1. 任一数值：时间间隔里没有数据，则返回该指定值
>   2. linear：时间间隔里没有数据，则返回该时间间隔的线性插值结果
>   3. none：时间间隔里没有数据，则不返回结果
>   4. previous：时间间隔里没有数据，则返回前一个间隔的数据
>   5. null：时间间隔里没有数据，则返回null

#### <font color=Brown>INTO子句</font>

> * INTO子句将查询的结果写入用户自定义的measurement中。
>
>   ```sql
>   --INTO子句语法如下
>   SELECT_clause INTO <measurement_name> FROM_clause [WHERE_clause] [GROUP_BY_clause]
>   
>   --实例
>   INTO "database1"."autogen"."measurement1" --写入measurement1(限定数据库、限定保留策略、限定表)
>   INTO "database1".."measurement1"		--写入measurement1(限定数据库，默认保留策略、限定表)
>   INTO "measurement1"						--写入measurement1(限定表)
>   INTO "database1"."autogen".:MEASUREMENT FROM /<正则表达式>/ --:MEASUREMENT是对FROM子句匹配														    的每个measurement的反向引用
>   ```
>
> * 在InfluxDB中直接重命名数据库是不可能的，因此`INTO`子句的常见用途是将数据从一个数据库移动到另一个数据库。（注：InfluxDB的正则表达式前后使用斜杠`/`，并且使用[Golang的正则表达式语法](http://golang.org/pkg/regexp/syntax/)）。<font color=Red>注意要加`GROUP BY *`,否则源数据库中的`tag`将被copy为`field`</font>
>
> * 建议在迁移大量数据时，分开`measurement`并使用`WHERE子句`按时间切片，逐个INTO语句运行。这样可以避免内存占用过大
>
>   ```sql
>   --例子：小数据量迁移，直接一次搞定；大数据量迁移时分开多个INTO语句执行
>   > SELECT * INTO "copy_database"."autogen".:MEASUREMENT FROM "database"."autogen"./.*/ GROUP BY *
>   ```
>
>   

### <font color=Orange>schema查询</font>
### <font color=Orange>数据库管理</font>
### <font color=Orange>函数</font>
### <font color=Orange>continuous queries</font>
### <font color=Orange>数学计算</font>
### <font color=Orange>认证和授权</font>

***

## <mark>InfluxDB安装使用</mark>

### <font color=Orange>InfluxDB服务端安装</font>

* 下载地址：https://portal.influxdata.com/downloads/，可能会无法下载，可以参考https://blog.csdn.net/v6543210/article/details/86934941

* 服务端安装

  > 1. 最好在`root`或`administrator`下安装
  >
  > 2. 默认情况下，TCP端口`8086`用作`InfluxDB`客户端和服务端之间的`InfluxDB API`通信，TCP端口`8088`给`RPC`服务用来备份和恢复数据。
  >
  >    除此之外，`InfluxDB`提供许多其他插件供使用，你可能需要为它们定制端口。所有端口设置都可以通过修改`influxdb.conf`文件进行配置。
  >
  > 3. 自行百度不同操作系统如何安装`InfluxDB`
  > 4. 配置`InfluxDB`，可通过`influxdb.conf`文件来进行配置，一般使用默认配置即可，有需要再查看详细配置项
  > 5. 运行`InfluxDB`服务

### <font color=Orange>InfluxDB客户端</font>

#### <font color=Brown>Command line interface（CLI）工具</font>

> * `InfluxDB`的命令行交互式工具`influx.exe`，它支持`InfluxDB API`，可以使用它来写入数据（手动写入或从文件导入）。交互式的查询数据，并格式化输出查询结果。
>
> * 下面展示了如何进入`InfluxDB`的CLI环境（应该确保服务端和客户端的版本一致，避免出现解析问题，连接成功后会自动打印出服务端和客户端的版本）：
>
>   ```python
>   # 启动 influx.exe 默认连接 127.0.0.1:8086 服务端
>   # 退出 influxdb 交互环境可输入 quit 回车即可
>   # 若想查看influx.exe支持具体哪些参数，可以使用 influx.exe --help
>   C:\influxdb\influx.exe --help
>      
>   # 这里介绍几个常用的参数
>   -host  	# 指定连接的服务端地址，默认是本机
>   -port	# 指定连接的服务端端口，默认是8086
>   -execute 'command'	# 执行command后退出CLI交互环境，windows系统使用双引号将command括起来
>   -format # 指定服务器应答结果的输出格式
>   -import # 从文件导入数据到数据库
>   ```
>   
> * 下面介绍如何使用CLI环境进行交互式操作（已经进入CLI）：
>
>   ```python
>   # 任何时候，可以输入help获取可用的command列表
>   # 使用ctrl+c结束一个耗时很长的查询
>   ```
> * 下面介绍CLI环境支持的命令：（略）
>   

***