## python之 re使用

###  re模块

 - 匹配可能存在小数的文字
```python
In [1]: import re

In [2]: text = '123mg/l'

In [3]: test = '1.232mg/l'

In [4]: re.findall(r'[0-9]*\.?[0-9]+',text)

Out[4]: ['123']

In [5]: re.findall(r'[0-9]*\.?[0-9]+',test)
Out[5]: ['1.232']
```

- 匹配可能存在小数的文字
```python
In [1]: import re

In [2]: text = '123mg/l'

In [3]: test = '1.232mg/l'

In [4]: re.findall(r'[0-9]*\.?[0-9]+',text)
Out[4]: ['123']

In [5]: re.findall(r'[0-9]*\.?[0-9]+',test)
Out[5]: ['1.232']
```


 -  字符串头尾去除空格.strip()
```python
In [1]: s = '\nsadasd    '

In [2]: s.strip()
Out[2]: 'sadasd'
```


**字符串替换：re模块
字符串变量.replace（需要替换的字符串，替换的字符串）
复杂替换用re模块中sub**

- Django项目 生成表 链接：http://www.cnblogs.com/yangmv/p/5327477.html
```bash
python manage.py makemigrations
python manage.py migrate
```

- 将已经排序的结果再进行排序
```mysql
select a.* from (select * from coastal_city_water_quality_weekly order by id DESC limit 40) as a order by periods DESC limit 2;
```

- 将字符串中‘，’分割的字符串变为数组 split(",")
```python
In [1]: a = "hello,everyone,are,you,ok"
In [2]: print a.split(',')
['hello', 'everyone', 'are', 'you', 'ok']
```
