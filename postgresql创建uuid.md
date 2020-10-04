### 数据库配置
#### 1.UUID配置项
在sqlalchemy中postgresql的uuid类型对象是： sqlalchemy.dialects.postgresql.UUID, 所以基本的用法是：
```python
from sqlalchemy.dialects.postgresql import UUID
id = Column(UUID)
```
如何设置自动生成随机的uuid。

1.使用python的uuid模块生成uuid:
``` python
id = Column(UUID, default=lambda: str(uuid.uuid4()))
```
2.(推荐)服务端去生成uuid之gen_random_uuid():
``` python
from postgreql import text id = Column(UUID, server_default=text("gen_random_uuid()"))
gen_random_uuid() 并不是默认可用的，需要在数据库中安装pgcrypto模块（下面的操作在 postgresql 数据库控制台中操作）
```

```
SQL
# \c db_name
# CREATE EXTENSION pgcrypto;
CREATE EXTENSION
# select gen_random_uuid();
gen_random_uuid
--------------------------------------
52f3e12b-b42a-47df-80de-6bfd9396b87e
(1 row)
```
3.服务端去生成uuid之uuid_generate_v4():

``` python
id = Column(UUID, server_default=text("uuid_generate_v4()"))
需要在数据库中安装 uuid-ossp 模块：
```

```
SQL
# \c db_name # create extension "uuid-ossp"; CREATE EXTENSION # select uuid_generate_v4(); uuid_generate_v4 -------------------------------------- 53153822-8516-45d7-8804-9792439e449a (1 row)
```
