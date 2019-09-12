## python 常用组件使用

http://www.runoob.com/python/att-string-strip.html
python基础知识点学习网站
https://www.postgresql.org/docs/current/static/libpq-connect.html#LIBPQ-PQCONNECTDBPARAMS


- time模块

 - 1）time.localtime([secs])：将一个时间戳转换为当前时区的struct_time。secs参数未提供，则以当前时间为准。

 - 2）time.gmtime([secs])：和localtime()方法类似，gmtime()方法是将一个时间戳转换为UTC时区（0时区）
的struct_time。

 - 3）time.time()：返回当前时间的时间戳。

  - 4）time.mktime(t)：将一个struct_time转化为时间戳。

  -  5）time.sleep(secs)：线程推迟指定的时间运行。单位为秒。

  - 6）time.clock()：这个需要注意，在不同的系统上含义不同。在UNIX系统上，它返回的是“进程时间”，它是用秒表示的浮点数（时间戳）。而在WINDOWS中，第一次调用，返回的是进程运行的实际时间。而第二次之后的调用是自第一次调用以后到现在的运行时间。（实际上是以WIN32上QueryPerformanceCounter()为基础，它比毫秒表示更为精确）

- psycopg2模块

 可以提供数据库连接
```python
import psycopg2
#conn = psycopg2.connect(database="test", user="postgres", password="123456", host="127.0.0.1", port="5432")  #连接到数据库
conn = psycopg2.connect(os.environ.get('PG_URL'))
cur = conn.cursor()
cur.execute(sql)       #
data = cur.fetchone() #执行sql语句并获取结果
cur.execute(sql) #将字符串转化为命令
conn.commit()   #确定保存
conn.rollback()  #执行回滚操作,返回至上一次commit之前
```
- traceback模块

 异常处理
```python
traceback.print_exc()
```

- dotenv模块
```python
from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())
或
from py_dotenv import read_dotenv
dotenv_path = os.path.join(os.path.dirname(__file__), '.env')
read_dotenv(dotenv_path)
```
可以检测工作目录环境变量，可以把一些敏感信息写入环境变量文件中（一般为.env）

```python
import sys
reload(sys) #修改系统默认编码
sys.setdefaultencoding('utf-8')
```
```python
#获得系统默认编码
import sys
sys.getdefaultencoding()
```
```python
from requests.adapters import HTTPAdapter
max_timeout = 120
max_request = 3
create_time = datetime.datetime.now()
req_url = requests.session()
req_url.mount('http://', HTTPAdapter(max_retries=max_request))     #python requests 配置超时及重试次数3次
```

- json模块(http://www.runoob.com/json/json-intro.html)

 JSON: JavaScript Object Notation(JavaScript 对象表示法)

 JSON 是存储和交换文本信息的语法。类似 XML。

 JSON 比 XML 更小、更快，更易解析。


- data.replace('&nbsp;', '').strip()可以对数据进行简单的清洗，把html文本中的"&nbsp"变成‘’，
 - strip()
   - 描述
     - Python strip() 方法用于移除字符串头尾指定的字符（默认为空格）。
   - 语法
     - strip()方法语法：
     - str.strip([chars]);
   - 参数
     - chars -- 移除字符串头尾指定的字符。
   - 返回值
     - 返回移除字符串头尾指定的字符生成的新字符串。


- lxml.html 模块
```python
r = requests.post(url[0], from_data, timeout=120)  #发送一个 HTTP POST 请求
rtext = r.text     #转换文本格式
x = lxml.html.fromstring(rtext.replace('TO_CHAR(BATCHDATE,\'YYYY-MM-DD\')', 'TIME'))  #先把rtext的前面的旧字符串替换成‘TIME’,解析html
data_str = x.xpath("//input[@id='gisDataJson']")[0].get('value')#使用xpth来匹配解析出来的html文件标签里面的内容
data1 = json.load(io.StringIO(data_str)) #StringIO 顾名思义就是在内存中以 io 流的方式读写 str。生成字典格式
```

-  json.loads和json.load的区别

 load和loads都是实现“反序列化”，区别在于（以Python为例）：

 loads针对内存对象，即将Python内置数据序列化为字串

 如使用json.dumps序列化的对象d_json=json.dumps({'a':1, 'b':2})，在这里d_json是一个字串'{"b": 2, "a": 1}'

 d=json.loads(d_json)  #{ b": 2, "a": 1}，使用load重新反序列化为dict

 load针对文件句柄

 如本地有一个json文件a.json则可以d=json.load(open('a.json'))

 相应的，dump就是将内置类型序列化为json对象后写入文件

 把dict字典变成json格式，生成到外部文件里面

 json.dump(dict,open('a.json',"w"))

- 断点调试

 import ipdb   #断点执行

  ipdb.set_trace()

- docker常用命令
```bash
sudo docker ps -a
sudo docker rm 42b65f6f3feb
sudo docker build -t learn/weekdocker .
sudo docker run -it learn/weekdocker
#   
sudo docker run -it learn/weekdocker bash  进入docker内
sudo docker exec -it 00637e482eb2 bash  进入docker内
```

- docker中执行代码报这个错误UnicodeEncodeError 的解决办法
 在docker的env中加入PYTHONIOENCODING=UTF-8

 但最好是写在dockerfile文件中

 ENV PYTHONIOENCODING=UTF-8


- git中子项目修改上传后，父项目需要执行更新

 git submodule update

 从远程下载要用clone
