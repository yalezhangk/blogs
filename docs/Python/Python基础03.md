# Python基础03

# 一、Python整数输出问题，1输出为01或001
**S.zfill(width) -> string**
```python
# 字符串方法
<!--S.zfill(width) -> string-->
<!--width为字符的宽度-->
<!--'1'.zfill(2), 》 01-->
<!--'33'.zfill(3)， 》 033-->

for i in range(3):
    s = str(i).zfill(3)
    print(s)
    i += 1
000
001
002
```

# 二、Python函数参数传递的方式？
1. **不可变参数用值传递, 可变参数是引用传递：**
2. 值传递:表示传递直接传递变量的值，把传递过来的变量的值复制到形参中，这样在函数内部的操作不会影响到外部的变量
3. 引用传递:我个人觉得可以把引用理解为一个箭头，这个箭头指向某块内存地址，而引用传递，传递过来的就是这个箭头，当你修改内容的时候，就是修改这个箭头所指向的内存地址中的内容，因为外部也是指向这个内存中的内容的，所以，在函数内部修改就会影响函数外部的内容。

# 三、host 文件夹 
- C:\Windows\System32\drivers\etc

# 四、函数运行效率 （cProfile模块）
**Python的7种性能测试工具：timeit、profile、cProfile、line_profiler、memory_profiler、PyCharm图形化性能测试工具、objgraph**
```python
# 测试函数运行时间效率
import cProfile, random

def funtest(alist):
    sort_list = sorted(alist)
    return sort_list

if __name__ ** '__main__':
    alist = [random.random() for i in range(10000)]
    funtest(alist)
    cProfile.run('funtest(alist)')
# 结果
         5 function calls in 0.002 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.002    0.002 <string>:1(<module>)
        1    0.000    0.000    0.002    0.002 class_03.py:4(funtest)
        1    0.000    0.000    0.002    0.002 {built-in method builtins.exec}
        1    0.002    0.002    0.002    0.002 {built-in method builtins.sorted}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```

# 五、py3中dict有哪些变化（与py2比）
1. 支持字典解析表达式zip
```python
>>> d = {k:v for (k,v) in zip(['a','b','c'],[1,2,3])}
>>> d
{'a': 1, 'c': 3, 'b': 2}
```
2. 字典的一些方法，比如dict.key()、dict.values()、dict.items()操作方法，**在python 3.0中返回的不再是列表**，而是dict_keys对象，dict_values对象，dict_items对象
3. 取消has_key方法
4. 字典比较大小不再有效，在python 3.0中不同于2.6的用<、>来比较字典相对大小，而是用sorted(d1.items())<sorted(d2.items())方法手动比较排序后的键列表来操作。

# 六、abc模块，抽象基类
1. ABC，Abstract Base Class（抽象基类），主要定义了基本类和最基本的抽象方法，可以为子类定义共有的API，不需要具体实现。相当于是Java中的接口或者是抽象类。
2. 抽象基类可以不实现具体的方法（当然也可以实现，只不过子类如果想调用抽象基类中定义的方法需要使用super()）而是将其留给派生类实现。

# 七、查看一个类的继承顺序(mro)
1. py3: **类.mro()方法即可，返回类继承顺序list**
2. py2: Class.\__mro__(),返回类的继承顺序
```python
class A(object):
    def get(self):
        print('A')
class B(object):
    def get(self):
        print('B')
class C(B, A):
    def get(self):
        print('C')
c =C()
c.get()
# py3:
print(C.mro())
# py2:
print(C.__mro__())

# 输出结果
C
[<class '__main__.C'>, <class '__main__.B'>, <class '__main__.A'>, <class 'object'>]
```

# 八、with语句、\_enter__、__exit__方法
## 1、with语句工作原理
1. 有一些任务，可能事先需要设置，事后做清理工作。对于这种场景，Python的with语句提供了一种非常方便的处理方式。一个很好的例子是文件处理，你需要获取一个文件句柄，从文件中读取数据，然后关闭文件句柄。
2. 基本思想是with所求值的对象必须有一个__enter__()方法，一个__exit__()方法。
3. **紧跟with后面的语句被求值后，返回对象的__enter__()方法被调用，这个方法的返回值将被赋值给as后面的变量。当with后面的代码块全部被执行完之后，将调用前面返回对象的__exit__()方法**
4. 一个实现了__enter__和__exit__方法的对象就称之为**上下文管理器。**

```python
class Sample:
    def __enter__(self):
        print "In __enter__()"
        return "Foo"
    def __exit__(self, type, value, trace):
        print "In __exit__()"
def get_sample():
    return Sample()
with get_sample() as sample:
    print "sample:", sample
# 输出结果
In __enter__()
sample: Foo
In __exit__()
# 原理解析
__enter__()方法被执行
__enter__()方法返回的值 - 这个例子中是"Foo"，赋值给变量'sample'
执行代码块，打印变量"sample"的值为 "Foo"
__exit__()方法被调用
```
4. **开发库时，清理资源，关闭文件等等操作，都可以放在__exit__方法当中。总会执行__enter__方法**。
5. 在实际应用中 **，__enter__一般用于资源分配，如打开文件、连接数据库、获取线程锁；__exit__一般用于资源释放，如关闭文件、关闭数据库连接、释放线程锁。**

# 九、在Python中使用过什么代码检查工具？
1. **Pylint**:检测代码是否遵循PEP8规范
2. **flake8， 会检查py36,pep8,isort**

# 十、python高并发压力测试，如何测试的？
- 1 使用apache服务器中的ab.exe
- 2 pylot ;基于python的跨平台开源网站压力测试工具

# 十一、虚拟环境
1. virtualenv venv_flask
2. cd venv_flask/Script
3. activate 激活虚拟环境

# 十二、Python解释器及.py文件.pyc文件
1. 解释器大小3-4M左右
2. **python在执行.py文件时，会将其编译成（byte）的中间式文件.pyc,是为了加快下次文件的执行速度**。
3. 我们在运行python文件的时候，会**先检查是否有.pyc文件**，如果.pyc文件的修改时间与.py的修改时间一样，就会直接读取.pyc文件，否则执行.py文件
4. **只有.py文件被import时，才会生成相应的.pyc文件**

# 十三、 GIL锁
## 1. 概述
1. **GIL并不是python的特性，而是在实现Python解释器(CPython)时所引入的一个概念，然而由于CPython是大部分环境下默认的Python执行环境**。默认的解释器CPython有GIL锁限制，JPython就没有，或者写一个其他的不带GIL的python解释器，**最开始为了解决多线程之间数据完整性和状态同步而加了一把大锁-GIL**
2. **Python的多线程在多核CPU上，只对于IO密集型计算产生正面效果**；而当有至少有一个CPU密集型线程存在，那么多线程效率会由于GIL而大幅下降；**多进程适合在 CPU 密集型操作，多线程适合在 IO 密集型操作**
3. GIL的问题主要是操作系统由单核发展到多核导致的，单核上多线程任务调度，大家共享一个全局锁，谁在CPU执行，谁就占有这把锁，直到这个线程因为IO操作或者Timer Tick到期让出CPU。
4. **一个线程会在两种情况下释放GIL锁**：
- **线程进入IO操作时释放GIL**
- **解释器不间断运行了1000字节码(py2)或运行15毫秒后(py3)释放GIL**
## 2. GIL是线程安全的吗
1. **由于IO操作而释放的GIL是线程安全的；非IO操作，CPU密集型的，并且不是原子操作的计算时，需要加锁(threading.Lock)来保证线程安全。**
1. 如果多线程的操作中不是IO密集型，并且计算操作不是原子级的操作时，那么我们需要考虑线程安全问题，否则都不需要考虑线程安全
1. 有GIL并不意味着python一定是线程安全的，要分情况的。
2. GIL虽然从设计的出发点就是考虑到线程安全，但这种线程安全**是粗粒度的线程安全**，即不需要程序员自己对线程进行加锁处理（同理，所谓细粒度就是指程序员需要自行加、解锁来保证线程安全，典型代表是 Java , 而 CPthon 中是粗粒度的锁，即语言层面本身维护着一个全局的锁机制,用来保证线程安全）
3. **dis模块用来分析某个函数的字节码执行过程**，**针对原子级的操作，由于方法本身是单个字节码，所以线程没有办法在调用期间放弃GIL，所以他是线程安全的**。原子操作如：sorted()、list.sort()
```python
import dis
n = 0
def add():
    global n
    n = n + 1
print(dis.dis(add))

def sub():
    global n
    n = n - 1
print(dis.dis(sub))
```
4. dis结果分析：
![image](https://note.youdao.com/yws/res/26451/AC86EFEBB1C547959A5561AABEED60FC)

## 3. 避免GIL的影响
1. **IO密集型，多线程或者协程适合；CPU密集型，推荐多进程+协程的方式。**
2. **python下想要充分利用多核CPU，就用多进程，原因是：每个进程有各自独立的GIL，互不干扰**
## 4. CPython、PyPy
1. **CPython、PyPy、JPython等是各个解释器**
1. CPython是最流行的Python实现
2. PyPy也是一种Python实现，实现的语言是RPython
3. Python：共产主义；CPython：苏联；PyPy：天朝
4. **PyPI**(Python Package Index)**是python官方的第三方库的仓库**

# 十四、pipenv,Python包管理工具

# 十五、python 中的序列
1. 序列是python中的一种数据结构，可根据索引来获取序列中的对象
2. 有六种内建序列：list, tuple, string, unicode, buffer, xrange。xrange是一个生成器，其他几个类型具有的一些序列特性对它并不适合。

# 十六、pip换源
1. linux:
2. 修改 **~/.pip/pip.conf** (没有就创建一个)， 内容如下：（清华源）
3. [global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
4. pip install -i 临时源路径 包名  临时换源
#### 阿里pip源
https://mirrors.aliyun.com/pypi/simple
#### 豆瓣源
https://pypi.douban.com/simple/

# 十七、pdb、remote_pdb

## 7.1 本机pdb调试
### 在源代码处添加pdb
1. **在调试代码处，设置import pdb;pdb.set_trace()**
2. 接口跑到此处时，会进入pdb调试模式
3. (Pdb) xxxx
### pdb命令：
- bt 查看当前函数的调用堆栈
- p 打印变量
- n 下一行，不进入函数体
- s 进入函数体调试
- r 执行到函数返回处
- l 查看源代码
- c 非逐行跑，直到遇到下一个断点
- whatis xx 打印变量类型
- b  查看断点（后面带参数，表示设置断点）
- b lineno
- b filename:lineno 设置断点
- b functionname
- tbreak xx 设置临时断点

### 非侵入式，命名行调试
1. 不用额外修改源代码，在命令行下直接运行就能调试）
2. python3 -m pdb filename.py

## 7.2 远程调试
1. 在远程代码处设置

2. from remote_pdb import RemotePdb

3. RemotePdb('192.168.103.140', 4444).set_trace()

4. 调用接口，跑到此处时，

5. 执行：**socat readline tcp:192.168.103.140:4444**

6. 或者执行**telnet 192.168.103.140 4444**

7. 即可进入pdb模式
