# 一、sys与os模块区别
1. **os模块负责程序与操作系统的交互**，提供了访问操作系统底层的接口，eg:操作文件或文件夹
2. **sys**模块负责程序与**python解释器的交互**，提供了一系列的函数和变量，用于**操控python的运行时环境**。eg:查看一个对象的引用计数，sys.getrefcount(a)
3. sys.path  》import的导包路径，[]
4. sys.path.append('/app'), 》将/app添加到导包路径
5. 重新导入 reload(模块)

# 二、==、is、拷贝

## 2.1 ==、is
1. `is` 是比较两个引用是否指向了同一个对象（引用比较）>> id相等。
2. `==` 是比较两个对象的值是否相等（值比较）

## 2.2 拷贝
1. 字面上理解：**浅拷贝指仅仅拷贝数据集合的第一层数据，深拷贝指拷贝数据集合的所有层**
2. **对于不可变类型而言（str/int/tuple），赋值、浅拷贝、深拷贝效果都一样，因为其永远指向同一块内存地址**
3. **对于可变类型（dict/list/set）,赋值与原数据均指向同一个内存地址，浅拷贝/深拷贝均产生新的对象**
4. **对嵌套数据，浅拷贝只拷贝最外层数据，而深拷贝则拷贝数据集合的所有层，因此里层数据改变时，深拷贝依然指向最原始的数据，而浅拷贝数据相应改变**

### 2.2.1 浅拷贝
3. 浅拷贝，copy.copy(),只复制最外层的引用,里边的小a变， 浅拷贝也变

### 2.2.2 深拷贝
4. 深拷贝，copy.deepcopy(),对一个对象的所有层次的拷贝（递归），里边的小a变，深拷贝不变

```python
import copy
a = [1, 2, 3, 4, ['a', 'b']]  #原始对象

b = a  #赋值，传对象的引用
c = copy.copy(a)  #对象拷贝，浅拷贝：只拷贝最外层
d = copy.deepcopy(a)  #对象拷贝，深拷贝：拷贝所有层，永远指向最原始数据

a.append(5)  #修改对象a
a[4].append('c')  #修改对象a中的['a', 'b']数组对象

print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d

输出结果：
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```

```python
from copy import copy
from copy import deepcopy

yalezhang = [1, {1: 'a'}]
yalezhang01 = copy(yalezhang)
yalezhang02 = deepcopy(yalezhang)
yalezhang[0] = 2
yalezhang[1][1] = 'b'

print(yalezhang, yalezhang01, yalezhang02)
# yalezhang
# [2, {1: 'b'}]  原始数据改变
# yalezhang01
# [1, {1: 'b'}]  浅拷贝只拷贝最外层数据，原数据改变，里层数据相应改变
# yalezhang02
# [1, {1: 'a'}]  深拷贝，永远指向最原始的数据
```

# 三、迭代器iterator
1. 能够通过for..in..这类语句迭代读取一条数据的对象称为**可迭代对象(Iterable)**
2. python要求迭代器本身也是可迭代的
2. **实现了__iter__方法返回自身和__next__方法读取下一条数据的对象就是迭代器(Iterator)**
3. 斐波那契数列

```python
class FibIterator(object):
    """斐波那契数列迭代器"""
    def __init__(self, n):
        """
        :param n: int, 指明生成数列的前n个数
        """
        self.n = n
        # current用来保存当前生成到数列中的第几个数了
        self.current = 0
        # num1⽤来保存前前⼀个数，初始值为数列中的第⼀个数0
        self.num1 = 0
        # num2⽤来保存前⼀个数，初始值为数列中的第⼆个数1
        self.num2 = 1
    def __next__(self):
        """被next()函数调⽤来获取下⼀个数"""
        if self.current < self.n:
            num = self.num1
            self.num1, self.num2 = self.num2, self.num1+self.num2
            self.current += 1
            return num
        else:
            raise StopIteration
    def __iter__(self):
        """迭代器的__iter__返回⾃身即可"""
        return self
if __name__  == '__main__':
    fib = FibIterator(5)
    for num in fib:
        print(num, end=" ")
```
# 四、生成器generator
1. 由于迭代器需要我们自己记录当前的状态，进而生成下一条数据，而生成器是特殊的迭代器
2. **生成器节省内存，使用一个取一个，不用一下子全加载出来**。
2. 创建生成器的方法1，将列表生成式[]改成()
3. 方法2**函数使用yield关键字，会返回一个生成器对象**
4. 使用了yield关键字的函数不再是函数，而是生成器。（使用了yield的函
数就是生成器）
5. yield关键字有两点作⽤：
    - **保存当前运行状态（断点），然后暂停执行，即将生成器（函数）挂起**
    - **将yield关键字后的表达式的值作为返回值返回，此时可以理解为起到了return的作用**
6. **可以使用generator.next()或者generator.send(param)来唤醒生成器。或者next(generator)**
7. 在每段代码中，第一次next调用，相当于启动生成器，会从生成器函数的第一行代码开始执行，直到第一次执行完yield语句,输出yield关键字后的值。
7. **xx = yield yy 意思是：send(param)的作用就是给xx赋值为发送的值（param）,然后让生成器执行到下个yield**，即**将send发送的参数，赋值给yield左边的变量**
8. **yield from 可迭代对象 ->
将对象一个一个的返回**

```python
# 执⾏到yield时，gen函数作⽤暂时保存，返回i的值; 
# temp接收下次c.send("python")，send发送过来的值
# c.next()等价c.send(None)
In [10]: def gen():
....: i = 0
....: while i<5:
....:   temp = yield i
....:   print(temp)
....:   i+=1
# 使⽤send
In [43]: f = gen()
In [44]: next(f)
Out[44]: 0
In [45]: f.send('haha')
haha
Out[45]: 1
```
8. **send('hh')函数，唤醒yield生成器，输出send()里的值，继续执行。c.next()等价c.send(None)**
9. 生成器实现斐波那契数列

```python
def fib(n):
    current = 0
    num1, num2 = 0, 1
    while current < n:
        num = num1
        num1, num2 = num2, num1+num2
        current += 1
        yield num
    return 'done'
f = feibo(5)
print(next(f))
```

# 五、闭包
1. 在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，那么将这
个函数以及用到的一些变量称之为闭包
1. 闭包提高代码可复用性，闭包优化了变量，原来需要类对象完成的作作，闭包也可以完成
2. 由于闭包引用了外部函数的局部变量，则外部函数的局部变量没有及时释放，消耗内存
3. 修改外部函数变量的方法：
4. -py3: **内函数中nonlocal 声名变量即可**
5. **LEGB规则：**
  - **局部变量>闭包变量>全局变量>内建模块的命名空间**

# 六、装饰器decorator
## 6.1 介绍
1. **装饰器就是一个闭包函数，由于代码遵循'开放封闭'原则，在不改变原有代码基础上，额外增加功能。一般情况下，装饰器都带上return**
2. 不带参数的装饰器：

```python
import time
from functools import wraps
def timefun(func):

    @wraps(func)
    def wrappedfunc(*args, **kwargs):
        print("%s called at %s"%(func.__name__, time.ctime()))
        result = func(*args, **kwargs)
        return result
    return wrappedfunc
@timefun
def foo(a, b, c):
    print(a+b+c)
foo(3,5,7)
# foo called at Sun Sep 23 23:11:46 2018
# 15
# 15
```

3. 带参数的装饰器

```python
import time
from functools import wraps

def timefun_arg(pre="hello"):

    def timefun(func):

        @wraps(func)
        def wrappedfunc():
            print("%s called at %s %s"%(func.__name__, time.ctime(), pre))
            return func()
        return wrappedfunc
    return timefun
@timefun_arg("itcast")
def foo():
    print("I am foo")
foo()
# foo called at Sun Sep 23 23:15:23 2018 itcast
# I am foo
```

4. 类装饰器，重写__call__方法
5. functools模块，一些工具包函数
6. **wraps函数**--由于使用装饰器之后，函数名等一些属性已改变，在内函数上方使**用@functools.wraps(func)装饰**，可以消除此副作用

## 6.2 进阶
1. 用于**函数调用前的装饰器**，主要在函数执行前，做一些操作，比如**将一些参数注入到函数里**，到函数执行的时候，就可以用了。

```python
@wraps(fun)
def wra_begin(self, *args, **kwargs):
    # 函数调用前的装饰器，其他操作
    self.action_id = ‘参数’  # 将此参数注入到fun()函数中，被装饰的函数fun（）就可以直接使用了 
    return fun(self, *args, **kwargs)
```
2. **函数执行后的装饰器**，**在fun（)函数中，设置一些变量**，**之后内函数，就可也以直接使用**了

```python
@wraps(fun)
def wra_end(self, *args, **kwargs):
    result = fun(self, *args, **kwargs)
    # 函数调用后的装饰器
    id = self.resource_id # self.resource_id 是fun()函数注入进去的，后面可以直接使用
    pass
```

# 七、python是动态语言
1. 可以动态添加属性、方法，可以使用**\_\_slots\_\_ 变量，来限制当前实例**只能添加的属性，只能添加name和age2个属性

```python
class Person(object):
    __slots__ = ("name", "age")
```
2. 私有化，__属性名

##### @property装饰器，把一个方法变成属性调用

```python
class Money(object):

    def __init__(self):
        self.__money = 0
    # 正常属性设置,获取
    # def getMoney(self):
    #     return self.__money
    #
    # def setMOney(self, value):
    #     if isinstance(value, int):
    #         self.__money = value
    #     else:
    #         print("error:不是整型数字")
    
    # 将money方法当成属性调用
    @property
    def money(self):
        return self.__money

    @money.setter
    def money(self, value):
        if isinstance(value, int):
            self.__money = value
        else:
            print("error:不是整型数字")


m = Money()
# m.setMOney(55)
# print(m.getMoney())
m.money = 99
print(m.money)
```

4. **元类就是用来创建类的东西**。用**type**创建元类，type1 = type('类名'，（继承的类名，），{属性或方法})

# 八、垃圾回收机制
1. **引用计数机制为主，标记清除、分代收集机制为辅**
2. 引用计数无法解决**循环引用**的问题，所以引入了标记清除和分代回收机制。
2. 标记清除(Mark and Sweep):
3. 分代回收：分三代，第0、1、2代的分代回收，每一代达到阈值后就会回收。
2. **分代技术**是一种典型的以空间换时间的技术，这也正是java里的关键技术。这种思想简单点说就是：**对象存在时间越长，越可能不是垃圾，应该越少去收集。**
2. **内存泄漏**：申请了某些内存，没有释放，造成了内存的浪费

# 九、内建函数、属性
1. **lambda 匿名函数，格式：lambda 参数:返回值**，经常和高阶函数一块使用如map,filter,reduce
1. **map函数**，根据提供的函数对指定序列**做映射**，返回值为一个list；**map(enumerate),其中enumerate参数能获取到list中的下标及元素，如：**

```python
map(lambda x, y: x+y, [1, 2, 3], [4, 5, 6])
#结果为:[5, 7, 9]
map(lambda x: {x[0]:x[1]}, enumerate=[1,2,3])
# 结果[{0:1},{1:2},{2:3}]
```

2. **filter函数**，对指定序列执行过滤操作

```python
filter(function or None, sequence) -> list, tuple, or str
ing
# sequence:序列可以是str，tuple，list,返回值类型与之对应
filter(lambda x: x%2, [1, 2, 3, 4])
# [1, 3]
```

3. **reduce函数**，会对参数序列中元素进行累积

```python
from functools import reduce
# 定义：function参数是一个有两个参数的函数，sequence是一个序列，
# initial初始值，放在最开头计算
reduce(function, sequence [, initial] ) ->输出 list
reduce(lambda x, y: x+y, [1,2,3,4])
# 10
reduce(lambda x, y: x+y, ['aa', 'bb', 'cc'], 'dd')  # dd 初始值，放在最开头计算
# 'ddaabbcc'
alist = [[1, 2], [3, 4], [5, 6]]
reduce(lambda x, y: x+y, alist)
# [1, 2, 3, 4, 5, 6]
```

4. **集合 set**
元素不能重复。
可以求并集(x|y)、交集(x&y)、差集(x-y)、对称差集(x^y)

```python
>>> x = set('abcd')
>>> x
{'c', 'a', 'b', 'd'}
```

5. **repr()函数**
**能够将对象转化为字符串, 以字符串的形式输出**
```python
>>>d = 123
>>> repr(d)
"123"
>>>s = 'RUNOOB'
>>> repr(s)
"'RUNOOB'"
```

6. **eval()函数**，**eval(str)
用来计算在字符串中的有效Python表达式,并返回一个对象**
- 需求：读取项目中的ini文件，如3600*24,它为str类型的， 读取到py文件中，如何获取其计算结果，并比较大小

```python
s = str(3600*24)
i = eval(str)
print i
86400 <type 'int'>
```

7. **调试使用pdb模块**
8. **zip()函数，并行遍历**：zip()函数在运算时，会以一个或多个序列做为参数，**返回一个元组的列表（py2）,py3返回一个可迭代对象**。同时将这些序列中并排的元素配对。

```python
>> zip(['a', 'b', 'c'], [1,2,3])
>> [('a',1),('b',2),('c', 3)]
# zip()方法用在for循环中，就会支持并行迭代：
l1 = [2,3,4]
l2 = [4,5,6]
for (x,y) in zip(l1,l2):
    print x,y,'--',x*y
2 4 -- 8
3 5 -- 15
4 6 -- 24
```
9. **dir(obj) > 返回此对象的所有内置方法**

# 十、decode(解码)、encode(编码)
1. **字符串在Python内部的表示是unicode编码**，因此，在做编码转换时，通常需要以unicode作为中间编码，
2. 即**先将其他编码的字符串解码（decode）成unicode，再从unicode编码（encode）成另一种编码。**
3. **decode的作用是将其他编码的字符串转换成unicode编码**，如str1.decode('gb2312')，表示将gb2312编码的字符串str1转换成unicode编码。
4. **encode的作用是将unicode编码转换成其他编码的字符串**，如str2.encode('gb2312')，表示将unicode编码的字符串str2转换成gb2312编码。
5. 详见Resource/截图-Python/解码编码

# 十一、魔法方法(私有方法)
## 1、\_\_getattr\_\_(self, item)
1. 方法__getattr__, 当访问**对象的某个不存在的属性/方法的时候，调用此方法**
## 2、\_\_setattr\_\_(self, item, value)
1. 对对象的属性进行赋值时调用

## 3、\_\_getattribute\_\_(self, item)
1. 对对象的所有属性/方法的访问，都将调用此方法。
2. 在__getattr__之前调用，只在新式类（object）中才有。
3. 用这几个方法，可以做拦截器、动态代理等

## 4、\_\_repr\_\_、\_\_str\_\_
1. **改变实例对象的输出或显示方式__repr__**,与__str__方法效果一样.
2. 自定义__repr__() 和__str__() 通常是很好的习惯，因为它能简化调试和实例输出。
3. 如果__str__() 没有被定义，那么就会使用__repr__() 来代替输出。

```python
def __repr__(self):
    return 'PromeQue: {}'.format(self.id)
```

## 5、\_\_call\_\_方法
1. **\_\_call\_\_()的作用是使实例对象能够像函数一样被调用**
2. 实例化出来对象，即：*实例名()就是调用call（）方法*

## 6. \_\_getitem\_\_
1. 这个方法**返回所给键对应的值, 当实例对象做p[key]** 运算时，会调用类中的方法**__getitem__**
2. 一般如果想使用索引访问元素时，就可以在类中定义这个方法(__getitem__(self, key))。

# 十二、递归
1. 在函数内部，可以调用其他函数。如果一个**函数在内部调用自身本身**，这个函数就是**递归函数**。
2. 递归函数特性：
- 必须有一个明确的结束条件；
- 每次进入更深一层递归时，问题规模相比上次递归都应有所减少
- 相邻两次重复之间有紧密的联系，前一次要为后一次做准备（通常前一次的输出就作为后一次的输入）。
- 递归效率不高，递归层次过多会导致栈溢出（在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出）
