## 1、python实现单例模式



单例模式是指让一个类只能创建出唯一的实例。

用**装饰器**和**元类**这两种方式来实现单例模式，因为这两种方式的通用性最强


##  2、装饰器
本质上是一个python函数。它经常用于有**切面需求**的场景。比如：插入日志、性能测试、事务处理、缓存、权限校验等
**作用：**
它能使函数的功能得到扩充，而同时不用修改函数本身的代码。
它能够增加函数执行前、执行后的行为，而不需对调用函数的代码做任何改变。

```python

# 为函数添加一个统计运行时长的功能
import time
import threading
 
def how_much_time(func):
    def inner():
        t_start = time.time()
        func()
        t_end = time.time()
        print("一共花费了{0}秒时间".format(t_end - t_start, ))
    return inner
    # 将增加的新功能代码以及被装饰函数运行代码func()一同打包返回，返回的是一个内部函数，这个被返回的函数就是装饰器
 
def sleep_5s():
    time.sleep(5)
    print("%d秒结束了" % (5,))
 
def sleep_6s():
    time.sleep(6)
    print("%d秒结束了" % (6,))
 
sleep_5s = how_much_time(sleep_5s)
# 因为sleep_5s函数的功能就是睡5秒钟，虽然增加了统计运行时间的功能，但是他本身功能没变(还是睡5秒钟)，所以仍然用原来函数名接收增加功能了的自己
sleep_6s = how_much_time(sleep_6s)
 
t1 = threading.Thread(target=sleep_5s)
t2 = threading.Thread(target=sleep_6s)
t1.start()
t2.start()
# 5秒结束了
# 一共花费了5.014161109924316秒时间
# 6秒结束了
# 一共花费了6.011810302734375秒时间
```

语法糖写法：

```python
# 为函数添加一个统计运行时长的功能
import time
import threading
 
def how_much_time(func):
    def inner():
        t_start = time.time()
        func()
        t_end = time.time()
        print("一共花费了{0}秒时间".format(t_end - t_start, ))
 
    return inner
 
 
@how_much_time
# @how_much_time等价于sleep_5s = how_much_time(sleep_5s)
def sleep_5s():
    time.sleep(5)
    print("%d秒结束了" % (5,))
 
@how_much_time
def sleep_6s():
    time.sleep(6)
    print("%d秒结束了" % (6,))
 
t1 = threading.Thread(target=sleep_5s)
t2 = threading.Thread(target=sleep_6s)
t1.start()
t2.start()
 
```

## 3、pytho垃圾回收机制

一、引子

我们定义变量会申请[内存](https://so.csdn.net/so/search?q=内存&spm=1001.2101.3001.7020)空间来存放变量的值，而内存的容量是有限的，**当一个变量值没有用了(简称垃圾)就应该将其占用的内存空间给回收掉，而变量名是访问到变量值的唯一方式，所以当一个变量值没有关联任何变量名时，我们就无法再访问到该变量值了，该变量值就是一个垃圾会被python解释的垃圾回收机制自动回收**

二、什么是垃圾回收机制

垃圾回收机制(简称GC)是python解释器自带的一种机制，专门用来回收不可用的变量值所占用的内存空间

三、为什么要用垃圾回收机制

程序运行过程中会申请大量的内存空间，为对于一些无用的内存空间如果不及时清理的话会导致内存使用殆尽(内存的益出)，导致程序的崩溃，因此管理内存是一件非常重要而繁杂的事情，而python解释器自带的垃圾回收机制把程序员从繁杂的内存管理中解放出来

四、垃圾回收机制原理分析

python的GC模块主要运用了**“引用计数”(reference counting)**来跟踪和回收垃圾，在引用计数的基础上，还可以通过“**标记-清楚**”(Mark and sweep)解决容器对象可能产生的循环引用问题，并且通过“**分代回收**”(generation collection)以空间换取时间的方式来进一步提高垃圾回收的效率

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           

**python垃圾回收机制以引用计数为主，标记-清除和分代回收为辅。**

**引用计数**：Python在内存中存储每个对象的引用计数，对象被引用了计数加一，对象被释放了，计数减一，如果计数变成0，该对象就会消失，分配给该对象的内存就会释放出来。

```
引用计数就是：变量值被变量名关联的次数

如：name=“maple”

变量值maple被关联了一个name，称之为引用计数为1，

引用计数增加：

x=10(此时,变量值10的引用计数为1)

y=10(此时,把x的内存地址给了y,此时,x,y都关联了10,所以变量值10的引用计数为2)

引用计数减少：

x=3(此时，x与10解除了关联，与3建立了关联，变量10的引用计数为1)

del y (del的意思是解除变量名y与变量值10的关联关系，此时，变量10的引用计数为0)

这样变量值10的引用计数为0，其占用的内存地址就会被回收
```



**标记-清除**：是一种基于追踪回收(tracing GC)技术实现的垃圾回收算法。它分为两个阶段：第一阶段是标记阶段，GC会把所有的活动对象打上标记，第二阶段是把那些没有标记的对象非活动对象进行回收。对象之间通过引用(指针)连在一起，构成一个有向图，对象构成这个有向图的节点，而引用关系构成这个有向图的边。从根对象(root object)出发，沿着有向边遍历对象，可达的(reachable)对象标记为活动对象，不可达的对象就是要被清除的非活动对象。根对象就是全局变量、调用栈、寄存器。主要处理容器对象

**分代回收**：

==在多次扫描的情况下，都没有被回收的变量，GC机制就会认为，该变量是常用变量，GC对其扫描的频率会降低==

Python将内存根据对象的存活时间划分为不同的集合，每个集合称为一个代，Python将内存分为了3“代”，分别为年轻代(第0代)、中年代(第1代)、老年代(第2代)，他们对应的是3个链表，它们的垃圾收集频率与对象的存活时间的增大而减小。新创建的对象都会分配在年轻代，年轻代链表的总数达到上限时，Python垃圾收集机制就会被触发，把那些可以被回收的对象回收掉，而那些不会回收的对象就会被移到中年代去，依此类推，老年代中的对象是存活时间最久的对象，甚至是存活于整个系统的生命周期内。



## 4、lambda 表达式

简单来说，lambda表达式通常是当你需要使用一个函数，但是又不想费脑袋去命名一个函数的时候使用，也就是通常所说的匿名函数。

lambda表达式一般的形式是：关键词lambda后面紧接一个或多个参数，紧接一个冒号“：”，紧接一个表达式。lambda表达式是一个表达式不是一个语句。

```python
def add(x, y):
    return x+ y
print(add(1,2))

### 匿名函数
add = lambda x,y:x+y 
print(add(1,2))
```



## 5、Python多线程


   ### 5.1什么是线程？
   线程也叫轻量级进程，是操作系统能够进行运算调度的最小单位，它被包涵在进程之中，是进程中的实际运作单位。
   线程自己不拥有系统资源，只拥有一点儿在运行中必不可少的资源，但它可与同属一个进程的其他线程共享进程所
   拥有的全部资源。一个线程可以创建和撤销另一个线程，同一个进程中的多个线程之间可以并发执行.

###  5.2 为什么要使用多线程？
  线程在程序中是独立的、并发的执行流。与分隔的进程相比，进程中线程之间的隔离程度要小，它们共享内存、文件句柄
  和其他进程应有的状态。
  因为线程的划分尺度小于进程，使得多线程程序的并发性高。进程在执行过程之中拥有独立的内存单元，而多个线程共享
  内存，从而极大的提升了程序的运行效率。
  线程比进程具有更高的性能，这是由于同一个进程中的线程都有共性，多个线程共享一个进程的虚拟空间。线程的共享环境
  包括进程代码段、进程的共有数据等，利用这些共享的数据，线程之间很容易实现通信。
  操作系统在创建进程时，必须为改进程分配独立的内存空间，并分配大量的相关资源，但创建线程则简单得多。因此，使用多线程来实现并发比使用多进程的性能高得要多。

  ### 5.3 多线程编程优点
  进程之间不能共享内存，但线程之间共享内存非常容易。
  操作系统在创建进程时，需要为该进程重新分配系统资源，但创建线程的代价则小得多。因此使用多线程来实现多任务并发执行比使用多进程的效率高

　　**进程：正在运行的程序，QQ 360 ......**

​    **线程：就是进程中一条执行程序的执行路径，一个程序至少有一条执行路径。（360中的杀毒 电脑体检 电脑清理 同时运行的话就需要开启多条路径）**

　　**每个线程都有自己需要运行的内容，而这些内容可以称为线程要执行的任务。**

　　**开启多线程是为了同时运行多部分代码。**

　　**好处：解决了多部分需要同时运行的问题**

　　**弊端：如果线程过多，会导致效率很低（因为程序的执行都是CPU做着随机 快速切换来完成的）**

- **线程与进程的区别**
  - **线程共享内存，进程独立内存**
  - **线程启动速度块，进程启动速度慢，运行时速度没有可比性**
  - **同一个进程的线程间可以直接交流，两个进程想通信，必须通过一个中间代理来实现**
  - **创建新线程很简单，创建新进程需要对其父进程进行一次克隆**
  - **一个线程可以控制和操作同一线程里的其他线程，但是进程只能操作子进程**



## 6、Python深浅拷贝

在工作中，常涉及到数据的传递，在数据传递使用过程中，可能会发生数据被修改的问题。为了防止数据被修改，就需要在传递一个副本，即使副本被修改，也不会影响原数据的使用。为了生成这个副本，就产生了拷贝。今天就说一下Python中的深浅拷贝问题。

### 1、浅拷贝

其实列表是一个一个的槽位，每个槽位存储的是该对象的内存地址

**浅copy：会在内存中新开辟一个空间，存放这个copy的列表，但是列表里面的内容还是沿用之前对象的内存地址。**

![img](https://i0.hdslb.com/bfs/article/7232b27a6870a6a9eb8eeb22bee2e66c713e4aae.jpg@837w_731h_progressive.webp)

![img](https://i0.hdslb.com/bfs/article/dd19a85f1a2d1a24d5403d448f20d979de30e668.jpg@850w_879h_progressive.webp)

### 2、深拷贝

**深copy：会在内存中开辟新空间，将原列表以及列表里面的可变数据类型重新创建一份，不可变数据类型则沿用之前的。**

![img](https://i0.hdslb.com/bfs/article/d89c040fa559f7ee42ab7deabe1eef2a0f08f0b9.jpg@809w_636h_progressive.webp)

**为什么Python默认的拷贝方式是浅拷贝？**

时间角度：浅拷贝花费时间更少。

空间角度：浅拷贝花费内存更少。

效率角度：浅拷贝只拷贝顶层数据，一般情况下比深拷贝效率高。



### **总结**

不可变对象在赋值时会开辟新空间。

可变对象在赋值时，修改一个的值，另一个也会发生改变。

深、浅拷贝对不可变对象拷贝时，不开辟新空间，相当于赋值操作。

浅拷贝在拷贝时，只拷贝第一层中的引用，如果元素是可变对象，并且被修改，那么拷贝的对象也会发生变化。

深拷贝在拷贝时，会逐层进行拷贝，直到所有的引用都是不可变对象为止。

Python 有多种方式实现浅拷贝，copy模块的copy 函数 ，对象的 copy 函数 ，工厂方法，切片等。

大多数情况下，编写程序时，都是使用浅拷贝，除非有特定的需求。

浅拷贝的优点：拷贝速度快，占用空间少，拷贝效率高。



## 7、这两个参数是什么意思。*args、**kwargs。

**这也是python中非常有特色的：当\*和\**符号出现在函数定义的参数中时，表示任意数目参数收集。**

**在默认情况下，参数是通过其位置进行匹配的**，从左到右，而且必须精确的传递和函数头部参数名一样多的参数。

首先args，kwargs并不是必须这样设定的，只是一个约定俗成的名字，args（位置参数），kwargs（关键字参数）。
都用于函数的定义，用于将不定数量的参数传递给函数。
*args：用来发送非键值对可变数量参数，list，trump
**kwargs：用来发送键值对可变数量参数，dict

**这也是python中非常有特色的：当\*和\**符号出现在函数定义的参数中时，表示任意数目参数收集。**

```python
def myprint(*params):
    print(params)
    
myprint(1,2,3) 
==> （1,2,3）


def myprint1(*params):
    print(params)
myprint2(x=1,y=2,z=3) 
会返回一个字典
==> {'z'=3,'x'=1,'y'=2}
```

## 8、python中__new__和__init__的区别

__new__方法和__init__方法都是python中的构造方法，其中__new__方法使用较少，__init__方法使用较多。
首先来了解下这两种方法：

1.__new__是在**实例创建之前**被调用的，用于创建实例，然后返回该实例对象，是个静态方法。
2.__init__是当**实例对象创建完成后被调用的**，用于初始化一个类实例，是个实例方法。

由上可知，__new__先被调用，__new__的返回值将传递给__init__方法的第一个参数，然后__init__被调用，给这个实例设置一些参数。
```
class A():
    def __new__(cls, *args, **kwargs):
        print('this is A __new__')
        # return super(A, cls).__new__(cls)  # 或者下面这种形式，两种都可
        return object.__new__(cls)

    def __init__(self, title):
        print('this is A __init__')
        self.title = title


a = A('python book')

输出:
this is A __new__
this is A __init__

```



**两种方法的区别：**

1.__new__至少要有一个参数cls，**且必须要有返回值**，返回的是实例化出来的实例，有两种return方式：

```
return super(父类,cls).__new__(cls)
或者
return object.__new__(cls)

```

2.__init__**有一个参数self**，就是这个__new__返回的实例，__init__在__new__基础上完成一些其他初始化的动作，__init__不需要有返回值；



如果__new__没有返回cls(即当前类)的实例，那么当前类的__init__方法是不会被调用的；

```class A():
    def __new__(cls, *args, **kwargs):
        print('this is A __new__')
        # return super(A, cls).__new__(cls)  

    def __init__(self, title):
        print('this is A __init__')
        self.title = title


a = A('python book')

输出：
this is A __new__

```







































