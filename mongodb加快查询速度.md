# Linux提高大数据量而且数据关系不复杂的数据查询服务效率办法

### Linux安装Mongodb

下载地址：<https://www.mongodb.com/download-center#community>

```python
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.0.5.tgz # 下载
tar -zxvf mongodb-linux-x86_64-4.0.5.tgz # 解压
mv mongodb-linux-x86_64-4.0.5/ /usr/local/mongodb # 将解压包拷贝到指定目录
```

MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 **PATH** 路径中：

```
export PATH=<mongodb-install-directory>/bin:$PATH
```

**<mongodb-install-directory>** 为你 MongoDB 的安装路径。如本文的 **/usr/local/mongodb** 。


注意：/data/db 是 MongoDB 默认的启动的数据库路径(--dbpath)。

```python
mkdir -p /data/db
```

### 运行mongodb服务

进入安装的mongodb的目录下中的bin目录下启动mongodb的服务

```python
./mongod # 如若报错，加上sudo试试
./mongod --fork --logpath=/data/db/log.log # 后台启动方式
```

### mongodb后台shell

在与上面相同的目录下运行

```python
./mongo # 先启动mongodb服务才能运行， 如若报错，加上sudo
```

### MongoDB 创建数据库

### 语法

MongoDB 创建数据库的语法格式如下：

```
use DATABASE_NAME
```

如果数据库不存在，则创建数据库，否则切换到指定数据库。

### 实例

以下实例我们创建了数据库 runoob:

```
> use socip
switched to db socip
> db
socip
> show dbs
admin 0.000GB
config 0.000GB
local 0.000GB
socip 0.991GB
>
```

删除数据库方法

进入该数据库执行db.dropDatabase()

删除数据库中的集合

```
db.collection.drop()
```

mongodb创建集合

```
db.createCollection(name, options)
```

参数说明：

- name: 要创建的集合名称
- options: 可选参数, 指定有关内存大小及索引的选项

options 可以是如下参数：

| 字段 | 类型 | 描述 |
| ----------- | ---- | ------------------------------------------------------------ |
| capped | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。** |
| autoIndexId | 布尔 | （可选）如为 true，自动在 _id 字段创建索引。默认为 false。 |
| size | 数值 | （可选）为固定集合指定一个最大值（以字节计）。 **如果 capped 为 true，也需要指定该字段。** |
| max | 数值 | （可选）指定固定集合中包含文档的最大数量。 |

例如：

```
> use test
switched to db test
> db.createCollection("runoob")
{ "ok" : 1 }
>
```

## mongodb 查询

### MongoDB 与 RDBMS Where 语句比较

| 操作 | 格式 | 范例 | RDBMS中的类似语句 |
| ---------- | ------------------------ | ------------------------------------------- | -------------------- |
| 等于 | `{<key>:<value>`} | `db.col.find({"by":"hello"}).pretty()` | `where by = 'hello'` |
| 小于 | `{<key>:{$lt:<value>}}` | `db.col.find({"likes:{$lt:50}}).pretty()` | `where likes < 50` |
| 小于或等于 | `{<key>:{$lte:<value>}}` | `db.col.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50` |
| 大于 | `{<key>:{$gt:<value>}}` | `db.col.find({"likes":{$gt:50}}).pretty()` | `where likes > 50` |
| 大于或等于 | `{<key>:{$gte:<value>}}` | `db.col.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50` |
| 不等于 | `{<key>:{$ne:<value>}}` | `db.col.find({"likes":{$ne:50}}).pretty()` | `where likes != 50` |

### MongoDB AND 条件

MongoDB 的 find() 方法可以传入多个键(key)，每个键(key)以逗号隔开，即常规 SQL 的 AND 条件。

语法格式如下：

```
>db.col.find({key1:value1, key2:value2}).pretty()
```

### MongoDB OR 条件

MongoDB OR 条件语句使用了关键字 **$or**,语法格式如下：

```
>db.col.find(
{
$or: [
{key1: value1}, {key2:value2}
]
}
).pretty()
```

### 创建索引可以加快查询速度

### 语法

createIndex()方法基本语法格式如下所示：

```
>db.collection.createIndex(keys, options)
```

语法中 Key 值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可。

### 实例

```
>db.col.createIndex({"title":1})
>
```

createIndex() 方法中你也可ba以设置使用多个字段创建索引（关系型数据库中称作复合索引）。

```
>db.col.createIndex({"title":1,"description":-1})
>
```

在后台创建索引：

```
db.values.createIndex({open: 1, close: 1}, {background: true})
```

### mongodb备份

#### 语法

mongodump命令脚本语法如下：

```
>mongodump -h dbhost -d dbname -o dbdirectory
>mongodump --forceTableScan -d database_name -o target_directory # 版本不一致情况下
```

- -h：

MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017

- -d：

需要备份的数据库实例，例如：test

- -o：

备份的数据存放位置，例如：c:\data\dump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。

### MongoDB数据恢复

mongodb使用 mongorestore 命令来恢复备份的数据。

#### 语法

mongorestore命令脚本语法如下：

```
>mongorestore -h <hostname><:port> -d dbname <path>
```

- --host <:port>, -h <:port>：

MongoDB所在服务器地址，默认为： localhost:27017

- --db , -d ：

需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2

- --drop：

恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！

- <path>：

mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\data\dump\test。

你不能同时指定 <path> 和 --dir 选项，--dir也可以设置备份目录。

- --dir：

指定备份的目录

你不能同时指定 <path> 和 --dir 选项。

## python程序使用mongodb

如下：

```python
import pymongo
connection = pymongo.MongoClient('127.0.0.1', 27017)
tdb = connection.socip
post = tdb.ip_residence_all

mongodb_data = {"minip": 0, "maxip": 16777215, "continent": "保留IP", "areacode": "B1", "adcode": "", "country": "", "province": "","city": "", "district": "", "bd_lon": "", "bd_lat": "", "wgs_lon": "", "wgs_lat": "", "radius": "", "scene": "","accuracy": "", "owner": "保留地址"}
post.insert(mongodb_data) # 插入数据

info = post.find_one({"minip": {"$gte": 1672534}, "maxip": {"$lte": 1672534}}) # 查询数据
connection.close() #关闭连接
```
