### ClickHouse简单总结与实例分享

#### 前言

> 在项目开发过程中，总会遇到很多聚合统计相关的需求，在数据量较大的情况下，使用es或者pg等，聚合效率并不能满足我们的需求；所以ClickHouse在聚合统计相关非常高的性能就很满足我们的需求，现在就让我们快速了解ClickHouse吧

#### 简介

​	根据学习总结出ClickHouse的简介如下，

1. 源自俄罗斯Yandex公司开源的OLAP数据库

2. 2016年开源，发展非常迅速，目前在Github上获得23.4k star

3. ClickHouse全称 Click stream, Data WareHouse, 简称**CK**或者**CH**（官方给出的建议应该是CH，但是我们国家的技术大神非常喜欢俄罗斯的AK文化，为了致敬，称之为CK，纯粹道听途说，轻喷哈，简称哪个都不为过，大神们都了解~）

4. 适用于各种数据分析类场景

5. 不使用OLTP事务性操作场景

#### 特性

​	对特性总结为一下几点：

- 列式存储与数据压缩
  - 默认使用LZ4算法压缩，压缩比最高可达8:1
- 向量化执行引擎
  - ClickHouse目前利用SSE4.2指令集实现向量化执行，利用这项寄存器硬件层面的特性，为上层应用程序的性能带来了指数级的提升
- 关系模型与SQL查询
  - 支持数据库、表、视图和函数；SQL查询（大小写敏感）
- 多样化的表引擎
  - 合并树、内存、文件、接口和其他6大类20多种表引擎
- 多线程与分布式
  - 线程级并行，数据存取支持分区（多线程），分片（分布式）
- 多主架构
  - 不区分主控、数据和计算节点。规避单点故障问题
- 数据分片与分布式查询
  - 实现数据分片(reploca)是将数据进行横向切分，每个集群由1或N个分片组成，每个分片(replica)则对应ClickHouse的一个服务节点(node)
  - ClickHouse提供了本地表和分布式表，一张本地表等同于一份数据的分片。而分布式表本身不存储数据，它是本地表的访问代理，类似于分库中间件

#### ClickHouse为何快

- 列式存储数据，数据压缩
- 使用向量化引擎（单条指令操作多条数据）被广泛应用于文本转换、数据过滤、数据解压、JSON转换等场景
- 基于硬件功效最大化，使用CPU中的寄存器进行暴力优化，内存中进行GROUP BY查看过滤，使用HashTable装载数据
- 算法使用（例如在字符串搜索方面）
  - 对于常量：使用Volnitsky算法
  - 对于非常量：使用CPU的向量化执行SIMD，暴力优化
  - 正则匹配：re2和hyperscan算法
- 勇于尝试
  - 针对市面上出现的性能强大的新算法，ClickHouse团队会立即将其纳入其中进行验证，效果好的保留，差就抛弃
- 特定场景优化
  - 去重计算uniqCombined函数：数据量小的时候使用Array保存，数据量中等使用HashSet，数据量大的时候使用HyperLogLog
- 持续测试，持续改进
  - 经常进行试试数据真实场景测试，保证告诉发版频率，自底向上追求极致新能的设计思路

#### MergeTree表引擎

- MergeTree
  - 合并树基础表引擎，其他合并树表引擎都为该表引擎的变种
- ReplacingMergeTree
  - 相同分区的数据去重
- SummingMergeTree
  - 在合并分区的时候可以预先定义条件，将多条数据汇总成一行，可以用来做物化视图，只能处理累加情况
- AggregatingMergeTree
  - 为SummingMergeTree的升级版，能够通过聚合函数来自定义聚合指标
- CollapsingMergeTree
  - 设计逻辑就是像折叠的扇子一样，相同的数据标志字段不一样会被折叠到一起
  - 折叠合并树引擎，通过标志字段进行“以增代删”，绕过Mutation（突变）操作
- VersionCollapsingMergeTree
  - 版本折叠合并树，是CollapsingMergeTree的升级版，主要引入版本的概念，解决多线程下CollapsingMergeTree写入顺序影响折叠结果
- 主动执行合并分区命令
  - ```optimize TABLE table_name FINAL;```

#### Mutation操作

- ClickHouse 通过alter方式实现更新，删除。称之为mutation（突变）
- mutation通过异步的方式执行update语句时，服务端立即返回，但数据还未改变，在排队等待执行，通过system.mutations表查看是否完成，执行步骤如下：
  - 1、通过where条件找到需要修改的分区
  - 2、重建每个分区，用新的分区替换旧的，分区一旦被替换，不可回退

- 注意事项
  - 更新功能不支持更新有关主键或分区键的列
  - 更新操作若涉及多个分区，没有原子性，即在更新过程中select结果可能是一部分变了，一部分没变。
  - 更新是按提交顺序执行的
  - 更新一旦提交不能撤销，重启CickHouse也不行。也会按照system.mutations的顺序继续执行。
  - 已完成更新的条目不会立即删除，保留条目的数量有finished_mutations_to_keep存储引擎参数确定，超过数据量时旧的条目会被删除
  - 更新可能会卡住，如：update intvalue='abc' 这种类型错误的更新语句执行不过去，就会一直卡在这里，需要用kill mutation来消除

#### CollapsingMergeTree在折叠数据是，遵循以下规则

 **sign 为标志字段**

- 如果sign=1比sign=-1的数据多一行，则保留最后一行sign=1的数据
- 如果sign=-1比sign=1的数据多一行，则保留第一行sign=-1的数据
- 如果sign=1和sign=-1的数据一样多，并且最后一行是sign=1，则保留第一行sign=-1和最后一行sign=1的数据
- 如果sign=1和sign=-1的数据行一样多，并且最后一行是sign=-1，则什么也不保留
- 其余情况ClickHouse会打印告警日志，但不会报错，这种情形下，查询结果不可预知

 **在使用CollapsingMergeTree的时候，还有几点是要注意的**

 表结构

```sql
CREATE TABLE ver_collpase_table(     
    id String,     
    code Int32,     
    create_time DateTime,     
    sign Int8,     
    ver UInt8 
)ENGINE = VersionedCollapsingMergeTree(sign,ver) 
PARTITION BY toYYYYMM(create_time) 
ORDER BY id 
```

- 折叠数据并不是实时触发的，和所有其他的MergeTree变种表引擎一样，这项特性也只有在分区合并的时候才会体现。所以在分区合并之 前，用户还是会看到旧的数据。解决这个问题的方式有两种。

  - 在查询数据之前，使用```optimize TABLE table_name FINAL```命令强制 分区合并，但是这种方法效率极低，在实际生产环境中慎用。

  - 需要改变我们的查询方式。以collpase_table举例，如果原始的SQL 如下所示：

    ``` sql
    SELECT id, SUM(code), COUNT(code), AVG(code), uniq(code) 
    FROM collpase_table 
    GROUP BY id;
    -- 改成
    SELECT id, SUM(code * sign), COUNT(code * sign), AVG(code * sign), uniq(code * sign) 
    FROM collpase_table 
    GROUP BY id 
    HAVING SUM(sign) > 0 ;
    ```

- 只有相同分区内的数据才有可能被折叠。不过这项限制对于 CollapsingMergeTree来说通常不是问题，因为修改或者删除数据的时候， 这些数据的分区规则通常都是一致的，并不会改变

- 最后这项限制可能是CollapsingMergeTree最大的命门所在。 CollapsingMergeTree对于写入数据的顺序有着严格要求。__如果按照正常顺序写入，先写入sign=1，再写入sign=-1，则能够正 常折叠；若将写入的顺序置换，先写入sign=-1，再写入sign=1，则不能够折叠__

  - 这种现象是CollapsingMergeTree的处理机制引起的，因为它要求 sign=1和sign=-1的数据相邻。而分区内的数据基于ORBER BY排序，要实现 sign=1和sign=-1的数据相邻，则只能依靠严格按照顺序写入。（多线程写入的时候就会造成写入顺序不可预估）

#### VersionedCollapsingMergeTree

 VersionCollapsingMergeTree表引擎的作用域CollapsingMergeTree完全相同，它们的不同之处在于，VersionCollapsingMergeTree对数据写入顺序没有要求，在同一个分区内，任意顺序的数据都能够完成折叠操作

- 为什么能够任意顺序完成折叠操作
  - 看上面表结构，在定义ver字段后，VersionCollapsingMergeTree会自动将ver作为排序条件并增加到ORDER BY的末端。在每个数据分区内，数据都会按照ORDER BY (id, ver DESC)排序。所以无论写入时数据的顺序如何，在折叠处理是，数据都会被排回到相邻的位置，然后进行折叠操作。

#### 分享一个存在更新需求的实例

> 比如我存在一个场景 对sip dip port等聚合的需求，但是又需要进行更新或删除的请求。那就需要用到折叠树的表引擎的。

入表结构设计如下

```sql
CREATE TABLE situation.t_flow_origin_v2(
        `id` UInt64,
        `sip` String,
        `dip` String,
        `dport` UInt32,
        `sip_int` UInt64,
        `dip_int` UInt64,
        `hash_key` String,
        `is_ip_check` Int8,
        `sign` Int8,
        `ver` Int8
    ) ENGINE = VersionedCollapsingMergeTree(sign, ver)
    PARTITION BY is_ip_check
    ORDER BY id
    SETTINGS index_granularity = 8192;
```

现在库里有一批数据如下图

![image-20210811120134593](file://C:\Users\songyanhui\Desktop\workspace\note\image\ck1-更新数据图1.png?lastModify=1651146775)

插入两条数据一条标志位变为-1，一条标志位为1版本变为2（顺序无所谓）（展示更新操作）

![更新图2](C:\Users\songyanhui\Desktop\workspace\note\image\ck1-更新图2.png)

我们来实验查询语句的改写  怎么能将这两条更新前的数据在查询的时候类似于折叠起来

```sql
select id, sip, dip, dport, sip_int, dip_int, hash_key, is_ip_check, sum(ver * sign) from situation.t_flow_origin_v2 where id = 1 group by (id, sip, dip, dport, sip_int, dip_int, hash_key, is_ip_check) having sum(sign) > 0;
```

![更新图3](C:\Users\songyanhui\Desktop\workspace\note\image\ck1-查询更改图1.png)

聚合查询

数据库中有下图的样例数据

![以插代删数据库数据样例图1](C:\Users\songyanhui\Desktop\workspace\note\image\ck1-版本折叠合并树引擎-以插代删数据库数据样例图1.png)

```sql
select sip_int, count(Distinct dip_int), max(dip_int), min(dip_int), sum(ver * sign) as ver
from situation.t_flow_origin_v2
where id = 1
group by sip_int
having sum(sign) > 0;
```

![    ](C:\Users\songyanhui\Desktop\workspace\note\image\ck1-版本折叠合并树引擎-更改查询图.png)

我们查询更改成这种样式的就可以拿到我们更新或删除后的最新数据，现在我们实验一下折叠树在合并分区后，应该被折叠的数据还存不存在

![](C:\Users\songyanhui\Desktop\workspace\note\image\ck1-VersionCollapsingMergeTree-合并分区后数据展示图1.png)

可以发现应该被折叠的数据在合并分区后则不存在。



----

- Form: xaohuihui
- 手搓不易，记得star哦