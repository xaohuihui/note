### True/False求值

> 由宋延辉创建 最后修改于 2021-01-04 16:31

- [建议] 建议显式转换到bool类型，慎用到bool类型的隐式转换。如使用隐式转换，你需要确保充分了解其语义

- [强制] 禁止使用==或!=判断表达式是否为None，应该用is或is not None

- [强制] 当明确expr为bool类型时，禁止使用==或!=与True/False比较。应该替换为expr或not expr

- [强制] 判断某个整数表达式expr是否为零时，禁止使用not expr，应该使用expr == 0

**解释**

- python中None、空字符串、0、空tuple、list、dict都会被隐式转换为False，这可能和用户预期的行为不一致。
- 为便于不太熟悉python语言的其它语言开发者理解python代码，建议”显式”表明到bool类型的转换语义
- 运算符==或!=的结果取决于__eq__函数，可能出现obj is not None，但obj==None的情况
- if expr ！= False 当expr为None时，会通过检测。一般这不是用户期望的行为
  - from PEP：
  - Yes: if greeting:
  - No: if greeting == True:
  - Worse: if greeting is True:
- 当判断expr是否为0时，若expr为None，not expr也会返回True。一般这不是用户期望的行为

**示例**

```python
# YES:
if users is None or len(users) == 0:
    print 'no users'
 
if foo == 0:
    self.handle_zero()
 
if i % 10 == 0:
    self.handle_multiple_of_ten()
# Cautious:
 
if not users:
    print "no users"

# NO:
 
if len(users) == 0:
    print 'no users'
 
if foo is not None and not foo:
    self.handle_zero()
 
if not i % 10:
    self.handle_multiple_of_ten()

```

