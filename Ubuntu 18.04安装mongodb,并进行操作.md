## 1.安装mongodb
```bash
sudo apt-get update                   # 先进行更新
sudo apt-get install -y mongodb       # 安装mongodb
```
安装成功后，检查服务是否正常
```bash
sudo systemctl status mongodb
```
若有异常，结果中active会有显示，可以查阅资料解决问题
我们可以通过实际链接到数据库服务器并执行诊断命令来进一步验证
```bash
mogo --eval 'db.runCommand({ connectionStatus: 1 })'
```
会将当前数据库版本，服务器地址和端口及状态命令输出

状态查询，启，停，从起服务期命令和禁止开机自启或设置开机自启的命令如下
```bash
sudo systemctl status mongodb
sudo systemctl start mongodb
sudo systemctl stop mongodb
sudo systemctl restart mongodb
sudo systemctl disable mongodb
sudo systemctl enable mongodb
```
## 2.连接数据库
```bash
mongodb://[username@password@]host1[:pprt1][,host2[:port2], ...[,hostN[:portN]]][/[database][?options]]
```
- **mongodb://** 这是固定的格式，必须要指定
- **username:password@** 可选项， 如果设置，在连接数据库服务器之后，驱动都会尝试登录这个数据库
- **host1** 不许的值定至少一个host, host1是个这URL唯一要填写的。它指定了要连接服务器的地址。如果要链接复制集，请指定多个主机地址。
- **portX** 可选的指定端口，如果不填，默认为27017
- **/database** 如果指定username:password@, 连接并验证登录指定数据库。若不指定默认打开test数据库。
- **?options** 是连接选项。如果不使用/database, 则前面需要加上/。所有连接选项都是键值对name=value,键值对之间通过&或;(分号)隔开

标准的连接格式包含了多个选项(options)，请查阅资料。常用 __connectTimeoutMS__ 可以打开连接的时间， __socketTimeoutMS__ 发送和接受sockets的时间

shell连接数据库实例
```bash
xaohuihui@ubuntu:~$ mongo
MongoDB shell version v3.6.3
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.3
> mogodb://localhost
```

## 3.数据库操作
```bash
use DATABASE_NAME
```
如果数据库不存在，则创建数据库并切换到数据库，存在直接切换到指定数据库

刚创建的数据库用 show dbs 命令查看是查不到的，需要插入一些数据，才能展示出来

```bash
db.dropDatabase()
```
删除当前数据库，不知道当前数据库的话，可以用 db 命令查看

```bash
> db.createCollection("ticket_info")       # 创建集合， 类似数据库中的表， db.createCollection(name, options) options是可选参数，可以指定有关内存大小及索引的配置选项
> show tables                                                     #  show collections 命令会更准确一点
> show collections
ticket_info
> db.ticket_info.drop()
true
> show collections
>
```

语法格式：
```bash
db.createCollection(name, options)
```
参数说明：

name: 要创建的集合名称
options: 可选参数, 指定有关内存大小及索引的选项
options 可以是如下参数：

| 字段	|类型	|描述|
|------|--------|------|
|capped	|布尔	|（可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。**当该值为 true 时，必须指定 size 参数。** |
|autoIndexId	 |布尔	|（可选）如为 true，自动在 _id 字段创建索引。默认为 false。 |
| size	 |数值	|（可选）为固定集合指定一个最大值，以千字节计（KB）。**如果 capped 为 true，也需要指定该字段。** |
| max	|数值	|（可选）指定固定集合中包含文档的最大数量。|

## 4.python mongodb
连接数据库，关闭连接：
```python
import pymongo


def connection_db():
    connection = pymongo.MongoClient("127.0.0.1", 27017)      # 连接本地mongodb库
    tdb = connection["ticket"]                                # 连接ticket库 也可写为 tdb = connection.ticket
    return connection, tdb

def close_connection(connection):
    connection.close()                                        # 关闭连接

```

插入数据
```python
def insert_one_data(tdb, collection, mongodb_data):
    insert_x = tdb[collection].insert_one(mongodb_data)  # 插入一条数据
    return insert_x


def insert_many_data(tdb, collection, mongodb_data):
    insert_x = tdb[collection].insert_many(mongodb_data)  # 插入多条数据
    return insert_x

if "__name__" == "main":
    conn, tdb = connection_db()
    res = insert_many_data(tdb, "ticket_info", data)   
    print(res._InsertManyResult__inserted_ids)
    print( res.acknowledged)

    close_connection(conn)

```
输出结果：
```bash
[ObjectId('5e6f45309057eba36a8b2471'), ObjectId('5e6f45309057eba36a8b2472'), ObjectId('5e6f45309057eba36a8b2473'), ObjectId('5e6f45309057eba36a8b2474'), ObjectId('5e6f45309057eba36a8b2475')]
True
```


查询数据：
```python
from bson.objectid import ObjectId

## 若要查询mongodb自动生成的 _id 为查询条件 需要上面的包

def select_one_data(tdb, collection, filters):
    info = tdb[collection].find_one(filters)  # 查询单条数据
    return info

def select_all_data(tdb, collection, filters):  #  查询多条数据，返回可迭代对象
    info = tdb[collection].find(filters)
    return info

if "__name__" == "main":
    conn, tdb = connection_db()
    filter = {"_id": ObjectId("5e6f45309057eba36a8b2471")}
    res = select_all_data(tdb, "ticket_info", filter)
    for item in res:   
        print(item)

    close_connection()

```

输出结果：

```bash
{'_id': ObjectId('5e6f45309057eba36a8b2471'), 'item': 'journal', 'qty': 25, 'tags': ['blank', 'red'], 'dim_cm': [14, 21]}
```

查询条件：

| 需求条件  | 转化后示例| 备注 |
|-------------|----------|----|
| page=1, size=10| tdb.ticket_info.find(filter).limit(size).skip( size * (page - 1) )   | |
| qty > 40 | filter =  {"qty": {"$gt": 40}}  |  |
| qty = 25 | filter = {"qty": 25}  |或  filter = {"qty": {"$ne": 25}} |
| qty < 40 | filter = {"qty": {"$lt": 40}}|  |
| qty >= 40 | filter = {"qty": {"$gte": 40}} |  |
| qty <= 40 | filter = {"qty": {"$lte": 40}} |  |
| qty in [25, 45] | filter = {"qty": {"$in": [25, 45]}} |  |
| qty berween 10 and 100 | filter = {"qty": {"$gte": 10, "$lte": 100}} |  |
| item like "%p%" |  filter = {"item": {"$regex": "p"}}  |  **若是以p开头，"^p"; 若是以p结尾，"p$"**  |   
| 若遇到{"name": 'z3', "child":[{"name": "zq", "age": 10}, {"name": "zw", "age": 11} ]}这样的数据类型，想要搜索列表中的数据| filter = {"name.name": "zq"} |
| 排序，倒叙为-1，正序为1，以上面数据类型为例| query_result.sort("name", 1).sort( "name.age", -1)| 有多少个排序字段，加多少个sort方法|
| 计算结果条数| query_result.count() | query_result为find方法等等执行完成的对象 |
| 若想仅展示某些字段，或不返回某些字段| find(filter, {"_id": 0, "qty": 1})|find()的第一个参数是dict类型的检索条件，第二个参数为定义哪些字段展和禁止返回的，例子的信息是 "_id"字段不返回，"qty": 字段返回 |

__注意：__
> skip(), limilt(), sort()三个放在一起执行的时候，执行的顺序是先 sort(), 然后是 skip()，最后是显示的 limit()。


更新数据：

```python
def update_one_data(tdb, collection, my_query, new_values):   # 更新一条数据
    tdb[collection].update_one(my_query, new_values)


def update_many_data(tdb, collection, my_query, new_values):    # 更新多条数据
    tdb[collection].update_many(my_query, new_values)

```
删除数据：
```python
def delete_one_data(tdb, collection, filters):
    tdb[collection].delete_one(filters)


def delete_many_data(tdb, collection, filters):
    tdb[collection].delete_many(filters)
```
