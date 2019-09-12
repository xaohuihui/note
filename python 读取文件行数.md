## python 读取文件行数

在用readlines的时候size只会读取和缓存大小相近的字节整行数
若想要读取完成需要使用iter()方法

```bash
In [7]: import io

In [8]: io.DEFAULT_BUFFER_SIZE
Out[8]: 8192

In [9]: f = open('java_error_in_DATAGRIP_21937.log')

In [10]: list_s = iter(f)
In [12]: list_len = 0

In [13]: for item in list_s:
         ...:     list_len += 1
In [14]: list_len
Out[14]: 1401
```
