# InFluxDB教程

* 此教程参考`InFluxDB`官方文档：https://docs.influxdata.com/influxdb/v1.7
* `InFluxDB`源码地址：https://github.com/influxdata/influxdb
* `InFluxDB`是一种时间序列数据库（设计的根源是：时间是有意义的），`InFluxDB`中的所有数据都有一个`time`列，用于存储时间戳`timestamp`
* `InFluxDB`优先考虑`create`和`read`数据的性能而不是`update`和`delete`，`InfluxDB`不是一个完整的`CRUD`数据库，更像是一个`CR-ud`数据库。
* `InfluxDB`提供3种操作方式：
  1. `CLI`命令行方式
  2. `HTTP API`接口
  3. 各语言`API`库

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

### <font color=Orange>数据写入</font>

#### <font color=brown>写入协议：InfluxDb行协议</font>

> * `InfluxDB`的行协议是一种文本格式，向InfluxDB数据库写入`points`时需遵循该语法规范。
>
> * 一行表示一个`point`，下面代码说明了语法规则
>
>   1. measurement：你想要写入数据的measurement，必填
>   2. tag set：你想要`point`中包含的`tag`，多组`tag set`用`,`分割，为了获得最佳性能，你应该在将它们发送到数据库之前按`tag key`排序（排序规则参考Golang的bytes.compare函数），可选的
>   3. `tag set`和`field set`之间用空格分割
>   4. field set：你想要`point`中包含的`field`，多组`field set`用`,`分割，必填
>   5. `field set`和`timestamp`之间用空格分割
>   6. `timestamp`：以纳秒为精度的时间戳。可选的，默认使用服务器本地时间戳
>
>   ```sql
>   measurement1,tag1=a,tag2=b field1=1,field2=2 1465839830100400200
>   ```
>   
> * 行协议中的数据类型介绍：
>   
>   1. measurement，tag keys，tag values，field keys始终是字符串
>   
>   2. Field value可以是整数、浮点数、字符串和布尔值
>   
>      * 数字类型默认是`float`类型，`field value`末尾加`i`告诉`InfluxDB`以`integer`类型存储
>   
>        ```sql
>        --默认float类型
>        field=82
>        --integer类型
>        field=82i
>        ```
>   
>      * 加双引号表示字符串
>   
>      * 布尔类型，真使用`t`、`T`、`true`、`True`、`TRUE`，假使用`f`、`F`、`False`、`FALSE`。

#### <font color=brown>写入数据到InfluxDB</font>

##### <font color=green>CLI方式</font>

> * 使用`INSERT`语句
>
>   ```sql
>   INSERT 符合行协议的一行
>   ```
>
> * 使用`-import`从文件批量导入数据
>
>   1. -import：表示从文件导数据到`influxdb`
>   2. -path：指定数据文件路径
>   3. -precision：指定时间戳显示格式
>
>   ```sql
>   $influx -import -path=C:\data.txt -precision=s 
>   ```
>
>   4. 数据文件格式说明：
>      * 数据导入文件包含两个部分：
>        * DDL（数据定义语言）：包含用于创建相关数据库和管理保留策略的InfluxQL命令。如果数据库和保留策略已存在，可以跳过此部分。
>        * DML（数据操作语言）：列出相关数据库和（如果需要）保留策略，并包含行协议中的数据。
>   
>   ```shell
>   # DDL
>   CREATE DATABASE import
>   CREATE RETENTION POLICY oneday ON import DURATION 1d REPLICATION 1
>   
>   # DML
>   # CONTEXT-DATABASE: import
>   # CONTEXT-RETENTION-POLICY: oneday
>   
>   price,type=BTC close=968.29 1483228740000000000
>   price,type=BTC close=968.7 1483228800000000000
>   ```
>   
>   5. 注意事项:
>      * 对于大文件，`influx`每10万个`point`给出一个带入状态信息
>      * 允许使用`-pps`参数限制每秒写入的`point`数量
>      * 允许使用`.gz`文件导入数据，添加`-compress`参数来实时解压导入
>      * 建议每个文件的`point`数目为`5000~10000`

### <font color=Orange>数据查询</font>

#### <font color=Brown>SELECT语句</font>

> * SELECT语句介绍：
>   
>   1. 包括`SELECT子句`和`FROM子句`
>   2. 不能只查询`tag`字段
>   3. 下面是`SELECT语句`语法规则详细介绍：
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

> * `WHERE子句介绍`：
>
>   1. `WHERE子句`基于`fields`、`tags`和`timestamps`筛选数据
>
>   2. 在`WHERE子句`中用`'`表示字符串，使用`"`或无引号表示字符串，查询将不会返回任何数据
>
>   3. 不能使用`OR`来指定筛选多个时间段
>   4. 下面是`WHERE子句`语法规则详细介绍：
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

> * `GROUP BY`子句介绍：
>
>   1. `GROUP BY`子句按指定的`tags`或时间段将查询结果分组
>
>   2. 下面是`GROUP BY`子句按`tag`分组的语法介绍：
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
>   3. 下面是`GROUP BY`子句按时间段分组的语法介绍：
>      * `GROUP BY time()`使用系统预设的起始时间来划分时间段（一般是00:00:00,不取决于`WHERE子句`限定的时间范围）,并且每个时间段都是左闭右开区间。GROUP BY time(time_interval,offset_interval)`中的`offset_interval`可以调整起始时间，如5m表示往👉偏移5m，-5m表示往👈偏移5m。
>        
>      * `fill()`必须位于`GROUP BY`子句末尾，如果`GROUP BY time()`所有组都没有数据，那么`fill()子句`将不会填充任何数据，`fill()子句`的参数可以是：
>        
>        1. 任一数值：时间间隔里没有数据，则返回该指定值
>        2. linear：时间间隔里没有数据，则返回该时间间隔的线性插值结果
>        3. none：时间间隔里没有数据，则不返回结果
>        4. previous：时间间隔里没有数据，则返回前一个间隔的数据
>      5. null：时间间隔里没有数据，则返回null
>   
>   ```sql
>   --基本语法如下，SELECT子句中需全部使用聚合函数；WHERE子句指定时间范围（默认时间范围是16770921到当前时刻）；time()指定分组间隔；tag是可选项；fill()是可选项，它会更改不含数据的时间间隔的返回值
>   SELECT <function>(<field_key>) FROM_clause WHERE <time_range> GROUP BY time(<time_interval>),[tag_key] [fill(<fill_option>)]
>   
>   --例子:时间间隔为12分钟，并且还对tag1作分组，使用0来填充不含数据的返回值
>   SELECT COUNT("Pnl") FROM "measurement1" WHERE time >= '2015-08-18T00:00:00Z' AND time <= '2015-08-18T00:30:00Z' GROUP BY time(12m,-5m),"tag1",fill(0)
>   ```

#### <font color=Brown>INTO子句</font>

> * `INTO子句`介绍：
>
>   1. `INTO子句`将查询的结果写入用户自定义的measurement中。
>   2. `SELECT`子句中包含的的`tag`存入目标表时，将变为`field`，若想复制`tag`字段，请使用`GROUP BY *`或`GROUP BY tags`
> 3. 下面是`INTO子句`语法介绍：
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
> 3. 下面介绍一些`INTO子句`的常见使用案例：
>   
>    * 数据库重命名
>    
>        1. 在InfluxDB中直接重命名数据库是不可能的，通常使用`INTO`子句将数据从一个数据库移动到另一个数据库。
>        2. <font color=Red>注意要加`GROUP BY *`,否则源数据库中的`tag`将被copy为`field`</font>
>      3. 建议在迁移大量数据时，分开`measurement`并使用`WHERE子句`按时间切片，逐个INTO语句运行。这样可以避免内存占用过大
>    
>        ```sql
>      --例子：小数据量迁移，直接一次搞定
>      --注：InfluxDB的正则表达式前后使用斜杠`/`，并且使用`Golang`的正则表达式语法
>      SELECT * INTO "copy_database"."autogen".:MEASUREMENT FROM "database"."autogen"./.*/ GROUP BY *
>        
>      --大数据量迁移时分开多个INTO语句执行（将数据按measurement和时间段切片）
>      SELECT * INTO "copy_database"."autogen"."measurement1" FROM "database"."autogen"."measurement1" WHERE time > now() - 100w and time< now() -90w GROUP BY *
>        ```
>      
>      * 将查询结果写入到一个`measurement`
>      
>        1. `GROUP BY *`会自动将源表的所有`tag`字段copy到目标表。
>      
>        ```sql
>        --将查询结果写入一个measurement
>        SELECT * INTO "copy_measurement" FROM "measurement" WHERE "tag1" = 'miketest' GROUP BY *
>        --将查询结果写入一个完全指定的measurement
>        SELECT * INTO "copy_database"."autogen"."copy_measurement" FROM "measurement" WHERE "tag1" = 'miketest' GROUP BY *
>        --将聚合结果写入一个measurement（采样）
>        SELECT MEAN("Pnl") INTO "copy_database"."autogen"."copy_measurement" FROM "measurement" WHERE "tag1" = 'miketest' GROUP BY *
>        --将多个measurement的聚合结果写入到一个不同的数据库中（带反向引用的采样）
>        SELECT MEAN(*) INTO "copy_database"."autogen".:MEASUREMENT FROM /.*/ WHERE time >= '2015-08-18T00:00:00Z' AND time <= '2015-08-18T00:06:00Z' GROUP BY time(12m)
>        
>        ```

#### <font color=Brown>ORDER BY TIME DESC</font>

> * `ORDER BY TIME DESC`用于按时间降序返回查询结果

#### <font color=Brown>LIMIT子句和SLIMIT子句</font>

> * `LIMIT子句`介绍：
>
>   1. `LIMIT子句`用于限制返回的`point`个数
>
>   ```sql
>   SELECT * FROM "measurement" LIMIT 3
>   ```
>
> * `SLIMIT子句`介绍：
>
>   1. `SLIMIT子句`用于限制返回的`series`个数，比如限制`GROUP BY tags`返回的组数
>
>   ```sql
>   SELECT * FROM "measurement" GROUP BY * SLIMIT 1
>   ```
>
> * `LIMIT子句`和`SLIMIT子句`混用
>
>   1. 可以限制返回`series`的个数，以及每个`series`包含的`point`个数
>
>   ```sql
>   SELECT * FROM "measurement" GROUP BY * LIMIT 3 SLIMIT 1
>   ```

#### <font color=Brown>OFFSET子句和SOFFSET子句</font>

> * `OFFSET子句`介绍：
>
>   1. `OFFSET子句`使返回结果跳过查询结果的前n个`point`
>   2. `OFFSET子句`需要`LIMIT子句`，否则会造成不一致的查询结果
>
>   ```sql
>   --语法介绍
>   SELECT_clause [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] LIMIT_clause OFFSET <N> [SLIMIT_clause]
>   
>   --返回结果将跳过前两个point(返回第3、4个point)
>   SELECT * FROM "measurement" LIMIT 2 OFFSET 2
>   ```
>
> * `SOFFSET子句`介绍：
>
>   1. `OFFSET子句`使返回结果跳过查询结果的前n个`series`，比如跳过`GROUP BY tags`返回的前n个组
>   2. `SOFFSET子句`需要`SLIMIT子句`，否则会造成不一致的查询结果
>
>   ```sql
>   --语法介绍
>   SELECT_clause [INTO_clause] FROM_clause [WHERE_clause] GROUP BY *[,time(time_interval)] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] SLIMIT_clause SOFFSET <N>
>   
>   --返回结果将跳过前两个组(返回第3、4个series)
>   SELECT * FROM "measurement" GROUP BY * SLIMIT 2 SOFFSET 2
>   ```
>
> * `OFFSET子句`和`SOFFSET子句`混用：
>
>   1. 跳过前n个`series`，每个`series`跳过前m个`point`
>
>   ```sql
>   SELECT MEAN("Pnl") FROM "measurement1" WHERE time >= '2015-08-18T00:00:00Z' AND time <= '2015-08-18T00:42:00Z' GROUP BY *,time(12m) ORDER BY time DESC LIMIT 2 OFFSET 2 SLIMIT 1 SOFFSET 1
>   ```

#### <font color=Brown>Time Zone子句</font>

> * `Time Zone子句`介绍：
>
>   1. `tz(<time_zone>)`子句返回指定时区的时间（基于`UTC`偏移后的时间），`InfluxDB`默认使用`UTC`存储和返回时间
>   2. `time_zone`参数需使用`'`，时区命名可参考https://timezonedb.com/time-zones
>
>   ```sql
>   --返回时间使用上海时区
>   SELECT * FROM "measurement1" tz('Asia/Shanghai')
>   ```

#### <font color=Brown>时间语法介绍</font>

> * 时间语法介绍
>      * `UTC下`的`1677-09-21 00:12:43.145224194 ~ 2262-04-11T23:47:16.854775806Z `，是大部分`SELECT语句`的默认时间范围
>      
>      * `UTC下`的`1677-09-21 00:12:43.145224194 ~ now()`，含`GROUP BY time()`子句的`SELECT语句`的默认时间范围
>      
>      * 默认情况下`CLI`以`epoch`纳秒时间格式返回时间戳，输入`precision<format>`命令指代格式（例如precision rfc3339）。默认情况下，`HTTP API`返回`RFC3339格式`的时间戳。使用`epoch`查询参数指定替代格式。
>      
>      * 绝对时间介绍：
>        
>        1. 以下时间支持`=、<>、!=、>、>=、<、<=`运算符
>        
>        2. 绝对时间使用`rfc3339时间字符串`格式：`'YYYY-MM-DDTHH:MM:SS.nnnnnnnnnZ'`，其中9位小数`.nnnnnnnnn`是可选的，默认是`.000000000`
>        3. 绝对时间使用`类rfc3339时间字符串`格式：`'YYYY-MM-DD HH:MM:SS.nnnnnnnnn'`，其中`HH:MM:SS.nnnnnnnnn`是可选的，默认是`00:00:00.000000000`
>        4. 绝对时间使用`epoch_time`：指`UTC`下`1970-01-01 00:00:00`以来所经过的时间，默认单位是纳秒。
>        
>        ```sql
>        --使用rfc3339格式时间字符串
>        SELECT * FROM "measurement1" WHERE time >= '2015-08-18T00:00:00.000000000Z'
>        --使用类rfc3339格式时间字符串
>        SELECT * FROM "measurement1" WHERE time >= '2015-08-18 00:00:00.000000000'
>        --使用epoch格式
>        SELECT * FROM "measurement1" WHERE time >= 1439856000000000000
>        SELECT * FROM "measurement1" WHERE time >= 1439856720s
>        ```
>        
>      * 相对时间介绍：
>      
>        1. 使用`now()`或`now() +`或`now() -`来表示相对时间
>        2. `now() + 1h`，注意`now()`和`1h`之间的空格不能缺省
>        3. 单位介绍：
>           * u或µ：微妙
>           * ms：毫秒
>           * s：秒
>           * m：分钟
>           * h：小时
>           * d：天
>           * w：周
>      
>        ```sql
>        SELECT * FROM "measurement1" WHERE time >= now() - 1h
>        ```
>      

#### <font color=Brown>正则表达式</font>

> * `InfluxDb`正则表达式前后使用`/`，并使用`Golang`的正则表达式语法，`=~`表示匹配，`!~`表示匹配
> * 目前`InfluxDb`不支持
>   1. 匹配数据库名
>   2. 匹配`retention policy`
>   3. 以`/正则表达式/::field`或`/正则表达式/::tag`来匹配`field/tag`

#### <font color=Brown>数据类型和转换</font>

> * `SELECT子句`支持指定`field`的类型(`float、integer、string、boolean`)，支持基本的转换操作（目前只支持`integer`和`float`之间的相互转换）。它们都使用`::`
>
>   ```sql
>   --返回Pnl为float类型的数据
>   SELECT "Pnl"::float FROM "measurement"
>   --将Pnl（float）转换为int类型返回
>   SELECT "Pnl"::integer FROM "measurement"
>   ```

#### <font color=Brown>多语句</font>

> * 用`;`分割多个语句

#### <font color=Brown>子查询</font>

> * 子查询介绍：
>
>   1. 子查询是嵌套在一个查询的`FROM子句`中的另一个查询语句。
>   2. 执行顺序是先执行子查询，再执行主查询
>   3. 子查询支持多层嵌套
>   4. 下面是两个例子：
>
>   ```sql
>   SELECT SUM("Pnl"),SUM("Capital") FROM (SELECT "Pnl","Capital"-7000000 FROM "NoHedging_Pnl")
>   
>   SELECT SUM("dif") FROM (SELECT "Capital" - "Pnl" AS "dif" FROM "NoHedging_Pnl")
>   ```

### <font color=Orange>schema查询</font>

#### <font color=Brown>SHOW DATABASES</font>

> * 返回当前实例上的所有数据库
>
>   ```sql
>   SHOW DATABASES
>   ```

#### <font color=Brown>SHOW RETENTION POLICIES</font>

> * 返回指定数据库的`retention policy`列表
>
> ```sql
> SHOW RETENTION POLICIES [ON <database_name>]
> 
> --用ON指定数据库
> SHOW RETENTION POLICIES ON "database"
> 
> --CLI用USE指定数据库
> > USE "database"
> > SHOW RETENTION POLICIES
> ```

#### <font color=Brown>SHOW SERIES</font>

> * 返回指定数据库的`series`列表
>
>   ```sql
>   SHOW SERIES [ON <database_name>] [FROM_clause] [WHERE <tag_key> <operator> [ '<tag_value>' | <regular_expression>]] [LIMIT_clause] [OFFSET_clause]
>   ```

#### <font color=Brown>SHOW MEASUREMENTS</font>

> * 返回指定数据库的`measurement`列表
>
>   ```sql
>   SHOW MEASUREMENTS [ON <database_name>] [WITH MEASUREMENT <regular_expression>] [WHERE <tag_key> <operator> ['<tag_value>' | <regular_expression>]] [LIMIT_clause] [OFFSET_clause]
>   ```

#### <font color=Brown>SHOW TAG KEYS</font>

> * 返回指定数据库的`tag key`列表
>
>   ```sql
>   SHOW TAG KEYS [ON <database_name>] [FROM_clause] [WHERE <tag_key> <operator> ['<tag_value>' | <regular_expression>]] [LIMIT_clause] [OFFSET_clause]
>   ```

#### <font color=Brown>SHOW TAG VALUES</font>

> * 返回指定`tag key`的`tag value`列表
>
>   ```sql
>   SHOW TAG VALUES [ON <database_name>][FROM_clause] WITH KEY [ [<operator> "<tag_key>" | <regular_expression>] | [IN ("<tag_key1>","<tag_key2")]] [WHERE <tag_key> <operator> ['<tag_value>' | <regular_expression>]] [LIMIT_clause] [OFFSET_clause]
>   ```

#### <font color=Brown>SHOW FIELD KEYS</font>

> * 返回`field key`以及其`field value`的数据类型。
>
>   ```sql
>   SHOW FIELD KEYS [ON <database_name>] [FROM <measurement_name>]
>   ```

### <font color=Orange>数据库管理</font>

#### <font color=Brown>创建/删除数据库</font>

> * 创建数据库语法如下，可以在创建时匹配特定的`retention policy`
>
> * 如果你尝试创建已经存在的数据库，`InfluxDB`将什么都不做
>
>   ```sql
>   CREATE DATABASE <database_name> [WITH [DURATION <duration>] [REPLICATION <n>] [SHARD DURATION <duration>] [NAME <retention-policy-name>]]
>   ```
>
> * `DROP DATABASE`从指定数据库删除所有的数据，以及measurement，series，continuous queries, 和retention policies。语法为
>
>   ```sql
>   DROP DATABASE <database_name>
>   ```

#### <font color=Brown>measurement删除</font>

> * `DROP MEASUREMENT`删除指定measurement的所有数据和series，并且从索引中删除measurement。但不会删除相关的`continues queries`
>
> * `DROP MEASUREMENT`中不支持使用正则表达式
>
>   ```sql
>   DROP MEASUREMENT <measurement_name>
>   ```

#### <font color=Brown>retention policy管理</font>

> * 创建`retention policy`
>
>   1. `DURATION`子句确定保留数据的时间，最小是`1h`，最大是`INF`
>   2. `REPLICATION`子句确定数据在集群中的副本个数，该子句不能用于单节点实例。
>   3. `SHARD DURATION`子句确定`shard group`的时间切片，是可选项，默认由`DURATION`决定，
>   4. `DEFAULT`子句是可选的，将该保留策略设置为数据库的默认保留策略
>
>   ```
>   CREATE RETENTION POLICY <retention_policy_name> ON <database_name> DURATION <duration> REPLICATION <n> [SHARD DURATION <duration>] [DEFAULT]
>   ```
>
> * 修改`retention policy`
>
>   1.必须至少指定一个属性
>
>   ```sql
>   ALTER RETENTION POLICY <retention_policy_name> ON <database_name> DURATION <duration> REPLICATION <n> SHARD DURATION <duration> DEFAULT
>   ```
>
> * 删除`retention policy`
>
>   1. 在指定数据库删除指定保留策略的所有measurement和数据：
>
>   ```sql
>   DROP RETENTION POLICY <retention_policy_name> ON <database_name>
>   ```

#### <font color=Brown>series删除</font>

> * `DROP SERIES`
>
>   1. 删除一个数据库里的一个series的所有数据，并且从索引中删除series。
>
>   2. `DROP SERIES`不支持指定时间段
>
>   ```sql
>   DROP SERIES FROM <measurement_name[,measurement_name]> WHERE <tag_key>='<tag_value>'
>   
>   --从单个measurement删除所有series
>   DROP SERIES FROM "measurement"
>   --从单个measurement删除指定series
>   DROP SERIES FROM "measurement" WHERE "tag1" = 'miketest'
>   --从数据库删除有指定tag的series（所有measurement）
>   DROP SERIES WHERE "tag1" = 'miketest'
>   ```
>
> * `DELETE`
>
>   1. 删除数据库中的measurement中的所有点。与`DROP SERIES`不同，它不会从索引中删除series
>   2. 它支持`WHERE`子句指定时间段，默认时间段是`time < now()`
>
>   ```sql
>   DELETE FROM <measurement_name> WHERE [<tag_key>='<tag_value>'] | [<time interval>]
>   
>   --删除measurement中所有的相关数据
>   DELETE FROM "measurement"
>   --删除measurement中指定tag值的所有数据
>   DELETE FROM "measurement" WHERE "tag" = 3
>   --删除measurement中指定时间段的数据
>   DELETE FROM "measurement" WHERE time < `2019-01-01`
>   ```

#### <font color=Brown>删除shard</font>

> * `DORP SHARD`删除一个shard，也会从metastore中删除shard。格式如下：
>
>   ```sql
>   DROP SHARD <shard_id_number>
>   ```

### <font color=Orange>continuous queries</font>

* 连续查询是`InfluxDB`对实时数据自动周期运行的查询，然后把查询结果写入到指定的`measurement`中

* 用处

  * 数据采样，结合`retention policy`可以定时将高精度数据转换为较低精度，并只保留部分高精度数据
  * 预先处理耗时长的查询

* 基本语法

  ```sql
  CREATE CONTINUOUS QUERY <cq_name> ON <database_name>
  BEGIN
    <cq_query>
  END
  ```
  
* 高级语法（慎用）

  ```sql
  CREATE CONTINUOUS QUERY <cq_name> ON <database_name>
  RESAMPLE EVERY <interval> FOR <interval>
  BEGIN
    <cq_query>
  END
  ```
#### <font color=Brown>cq_query</font>

> * `cq_query`介绍：
>
>   1. 需要函数来聚合数据，需要`INTO子句`来写入聚合结果，需要`GROUP BY time()子句`来控制聚合时间切片
>   2. `WHERE子句`不需要指定时间范围，即使指定了，也会被系统忽略。
>   3. `cq_query`的执行时间点取决于`GROUP BY time()子句`，聚合的数据量也取决于`GROUP BY time()子句`。这样既让数据及时聚合，也让聚合的时间片里不会有数据缺失。
>   
>   ```sql
>   SELECT <function[s]> INTO <destination_measurement> FROM <measurement> [WHERE <stuff>] GROUP BY time(<interval>)[,<tag_key[s]>]
> 	```
>
> 	4.使用高级语法`RESAMPLE子句`时，`cq_query`执行的频率取决于`EVERY`指定的时间间隔。使用`FOR`指定时间范围。例如
> 	
> 	* 若`EVERY`间隔大于`GROUP BY time()`间隔，则cq执行的时间点和聚合的数据段取决于`EVERY`，以避免数据丢失。
> 	* 若`FOR`间隔比`GROUP BY time()`间隔或`EVERY`间隔小，`InfluxDB`会报错，以避免数据丢失。
> 	
> 	```sql
> 	--下面的连续查询会在
> 	--08:00执行 07:00~08:00 的聚合
> 	--08:30执行 08:00~09:00 的聚合
> 	--09:00执行 08:00~09:00 的聚合,并覆盖8:30执行的聚合结果
> 	CREATE CONTINUOUS QUERY "cq" ON "database"
> 	RESAMPLE EVERY 30m FOR 1h
> 	BEGIN
> 	  SELECT mean("field") INTO "new_measurement" FROM "measurement" GROUP BY time(1h)
> 	END
> 	```

#### <font color=Brown>cq的管理</font>

> * 列出cq
>
>   ```sql
>   --将按数据库分组展示cq
>   SHOW CONTINUOUS QUERIES
>   ```
>
> * 删除cq
>
>   ```sql
>   DROP CONTINUOUS QUERY <cq_name> ON <database_name>
>   ```
>   
> * 修改cq：`cq`一旦创建就不能修改了，只能`DROP`再`CREATE`
>

### <font color=Orange>函数</font>

* `InfluxDB`函数可以分为聚合类函数、选择类函数和转换类函数

#### <font color=brown>聚合类函数</font>

##### <font color=green>COUNT()</font>

> * 返回非空字段值的数目
>
>   ```sql
>   --基本语法
>   SELECT COUNT( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   --嵌套语法
>   SELECT COUNT(DISTINCT( [ * | <field_key> | /<regular_expression>/ ] )) [...]
>   
>   
>   --指定field key的field value的数目
>   SELECT COUNT("field_key") FROM "measurement"
>   --每个field key关联的field value的数量
>   SELECT COUNT(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的数目
>   SELECT COUNT(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>DISTINCT()</font>

> * 返回`field value`的不同值列表
>
> * 结合`INTO子句`，将`DISTINCT()`结果写入数据库时，由于时间戳都是`1970-01-01 00:00:00`，所以后面的distinct数据会覆盖前面的distinct数据
>
>   ```sql
>   --基本语法
>   SELECT DISTINCT( [ * | <field_key> | /<regular_expression>/ ] ) FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   --嵌套语法
>   SELECT COUNT(DISTINCT( [ * | <field_key> | /<regular_expression>/ ] )) [...]
>   
>   
>   --返回指定field key的field value的不同值列表
>   SELECT DISTINCT("field_key") FROM "measurement"
>   --经测试不支持返回以下用法
>   SELECT DISTINCT(*) FROM "measurement"
>   SELECT DISTINCT(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>INTEGRAL()</font>

> * 返回字段曲线下的面积，也就是积分，不常用，这里不做介绍

##### <font color=green>MEAN()</font>
> * 返回字段的平均值，
>
> * 支持`int64`和`float64`两种数据类型
>
>   ```sql
>   --基本语法
>   SELECT MEAN( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   --指定field key的field value的均值
>   SELECT MEAN("field_key") FROM "measurement"
>   --每个field key的field value的均值
>   SELECT MEAN(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的均值
>   SELECT MEAN(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>MEDIAN()</font>
> * 从一个排好序的`field values`中返回中位数，如果`field values`个数为偶数，则返回中间两个`field value`的平均值
>
> * 支持`int64`和`float64`两种数据类型 
>
>   ```sql
>   --基本语法
>   SELECT MEDIAN( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   --指定field key的field value的中位数
>   SELECT MEDIAN("field_key") FROM "measurement"
>   --每个field key的field value的中位数
>   SELECT MEDIAN(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的中位数
>   SELECT MEDIAN(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>MODE()</font>
> * 返回`field values`中出现频率最高的值，如果频率最高的值有多个，则返回具有最早时间戳的`field value`
>
>   ```sql
>   --基本语法
>   SELECT MODE( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   
>   --指定field key的field value的中位数
>   SELECT MODE("field_key") FROM "measurement"
>   --每个field key的field value的中位数
>   SELECT MODE(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的中位数
>   SELECT MODE(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>SPREAD()</font>
> * 返回`field values`中最大值和最小值的差值，支持所有数值类型的`field`
>
> * 支持`int64`和`float64`两种数据类型
>
>   ```sql
>   --基本语法
>   SELECT SPREAD( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   
>   --指定field key的field value的 最大值和最小值的差值
>   SELECT SPREAD("field_key") FROM "measurement"
>   --每个field key的field value的 最大值和最小值的差值
>   SELECT SPREAD(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的 最大值和最小值的差值
>   SELECT SPREAD(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>STDDEV()</font>
> * 返回`field values`的标准差
>
> * 支持`int64`和`float64`两种数据类型
>
>   ```sql
>   --基本语法
>   SELECT STDDEV( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   
>   --指定field key的field value的 标准差
>   SELECT STDDEV("field_key") FROM "measurement"
>   --每个field key的field value的 标准差
>   SELECT STDDEV(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的 标准差
>   SELECT STDDEV(/正则表达式/) FROM "measurement"
>   ```

##### <font color=green>SUM()</font>

> * 返回`field values`的和
>
> * 支持`int64`和`float64`两种数据类型
>
>   ```sql
>   --基本语法
>   SELECT SUM( [ * | <field_key> | /<regular_expression>/ ] ) [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>   
>   
>   --指定field key的field value的 和
>   SELECT SUM("field_key") FROM "measurement"
>   --每个field key的field value的 和
>   SELECT SUM(*) FROM "measurement"
>   --匹配一个正则表达式的每个field key关联的field value的 和
>   SELECT SUM(/正则表达式/) FROM "measurement"
>   ```

#### <font color=brown>选择类函数</font>

##### <font color=green>BOTTOM()</font>

> * 返回最小的N个`field value`
>
> * 支持`int64`和`float64`两种数据类型
>
> * 详细介绍后续补全
>
>   ```sql
>  --基本语法
>   SELECT BOTTOM(<field_key>[,<tag_key(s)>],<N> )[,<tag_key(s)>|<field_key(s)>] [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
>  
>   
>   --指定field key的field value的 最小的三个值
>   SELECT BOTTOM("field_key1",3) FROM "measurement"
>   --tag_key1的3个不同值对应三个field values组,分别列出这三个组的最小值
>   SELECT BOTTOM("field_key1","tag_key1",3) FROM "measurement"
>   --指定field key的field value的 最小的三个值以及他们相关联的其他field value和tag value
>   SELECT BOTTOM("field_key1",3),"field_key2","tag_key1" FROM "measurement"
>   ```
> 
> 

##### <font color=green>FIRST()</font>

> * 返回时间戳最早的值
> * 详细介绍后续补全

##### <font color=green>LAST()</font>

> * 返回时间戳最近的值
> * 详细介绍后续补全

##### <font color=green>MAX()</font>

> * 返回`field values`最大的值
> * 详细介绍后续补全

##### <font color=green>MIN()</font>

> * 返回`field values`最小的值
> * 详细介绍后续补全

##### <font color=green>PERCENTILE()</font>

> * 返回`field values`较大的前`N%`的几个值
> * 详细介绍后续补全

##### <font color=green>SAMPLE()</font>

> * 返回`field values`中`N`个随机抽样的值
> * 详细介绍后续补全

##### <font color=green>TOP()</font>

> * 返回最大的`N`个`field value`
> * 详细介绍后续补全

#### <font color=brown>转换类函数</font>

##### <font color=green>ABS()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ACOS()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ASIN()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ATAN()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ATAN2()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>CEIL()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>COS()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>CUMULATIVE_SUM()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>DERIVATIVE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>DIFFERENCE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ELAPSED()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>EXP()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>FLOOR()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>HISTOGRAM()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>LN()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>LOG()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>LOG2()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>LOG10()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>MOVING_AVERAGE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>NON_NEGATIVE_DERIVATIVE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>NON_NEGATIVE_DIFFERENCE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>POW()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>ROUND()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>SIN()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>SQRT()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>TAN()</font>

> * 
> * 详细介绍后续补全

#### <font color=brown>预测类函数</font>

##### <font color=green>HOLT_WINTERS()</font>

> * 返回`N`个预测`field value`
> * 详细介绍后续补全

#### <font color=brown>技术分析类函数</font>

* 主要应用于金融投资领域的技术指标

##### <font color=green>CHANDE_MOMENTUM_OSCILLATOR()</font>

> * 返回`钱德动量摆动指标`
> * 详细介绍后续补全

##### <font color=green>EXPONENTIAL_MOVING_AVERAGE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>DOUBLE_EXPONENTIAL_MOVING_AVERAGE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>KAUFMANS_EFFICIENCY_RATIO()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>KAUFMANS_ADAPTIVE_MOVING_AVERAGE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>TRIPLE_EXPONENTIAL_MOVING_AVERAGE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>TRIPLE_EXPONENTIAL_DERIVATIVE()</font>

> * 
> * 详细介绍后续补全

##### <font color=green>RELATIVE_STRENGTH_INDEX()</font>

> * 
> * 详细介绍后续补全

### <font color=Orange>数学运算符</font>

#### <font color=brown>+、-、*、\\</font>

> * 加、减、乘、除运算
> * 详细介绍后续补全

#### <font color=brown>求模</font>

> * 使用`%`运算符进行求模
> * 详细介绍后续补全

#### <font color=brown>按位与、按位或、按位异或</font>

> * 按位与(`&`)、按位或(`|`)、按位异或(`^`)
> * 详细介绍后续补全

### <font color=Orange>认证和授权</font>

#### <font color=brown>认证</font>

> * `InfluxDb`
> * 详细介绍后续补全

#### <font color=brown>授权</font>

> * 详细介绍后续补全

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
  >
  > 4. 配置`InfluxDB`，可通过`influxdb.conf`文件来进行配置，一般使用默认配置即可，有需要再查看详细配置项，可参考：https://www.cnblogs.com/guyeshanrenshiwoshifu/p/9188368.html。这里讲几个简单的配置项
  >
  >    * meta数据存储路径配置，和data数据存储路径配置
  >
  >      ```python
  >      [meta]
  >      dir = "/var/influxdb/meta" 	# meta数据存放目录
  >      
  >      [data]
  >      dir = "/var/influxdb/data" 	# 最终数据（TSM文件）存储路径
  >      ```
  >
  >      
  >
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