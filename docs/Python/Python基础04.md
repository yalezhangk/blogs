# 一、内存泄漏、内存溢出
1. 内存溢出是指向JVM申请内存空间时没有足够的可用内存了，就会抛出OOM即内存溢出。
2. 内存泄漏是指，向JVM申请了一块内存空间，使用完后没有释放，由于没有释放，这块内存区域其他类加载的时候无法申请，
3. 同时当前类又没有这块内存空间的内存地址了也无法使用，相当于丢了一块内存，这就是内存泄漏。
4. 值得注意的是内存泄漏最终会导致内存溢出，很好理解，内存丢了很多最后当然内存不够用了。

## 1. Python内存泄漏的原因
- 所用到的用 C 语言开发的底层模块中出现了内存泄露。
- 代码中用到了全局的 list、 dict 或其它容器，不停的往这些容器中插入对象，而忘记了在使用完之后进行删除回收
- 代码中有“引用循环”，并且被循环引用的对象定义了__del__方法，就会发生内存泄露。
