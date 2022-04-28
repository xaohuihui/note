### 记一次ES偶发性查询慢的问题

#### 前言

生产环境使用ES过程中，出现偶尔查询极慢的情况， 核查了集群状态，数据量与open状态索引数量等，都没有异常的地方，查询语句也是只进行了一层聚合和一些查询条件，按正常逻辑不会出现慢的情况

#### 核查过程

1. 查询集群健康状态，发现并不存在什么问题

   ```bash
   curl -sXGET localhost:9200/_cluster/health?pretty
   ```

   <img src="C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-01集群状态.png" alt="image-20220428155923201" style="zoom: 80%;" />

2. 查看open状态的索引数量

   ```bash
   curl -sXGET localhost:9200/_cat/indices | grep open | wc -l
   ```

   open 状态的索引数量为506，不多

3. 查看下thread_pool

   ```bash 
   curl -sXGET localhost:9200/_cat/thread_pool?v
   ```

   ![image-20220428160608481](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-02thread_pool.png)

   发现es的3号节点`search`存在`rejected`的情况,, (近段时间查询都是这个数字，证明知识在es集群上次启动到现在共出现了346次拒绝，但最近数量没有真多，证明最近查询慢的情况不是其引起的。)

4. 查询ES的JVM启动配置

   ```bash
   ps -wwwwef | grep ES0
   ```

   ![image-20220428161229884](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-03JVM配置.png)

   可以发现 `-Xmn8533m`：设置年轻代大小为`8G多`；`-Xms16384`：设置`JVM`促使内存为`16G`；`-Xmx`设置`JVM`最大可用内存为`16G`，`-Xms`与`-Xmx`相同可以避免每次垃圾回收完成后具名重新分配内存。

   但是我们知道 `整个堆大小=年轻代大小 + 年老代大小 + 持久代大小` 持久代一般固定大小为64m，所以增大年轻代后，将会减小年老代大小，此值对系统性能影响较大，我的线上环境建议给5G就足够了。（但是集群需要协调才能进行重启，所以暂时不能修改此配置）

5. 查看集群nodes信息

   ```bash
   curl -sXGET localhost:9200/_cat/nodes?v
   ```

   ![image-20220428163410070](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-04nodes-v.png)

   > 堆内存使用率>80%, cpu > 80 或者 load > 60，需要进行排查

   图上所示ES3节点的堆内存(heap.percent)使用率达到了90%，且在执行查询操作的时候，使用top查看，偶尔还能发现es3的JVM进程占用cpu能达到4000%，占满了cpu，这时问题快要浮出水面了

6. 查看一下节点的段数量和段内存(不算很高)

   ```bash
   curl -sXGET localhost:9200/_cat/nodes?h=sc,sm
   ```

   ![image-20220428164547993](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-05segemnt段.png)

7. 查看ES3的pid

   ```bash
   ps -ef | grep ES3
   ```

   ![image-20220428164940615](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-06ES3-pid.png)

8. 查看该ES3的JVM内存使用详情(打印出每个class实例数目，内存占用，类全名等信息)

   ```bash
   jmap -histo:live 13041 | head -30
   ```

   ![image-20220428165433099](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-07JVM内存使用详情.png)

9. 查看gc情况

   ```bash
   jstat -gc 13041 1s 10
   ```

   ![image-20220428165639807](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-08gc情况.png)

   发现OC和OU这两列特别接近，执行 ```curl -sXPOST localhost:9200/_clear/cache``` 也不见差距增大多少，说明该节点一直都在GC。可以kill掉该节点。

   综合上面排查到的情况，堆内存使用过高，ES3这个节点在尝试GC，然而每次GC又达不到释放的效果，因此在不断的GC，不断的使用CPU。

#### 临时解决办法

 简单粗暴，直接kill屌这个ES3的节点，将这个节点kill对应的堆内存也将释放；但是可能存在疑问，ES3这个节点kill掉了，ES集群不会出现问题吗？其实是ES集群会变成yellow状态，但是不影响使用，而且这个节点，ES集群还会自动拉起来的。

等了一会。。。

查看es集群的状态，堆内存，cpu和load信息占用等情况

![image-20220428172547349](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-09恢复后集群状态.png)

![image-20220428172727629](C:\Users\songyanhui\Desktop\workspace\note\image\ES响应慢-10恢复后nodes.png)

发现ES3节点已经拉起来了，堆内存占用也变成了17%

查看thread_pool也不存在rejected的情况。集群状态恢复，再去查询ES已经恢复成了原来的查询效率，但是查询不能太频繁的提交，容易挤压在队列中，而且需要把年轻代（-Xmn）的内存配置改小。

此时，已经将ES查询慢的情况临时处理完毕。



---

- From: xaohuihui

* 手搓不易，记得star哦