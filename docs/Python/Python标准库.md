
# Python标准库

# 一、 random 随机数
**标准库，只需 import random 即可**

## 1.1 random.random()
1. **生成一个0-1之间的随机浮点数**，0 <= n <= 1.0

##  1.2 random.uniform(a,b)
1. **生成一个指定范围内的随机浮点数**，必须：a >= b 或a <= b 均可，生成的随机数n: a <= n <=b,或 b <= n <= a
```python
print random.uniform(10, 20)
print random.uniform(20, 10)
# 14.73
# 18.579
```

## 1.3 random.randint(a,b)
1. **生成一个指定范围内的整数**，必须 a <= b，结果a <= n <= b

## 1.4 random.randrange(a, b, step)
1. **从指定范围内，按指定基数递增的集合中，获取一个随机数，step默认为1**
2. **random.randrange(1, 10, 2),即从[1, 3, 5,  7, 9]中随机获取一个**

## 1.5 random.choice(有序类型)
1. **从有序序列中随机获取一个元素，list/tuple/str 均可**
2. random.choice('asdf') > asdf中随机的一个

## 1.6 random.shuffle(list)
1. **用于将list中的元素打乱**
```python
alist = ['1', 'a', 200, 'sd']
random.shuffle(alist)
print p
# ['a', 200, '1', 'sd']
```

## 1.7 random.sample(有序类型，个数)
1. **取样，从有序序列中随机获取指定长度的片段，sample不会修改原序列**
```python
alist = ['a', 12, 'sd', 200]
# 从alist中随机取2个元素，不改变原序列
new_list = random.sample(alist, 2)
print(new_list)
['sd', 'a']
```

# 二、os 操作系统

## 2.1 介绍
1. **os模块负责程序与操作系统的交互，**提供了访问操作系统底层的接口****。如**文件**系统，**进程**，**操作系统本身的信息**，并屏蔽各种不同操作系统之间的细节差异。

## 2.2 使用：封装了常见的文件和目录操作
1. os.remove(path) 删文件
2. os.unlink(path) 删文件
2. os.rmdir(path) 删目录
3. os.environ 环境变量

## 三、sys 解释器环境

# 3.1 介绍
1. **sys负责程序与Python解释器的交互，提供了一系列的函数和变量，用于操控Python运行时的环境。**
2. 如调试类，profiling类，运行时的环境、路径，解释器本身的信息等。

## 3.2 使用
1. sys.modules.keys() 返回所有已经导入的模块列表
2. sys.path 返回模块的搜索路径
3. sys.version 获取Python解释程序的版本信息

## 四、argparse 命令行参数
1. **argparse是Py自带的命令行参数解析包**

## 4.1 使用
- 创建 ArgumentParser() 对象
- 调用 add_argument() 方法添加参数
- 使用 parse_args() 解析添加的参数
- 添加参数
分为添加**位置参数-positional arguments**和**可选参数-optional arguments**
添加位置参数声明的参数名前缀不带-或--，按照顺序进行解析，在命令中必须出现，否则报错
- 添加位置参数：parser.add_argument("a")
- 添加可选参数：parser.add_argument('--size', '-s')

```python
# coding=utf-8
import sys
import argparse

def test_sys():
    args = sys.argv
    print('file name is:%s' % args[0])
    print('first args is:%s' % args[1])
    print('second args is:%s' % args[2])

def test_argparse():
    parser = argparse.ArgumentParser()
    parser.add_argument('positional', help='位置参数')  # 位置参数
    parser.add_argument('--size', '-s', type=int, help='大小', required=True)  # 可选参数
    parser.add_argument('--num', '-n', type=str, help='数量', required=True)  # 可选参数
    args = parser.parse_args()
    positional, size, num = args.positional, args.size, args.num
    print('positional: %s, type: %s' % (positional, type(positional)))
    print('size: %s, type: %s' % (size, type(size)))
    print('num: %s, type: %s' % (num, type(num)))


if __name__ ** '__main__':
    test_sys()
    test_argparse()
# 调用:python arg_parse.py position --size=123 -n 1
# 输出
# file name is:arg_parse.py
# first args is:position
# second args is:--size=123
# positional: position, type: <type 'str'>
# size: 123, type: <type 'int'>
# num: 1, type: <type 'str'>
```
# 五、collections模块（高级数据结构）
1. 这个模块对Python基本的数据结构做了封装，增加了一些很酷的数据结构，比如：

a）Counter： 计数器，用于统计元素的数量

b）OrderDict：有序字典

c）defaultdict：值带有默认类型的字典

d）namedtuple：可命名元组，通过名字来访问元组元素

e）deque :双向队列，队列头尾都可以放，也都可以取（与单向队列对比，单向队列只能一头放，另一头取）
## 1 Counter
1. 计数器，用于统计对象中每个元素出现的个数
2. 他是dict的一个子类，
3. 
```python
# 通过字典形式统计每个元素重复的次数传  
res = collections.Counter('abcdabcaba')  
print(res)
# 结果Counter({'a': 4, 'b': 3, 'c': 2, 'd': 1})
```
## 2 OrderDict 有序字典
```python
# 创建一个有序字典
dic = collections.OrderedDict()
dic['name'] = 'winter'
dic['age'] = 18
dic['gender'] = 'male'
print(dic) 
# 结果OrderedDict([('name', 'winter'), ('age', 18), ('gender', 'male')])
#将一个键值对放入最后
dic.move_to_end('name')
print(dic) 
# 结果OrderedDict([('age', 18), ('gender', 'male'), ('name', 'winter')])
```

## 3 defaultdict
1. 默认字典，为字典的值设置一个默认类型，
2. 

```python
people = [['male', 'winter'], ['female', 'elly'], ['male', 'frank'], ['female', 'emma']]

gender_sort = collections.defaultdict(list)
for info in people:
    gender_sort[info[0]].append(info[1])

print(gender_sort)
# 结果defaultdict(<class 'list'>, {'male': ['winter', 'frank'], 'female': ['elly', 'emma']})
```

## 4 namedtouple 可命名元祖

```python
import collections

login_user = [
    (r'http://www.baidu.com', 'usr1', 'pwd1'),
    (r'http://www.youdao.com', 'usr2', 'pwd2'),
    (r'http://mail.126.com', 'usr3', 'pwd3')
]

page_info = collections.namedtuple('login_info', ['url', 'username', 'password'])
for user in login_user:
    x = page_info(*user)
    print(x)
# 结果
login_info(url='http://www.baidu.com', username='usr1', password='pwd1')
login_info(url='http://www.youdao.com', username='usr2', password='pwd2')
login_info(url='http://mail.126.com', username='usr3', password='pwd3')
```

## 5 deque 双向队列
1. deque其实是 double-ended queue 的缩写，双向队列
2. 说到队列就要说到队列和栈了；队列是FIFO，先进先出；栈是FILO，先进后出
3. 队列又分为：单向队列（只能从一边放，从另外一边取）；双向队列（两头都可以放，也都可以取），
 Python中单向队列就是queue.Queue
4. 很多方法和list的方法相同，比如count，index等

```python
raw = [1,2,3]
d = collections.deque(raw)
print(d)                    #结果deque([1, 2, 3])

#右增
d.append(4)
print(d)                    #结果deque([1, 2, 3, 4])
#左增
d.appendleft(0)
print(d)                    #结果deque([0, 1, 2, 3, 4])
```

# 六、datetime 模块

## 一、datetime 默认值及更新值
1. datetime
```python
import datetime
create_time1 = db.Column(db.DateTime, default=datetime.datetime.now)
create_time2 = db.Column(db.DateTime, default=datetime.datetime.now())
# 两者的区别：第一个插入的是期望的, 数据的插入时间，每条数据插入时可自动根据当前时间生成，一般使用第一条的根据此对象的生成时间的使用；
# 第二条是一个固定的时间, 程序部署的时间，**所有的数据都是这个固定时间
c_time = db.Column(db.DateTime, default=datetime.datetime.now)
update_time = db.Column(db.DateTime, default=datetime.datetime.now,onupdate=datetime.datetime.now)
```

2. **onupdate=datetime.now**,当此对象的数据更改时，会使用此时的时间来替换掉update_time中的默认值，即最后的修改时间

## 二、时间格式与字符串格式互相转化

```python
str_day = 2018-09-01 
from datetime import datetime
# 将其转化为有格式的日期
date_day = datetime.strptime(str_day, '%Y-%m-%d %H:%M:%S')
# pass
# 将时间格式转化为字符串
str_day1 = date_day.strftime('%Y-%m-%d %H:%M:%S')
```

3. **timedelta模块计算时间的**
```python
# 只有时间格式的才能计算
from datetime import timedelta
date_day1 = 2018-09-08  
date_day2 = date_day1 + timedelta(days=1)
print date_day2
# 2018-09-09
```

## 三、时间差中seconds和total_seconds
1. 两个日期类型进行相减可以获取到时间差
经常会使用seconds来获取，其实seconds获取的是时间差的秒数，遗漏了天
seconds是获取时间部分的差值，而total_seconds()是获取两个时间之间的总差

```python
import datetime
t1 = datetime.datetime.strptime("2016-08-24 10:30:00", "%Y-%m-%d %H:%M:%S")
t2 = datetime.datetime.strptime("2016-08-24 12:30:00", "%Y-%m-%d %H:%M:%S")

interval_time = (t2 - t1).seconds  # 输入的结果：7200
total_interval_time = (t2 - t1).total_seconds() # 输出结果也是: 7200

# 换一个方式
t1 = datetime.datetime.strptime("2016-08-24 10:30:00", "%Y-%m-%d %H:%M:%S")
t2 = datetime.datetime.strptime("2016-08-27 12:30:00", "%Y-%m-%d %H:%M:%S")
interval_time = (t2 - t1).seconds  # 输入的结果：7200
total_interval_time = (t2 - t1).total_seconds() # 输出结果也是: 266400
```

**总结：** 
1. **seconds**属性计算出的时间差，以秒为时间差，是**同一天的**，即不能跨天计算
2. **total_seconds()** 方法能够**跨天**计算时间差，都是以秒为计算单位，**常用此total_seconds()**

## 四、utc时间与本地时间互转

```python
from datetime import datetime

def utc2local( utc_dtm ):
    # UTC 时间转本地时间（ +8:00 ）
    local_tm = datetime.fromtimestamp( 0 )
    utc_tm = datetime.utcfromtimestamp( 0 )
    offset = local_tm - utc_tm
    return utc_dtm + offset

def local2utc( local_dtm ):
    # 本地时间转 UTC 时间（ -8:00 ）
    return datetime.utcfromtimestamp( local_dtm.timestamp() )

if __name__ ** "__main__":

    # utc_tm = datetime.utcnow()
    utc_tm = datetime( 2012, 10, 26, 10, 00, 00 )

    print( "src utc time:\t", utc_tm.strftime("%Y-%m-%d %H:%M:%S") ) 

    # UTC 转本地
    local_tm = utc2local(utc_tm)
    print( "tran loc time:\t", local_tm.strftime("%Y-%m-%d %H:%M:%S") ) 

    # 本地转 UTC
    utc_tran = local2utc(local_tm)
    print( "tran utc time:\t", utc_tran.strftime("%Y-%m-%d %H:%M:%S") ) 
```

##  五、dateutil 模块
1. dateutil 模块是对datetime的扩展库
- 主要有**parser：即日期解析器，能够将字符串转为日期格式**
```python
from dateutil import parser
#输入美式日期
In:parser.parse('22nd,July,2009')
Out: datetime.datetime(2009, 7, 22, 0, 0)
#输入以-为分割日期
In:parser.parse('2018-04-20')
Out[131]: datetime.datetime(2018, 4, 20, 0, 0)
# 输入时间，输出当前日期+时间
In:parser.parse('12:0:0')
Out[131]: datetime.datetime(2018, 4, 20,12, 0)
```
- **rrule**可计算出两个datetime对象间相差的年月日等时间数量。

```python
函数原型如下。
rrule(self, freq, dtstart=None, interval=1, wkst=None, count=None, until=None, bysetpos=None,
        bymonth=None, bymonthday=None, byyearday=None, byeaster=None, byweekno=None, byweekday=None, byhour=None, byminute=None, bysecond=None, cache=False)
其中
freq:可以理解为单位。可以是 YEARLY, MONTHLY, WEEKLY, DAILY, HOURLY, MINUTELY, SECONDLY。即年月日周时分秒。
dtstart,until:是开始和结束时间。
wkst:周开始时间。
interval:间隔。
count:指定生成多少个。
byxxx:指定匹配的周期。比如byweekday=(MO,TU)则只有周一周二的匹配。byweekday可以指定MO,TU,WE,TH,FR,SA,SU。即周一到周日。
```

1. 如求出两个日期范围的日期列表

```python
>>from dateutil.rrule import rrule, DAILY
# 2013-08-01到2013-08-07每日
>>list(rrule(DAILY,dtstart=parse('2013-08-01'),until=parse('2013-08-07')))
[datetime.datetime(2013, 8, 1, 0, 0),
 datetime.datetime(2013, 8, 2, 0, 0),
 datetime.datetime(2013, 8, 3, 0, 0),
 datetime.datetime(2013, 8, 4, 0, 0),
 datetime.datetime(2013, 8, 5, 0, 0),
 datetime.datetime(2013, 8, 6, 0, 0),
 datetime.datetime(2013, 8, 7, 0, 0)]
```
