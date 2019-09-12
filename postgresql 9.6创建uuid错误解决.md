## postgresql 9.6创建uuid错误解决
```bash
postgres=# create extension "uuid-ossp";
ERROR: could not open extension control file "/usr/pgsql-9.6/share/extension/uuid-ossp.control": No such file or directory
```
我的postgresql版本为9.6的所以要安装9.6的扩展包
```bash
 sudo yum install postgresql96-contrib.x86_64
```
然后再去数据库执行
```bash
postgres=# create extension "uuid-ossp";
CREATE EXTENSION
```

OK!
