
# Python基础01

# 一、换行符

- Unix 系统中：每行结尾只有 "<换行>"，即 "\n"；

-  Windows 系统中：每行结尾是 "<回车><换行>"，即 "\r\n"；

- Mac 系统中：每行结尾是 "<回车>"，即 "\r"。


# 二、运算符(and/or)

## 1. and/or

1. **在python中：0、''、""、[]、()、{}、None、False为假；其它任何东西都为真，注意' '、" "为真**
2. **and实例：从左到右扫描，返回第一个为假的表达式值，无假值则返回最后一个表达式值。**

```python
>>> 'a' and 'b'
'b'
>>> '' and 'b'
''
>>> ' a' and 'b' and 'c '
'c '
```

3. **or实例：从左到右扫描，返回第一个为真的表达式值，无真值则返回最后一个表达式值**。
4. **and or 没有先后级别;or有短路现象，即如果为真，后面的不执行**

```python
>> 'a' or 'b'
'a'
>> '' or 'b'
'b'
>> '' or "" or () or {}
{}
```

5. ** or短路现象 **

```python
>>>False and 1 or 2=>False or 2=>2 

>>>1 or 2 and False=>1（1为真之后，后面就不会判断了，直接返回1） 

>>>(1 or 2 ) and False=>1 and False=>False
```

## 2. 算术运算符

1. 有：**+(加) 、-(减)、*(乘)、/(除)、%(取模、取余)、**(幂)、//(取整除)**

**运算符执行顺序**

1. 一般按照从左到右的顺序正常执行。

2. **2 == 2 > 1 的结果却为True, **这涉及到Python中的**链式对比**，上式等价于 2 == 2 and 2 > 1 ,其结果为True

# 三、循环

1. break用来结束整个循环，continue用来结束本次循环，紧接着执行下一次循环

2. break/continue只能用在循环中，除此以外不能单独使用。break/continue在嵌套循环中，只对最近的一层循环起作用

# 四、函数及变量

1. 缺省参数，参数没传入的话，使用默认值

2. **带有默认值的参数一定要位于参数列表的最后面**

```python

def printinfo( name, age = 35 ):

   # 打印任何传入的字符串

   print "Name: ", name

   print "Age ", age

# 调用printinfo函数

printinfo(name="miki" )

printinfo( age=9,name="miki" )

```

3. 函数内部变量定义并且赋值，该变量为**局部变量**;

4. 在函数外部定义的变量，为**全局变量**；

5. 函数内部使用变量名，局部变量名和全局变量名同名，优先使用局部变量;

6. 在函数内部如果想要使用全局变量，使用**global关键字声明**;

7. **全局变量在函数内部未声明的情况下，只能使用，不可修改值**

8. **函数参数传递：可变参数是引用传递，不可变参数是值传递**

# 五、字符串str

1. 字符串本质是字符序列。Python的字符串是不可修改的。无法对原字符串进行修改，但是可以将字符串的一部分赋值到新字符串，来达到相同的修改效果。

2. 只有**+**和**\***运算

3. 提取用[],下标

4. 切片[starte:\end:step], 左闭右开，下标

5. 字符串反转[::-1]

##  5.1 字符串操作

1. len(str),获取长度

2. **str.split('分隔符', 分割次数) 分割成若干字串组成的列表，默认全分割**

3. **' '.join(li1), 以空格将列表组成为str，方法要求列表中的元素为字符串才可操作**

3. str.find('a'), str.rfind('a')查找字串，第一次、最后一次出现的位置，返回位置（下标），若无，返回-1

4. **str.startswith('a')** 以某字符开头，返回True或False

5. **str.endswith('b')以某字符结尾**

6. str.replace('a','b',2)替换

7. **str.splitlines()**, 返回str中的行列表，在行边界处中断。 通常用于有换行符n的切割，返回str列表

```python

# 字符串转列表

str1 = '1,2,3,4'

li1 = str1.split(',', 2)

# ['1','2','3,4']

# 列表转字符串

# 此方法要求列表中的元素为字符串才可操作

li2 = ['1','2','3','4']

s1 = '-'.join(li2)

# '1-2-3-4'

# 字符串替换

s = "abadeag"

s1 = s.replace("a", "1",2)

print(s1)

# 1b1deag

```

7. **str.strip()：默认删除两侧空格**

8. **str.strip('字符')：删除首尾两侧字符**

8. str.count('a'),统计某字符出现的次数

##  5.2 str格式化

1. **py2.6引入的：str.format()**

2. **f或F表达式，py3.6引入的格式化字符串的方法**



```python
>> name = 'yalezhang'

>> print('He is {}'.format(name))

He is yalezhang
# 格式化数字
>> print("{:.2f}".format(3.1415926));

3.14

# f/F表达式

>> name = 'yalezhang'

>> age = 12

>> a = f'He is {name}, age is {age}'

He is yalezhang, age is 12
```

# 六、列表 list

1. 列表非常适合利用顺序和位置定义某一个元素，尤其是当元素的顺序或内容经常发生改变的时候.列表可修改、添加、删除、覆盖

## 1. 列表操作

1. list.append('a'),尾部追加元素

2. extend()合并列表

3. **list1.extend(list2),等价于list1 += list2, 将lis2中的元素依次添加到list1中**

4. list.insert(1,'a')，指定位置前插入元素

5. python删除方法del，del list[0], 删除list第一个元素

6. **list.remove('a'), 删除a元素**

7. list.pop(1),输出第1位置的元素，并将之删除

8. list.index('a'), 返回a元素的下标

9. in 判断某元素是否存在

10. **str.join(list)，将列表转化为字符串**，**list中的元素必须是str型的**

```python
list = ['aaa', 'bbb', 'ccc']

str1 = ','.join(list)

str2 = ';'.join(list)

# 要求list中的元素必须是str

print(str1)
# aaa,bbb,ccc
print(str2)

# aaa;bbb;ccc
li = [1,2,3]

## 会报错，无法转换
str_error = ''.join(li)
```

11. **list.sort()，对原列表升序排序;

sorted(list),则会生成一个新列表，升序**

12. **sorted(元素，key, reverse),产生一个新列表**

```python

>>> fruits = ['grape', 'raspberry', 'apple', 'banana']
>>> sorted(fruits, key=len, reverse=True)
['raspberry', 'banana', 'grape', 'apple']
>>> fruits
['grape', 'raspberry', 'apple', 'banana']

```

12. len(list), 返回列表长度

13. **两个list求交集、并集、差集：可以将list转为set,效率更高**（注意list中不存在重复元素才可用set中转）

```python

list1 = [1,2,3,4]

list2 = [1,2,3,5]

retA = [i for i in list1 if i in list2]

retA = list(set(list1).intersection(ste(list2)))

等价于 retA = list(set(list1) & set(list2))

retB = list(set(list1).union(set(list2)))

等价于 retB = list(set(list1) | set(list2))

#求差集，在list1中但不在list2中

retC = list(set(list1).difference(set(list2)))

等价于 retC = list(set(list1) - set(list2))
```

# 七、元组Tuple()

1. 元组元素不可改变，占用空间小

2. python中不允许修改元组的数据，包括不能删除其中的元素。

3. 访问元组中的元素,通过下标：tuple[0]

```python
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5, 6, 7 )
print "tup1[0]: ", tup1[0]
print "tup2[1:5]: ", tup2[1:5]
```


# 八、字典dict{}

## 8.1 字典操作

**字典的k必须是可hash的，即键必须是不可变类型的(str、int、tuple、bool)**

1. **字典推导式

d = {key: value for (key, value) in dict.items()}**

1. **dict1.update(dict2)

更新dict1(dict2中的元素更新到dict1中)，以dict2为准**

```python
dict1 = {'name': 'yalezhang', 'age': 18}
dict2 = {'name': 'yalezhang', 'age': 19}
dict1.update(dict2)
print(dict1)
# {'name': 'yalezhang', 'age': 19}
```
2. dict.keys()获取所有键 > ['a', 'b', 'c']

3. dict.values()获取所有值 > [1,2,3]

4. dict.items()获取所有键值对 > [('a',1),('b',2),('c',3)]

5. len(dict1) 返回字典的长度

## 8.2 字典的底层实现

### 1. 概述

1. py3.7以前dict是无序的，3.7以后dict是有序的

2. **py字典是通过散列表(哈希表)实现的**，即哈希数组，即字典是一个数组，但数组的索引是键(key)经过哈希函数处理后得到的散列值。

3. 由于不同的键可能具有相同的哈希值，即哈希冲突。py中建立哈希表的具体过程如下：

- 数据添加：**把key通过哈希函数转换成一个整型数字，然后就将该数字对数组长度进行取余，取余结果就当作数组的下标，将value存储在以该数字为下标的数组空间里**。

- 数据查询：再次使用哈希函数将key转换为对应的数组下标，并定位到数组的位置获取value。

4. **哈希函数就是一个映射**，因此**怎么设计合理的哈希函数**很重要，**即key哈希后形成映射关系，本质上是一个多对一的映射,可能几个key的哈希值一样,就形成了哈希冲突**。

5. 新字典的2点变化：

- **字典占用的内存变小了**。旧字典为了减少hash冲突的概率，总会预留大于1/3的容量的hash位置，现在则不必预留。

- **字典有序了**。

### 2. 解决哈希冲突

> **1. 开放寻址法**

> 1. **py2使用开放寻址法解决冲突**，CPython使用**伪随机探测的散列表**作为字典的底层数据结构。

> 2. 开放寻址法中，所有的元素都存放在散列表里，当产生哈希冲突时，通过一个**探测函数**计算出下一个候选位置，如果下一个获选位置还是有冲突，那么不断通过探测函数往下找，直到找个一个空槽来存放待插入元素。

> 3. 开放地址的意思是除了哈希函数得出的地址可用，当出现冲突的时候其他的地址也一样可用，常见的开放地址思想的方法有**线性探测再散列，二次探测再散列等**，这些方法都是在第一选择被占用的情况下的解决方法。

> **2. 链表法**

> 1. HashMap数组的**每一个元素不仅是一个数组对象，还是一个链表的头节点**。每一个Entry对象通过next指针指向他的下一个Entry节点，**当新来的映射冲突时，只需插入到对应的链表中即可**。

> **3. 再哈希法**

> 1. 这个方法是按顺序**规定多个哈希函数，每次查询的时候按顺序调用哈希函数，调用到第一个为空的时候返回不存在，调用到此键的时候返回其值**。

> **4. 公共溢出区**

> 1. 其基本思想是：所有关键字和基本表中关键字为相同哈希值的记录，不管他们由哈希函数得到的哈希地址是什么，一旦发生冲突，都填入溢出表。

> **5. 装填因子α**

> 1. 一般情况下，处理冲突方法相同的哈希表，其平均查找长度依赖于哈希表的装填因子。哈希表的装填因子定义为表中填入的记录数和哈希表长度的比值，也就是标志着哈希表的装满程度。

> 2. 直观看来，**α越小，发生冲突的可能性就越小，反之越大。一般0.75比较合适**，涉及数学推导。



### 3. dict实现原理

#### 1. py3.7之前的无序dict

1. **dict底层维护一张哈希表**，我们可以把哈希表看成一个列表，哈希表中的每一个元素又存储了哈希值（hash）、键（key）、值（value）3个元素

```python
enteies = [ 
   ['--', '--', '--'],
   [hash, key, value],
​   ['--', '--', '--'],
​   ['--', '--', '--'],
​   [hash, key, value],
   ]
```

2. 由上可以见哈希表的存储结构，我们也可以看出，元素之间有一些空元素，我们通过增加一个元素来讲解具体实现。

- 计算key的hash值【hash(key)】，再和mask做与操作【mask=字典最小长度（DictMinSize） - 1】，运算后会得到一个数字【index】，这个index就是要插入的enteies哈希表中的下标位置

- 若index下标位置已经被占用，则会判断enteies的key是否与要插入的key是否相等

- 如果key相等就表示key已存在，则更新value值

- 如果key不相等，就表示hash冲突，则会继续向下寻找空位置，一直到找到剩余空位为止。

3. 我们可以看到，**不同的key计算的出的index值是不一样的，在enteies中插入的位置不一样，所以当我们遍历字典的时候，字段的顺序与我们插入的顺序是不相同的。**

4. 我们同样可以发现，enteies表是稀疏的，随着我们插入的值不同，enteies表会越来越稀疏（**enteies也是一个会动态扩展长度的，每一次扩展长度，都会重新计算所有key的hash值**），所以新的字典实现就随之出现。

#### 2. py3.7之后的有序dict

1. **hash表、加上Indices表(索引表、指数表)来辅助**，其数据结构如下

```python
indices = [None, None, index, None, index, None, index]
enteies = [
   [hash0, key0, value0],
   [hash1, key1, value1],
   [hash2, key2, value2]
]
```

2. 下面为具体的实现过程：

- **计算key的hash值【hash(key)】，再和mask做与操作【mask=字典最小长度（IndicesDictMinSize） - 1】，运算后会得到一个数字【index】，这个index就是要插入的indices的下标位置（注：具体算法与Python版本相关，并不一定一样）**

- **得到index后，会找到indices的位置，但是此位置不是存的hash值，而是存的len(enteies)，表示该值在enteies中的位置**

- **然后enteies会插入次元素**

- 如果出现hash冲突，则处理方式与老字典处理方式类似

3. **新字典存储数据本身的enteies并不会稀疏，由indices来维护具体存储的位置，enteies中的数据是和插入的数据是一样的，所以新的字典是有序的**。

4. 这种方法，字典 增删改查的时间复杂度 会有以前的O(1) 变为O(2)，因为多了一步查找的过程。而且字典扩容和缩容时要按照Indices的顺序来保持字典始终有序。

# 九、集合set {}

## 9.1 集合概念

1. **集合（set）是一个无序的不重复元素序列**。

2. 可以使用**大括号 { } 或者 set()** 函数创建集合，注意：创建一个空集合必须用 set() 而不是 { }，因为 { } 是用来创建一个空字典。

3. 基本功能包括关系测试和消除重复元素，集合对象**还支持union(联合)，常用于将list中的元素去重**

## 9.2 基本操作

1. S = set('abcde')

2. **添加元素：S.add(x),若x已存在，不进行任何操作**

3. 添加元素：S.update(x), x可以是多个，用逗号隔开

4. 移除元素：S.remove(x), 若元素不存在，会报错

5. **移除元素：S.discard(x), 若元素x存在，则移该元素，元素不存在，不会报错**

6. **S.pop(),随机删除一个元素，若在交互模式下，删除排序后的第一个元素**

6. 包含的操作如下,**交集&，并集|，差集-，对称差集^**

```python
>> a = set('abcde')

>> b = set('abcfg')

>> c = a & b

{'a','b','c'}

>> d = a | b

{'a','b','c','d','e','f','g'}

# 差集-, a中有，b中没有的

>> e = a-b
{'d','e'}

# 对称差集^, 在a中或者b中，但两者中不会同时存在

>> f = a^b

{'d','e','f','g'}

>> g = a.unition(b)

{'a','b','c','d','e','f','g'}
```

# 十、文件操作及os模块

1. 文件操作

```python
# 打开并关闭
f = open('file1.txt', 'w')
f.close()

# 读文件

f = open("./index.cpp","r")
# 写文件

f = open("./index.cpp","w")
contents = "hello world!"
f.write(contents)
#关闭文件
f.close()
```

2. os模块，python用来操作文件或文件夹的

3. 判断文件是否存在os.path.isfile(fileName)

# 十一、类和对象

## 11.1 私有的属性方法

1. 私有的属性，不能通过对象直接访问，但是可以通过方法访问

2. 私有的方法，不能通过对象直接访问

3. 私有的属性、方法，不会被子类继承，也不能被访问；

4. 一般情况下，私有的属性、方法都是不对外公布的，往往用来做内部的事情，起到安全的作用。

## 11.2 共有的属性方法

5. 继承，__init__()中使用super().__init()方法

5. **公有类属性，类和实例对象均能访问，类属性只能类修改**；实例对象若修改，则会认为是此实例对象动态添加了一个实例属性，不会影响到类属性。

6. 类外不可访问私有类属性，可以在类内通过方法调用

6. **实例属性，只能通过实例对象访问**

7. **类方法，@classmethod, 类和实例对象均能访问**

8. **实例方法，只能实例对象访问**

9. **静态方法，@staticmethod,类和实例对象均能调用，只能访问类属性**

10. **__new(cls,)，在init之前，用于创建类的实例，返回实例化出来的对象，必须有返回值**

```python


class Cat(object):

​    def __init__(self,name):
​        self.name = name
​        self.color = 'yellow'


class Bosi(Cat):

​    def __init__(self,name):
​        # 调用父类的__init__方法1(python2)
​        #Cat.__init__(self,name)
​        # 调用父类的__init__方法2
​        #super(Bosi,self).__init__(name)
​        # 调用父类的__init__方法3
​        super().__init__(name)

​    def getName(self):
​        return self.name


bosi = Bosi('xiaohua')

print(bosi.name)

print(bosi.color)

```

## 11.3 super()方法
1. **super()是用于调用父类（超类）的一个方法**
2. **Py3：super().xxx** 
3. **Py2: super(Class, self).xxx** 

```python


class FooParent(object):

​    def __init__(self):
​        self.parent = 'I'm the parent.'
​        print ('Parent')

​    def bar(self,message):
​        print ("%s from Parent" % message)


class FooChild(FooParent):

​    def __init__(self):
​        # super(FooChild,self) 首先找到 FooChild 的父类（就是类 FooParent），然后把类B的对象 FooChild 转换为类 FooParent 的对象
​        super(FooChild,self).__init__()    
​        print ('Child')


​    def bar(self,message):
​        super(FooChild, self).bar(message)
​        print ('Child bar fuction')
​        print (self.parent)

if __name__  == '__main__':
​    fooChild = FooChild()
​    fooChild.bar('HelloWorld')
# 输出
Parent
Child
HelloWorld from Parent
Child bar fuction
I'm the parent.
```

## 11.4 类继承顺序（深度优先、广度优先）(mro方法)

1. Python 类是支持**多继承**的，一个类的方法和属性可能定义在当前类，也可能定义在基类。

2. 针对这种情况，当调用类方法或类属性时，就需要对当前类以及它的基类进行搜索，以**确定方法或属性的位置，而搜索的顺序就称为方法解析顺序**。

3. 方法解析顺序（Method Resolution Order），简称 **MRO**。

1. **py2默认经典类，继承了object才是新式类**

2. **py3默认都是新式类**

3. **经典类都是深度优先，从左到右，纵向寻找**

4. **新式类：广度优先: 从左到右，横向继承**

5. **Class.mro()方法 > 类的继承查找顺序**

6. Python 发展至今，经历了以下 3 种 MRO 算法，分别是：

- 从左往右，采用深度优先搜索（DFS）的算法，称为旧式类的 MRO；会引发`菱形继承`问题

- 自 Python 2.2 版本开始，新式类在采用深度优先搜索算法的基础上，对其做了优化；违反了`单调性`原则

- 自 Python 2.3 版本，对新式类采用了 C3 算法。由于*Python 3.x仅支持新式类，所以该版本只使用`C3 算法`*。该方法有点类似于`图的拓扑排序`

```python

class A1:
   pass


class A2:
   pass

class B1(A1, A2):
   pass

class B2(A1, A2):
   pass

class C(B1, B2):
​   pass

c = C()

c.foo()

# 经典类：按照类的顺序纵向找其方法

C类的foo() > B1类的foo() > A1类的foo() > A2 > B2

# 新式类：从左到右，横向继承

C > B1 > B2 > A1 >A2
```

# 十二、函数（function）与方法（method）的区别

1. **范围： 函数 > 方法**

2. **函数是python中的一个可调用对象,方法是一种特殊的函数**

3. **实例方法，在类中未和类绑定，是函数；在实例中，此实例方法与实例绑定，即变成方法。**

4. **静态方法没有和任何类或实例绑定，所以静态方法是个函数**。

5. **装饰器不会改变被装饰函数或方法的类型**。

6. **使用type(),判断其是函数还是方法**
