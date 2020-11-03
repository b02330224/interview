### 1.python递归的最大层数

998



### 2.写一个函数实现将IP地址转换成一个整数



### 3.Python可迭代对象，迭代器，生成器的区别

#### 三者简要关系图



![img](https://img-blog.csdn.net/20170516000644044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluaXhpbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

#### 可迭代对象与迭代器



刚开始我认为这两者是等同的，但后来发现并不是这样；下面直接抛出结论：

1）可迭代对象包含迭代器。
2）如果一个对象拥有__iter__方法，其是可迭代对象；如果一个对象拥有next方法，其是迭代器。
3）定义可迭代对象，必须实现__iter__方法；定义迭代器，必须实现__iter__和next方法。** 



你也许会问，结论3与结论2是不是有一点矛盾？既然一个对象拥有了next方法就是迭代器，那为什么迭代器必须同时实现两方法呢？

因为结论1，迭代器也是可迭代对象，因此迭代器必须也实现__iter__方法。 



介绍一下上面涉及到的两个方法：

1）__iter__()

该方法返回的是当前对象的迭代器类的实例。因为可迭代对象与迭代器都要实现这个方法，因此有以下两种写法。

写法一：用于可迭代对象类的写法，返回该可迭代对象的迭代器类的实例。

写法二：用于迭代器类的写法，直接返回self（即自己本身），表示自身即是自己的迭代器。

也许有点晕，没关系，下面会给出两写法的例子，我们结合具体例子看。



2）next() 

返回迭代的每一步，实现该方法时注意要最后超出边界要抛出StopIteration异常。



下面举个可迭代对象与迭代器的例子：

```python
#!/usr/bin/env python  
# coding=utf-8  
  
  
class MyList(object):            # 定义可迭代对象类  
  
    def __init__(self, num):  
        self.data = num          # 上边界  
  
    def __iter__(self):  
        return MyListIterator(self.data)  # 返回该可迭代对象的迭代器类的实例  
  
  
class MyListIterator(object):    # 定义迭代器类，其是MyList可迭代对象的迭代器类  
  
    def __init__(self, data):  
        self.data = data         # 上边界  
        self.now = 0             # 当前迭代值，初始为0  
  
    def __iter__(self):  
        return self              # 返回该对象的迭代器类的实例；因为自己就是迭代器，所以返回self  
  
    def next(self):              # 迭代器类必须实现的方法  
        while self.now < self.data:  
            self.now += 1  
            return self.now - 1  # 返回当前迭代值  
        raise StopIteration      # 超出上边界，抛出异常  
  
  
my_list = MyList(5)              # 得到一个可迭代对象  
print type(my_list)              # 返回该对象的类型  
  
my_list_iter = iter(my_list)     # 得到该对象的迭代器实例，iter函数在下面会详细解释  
print type(my_list_iter)  
  
  
for i in my_list:                # 迭代  
    print i 
```

![image-20201103101759479](D:\code\python\installation_guide\python面试题\image-20201103101759479.png)

iter(collection)
1）用于返回collection对象的迭代器实例，这里的collection我认为表示的是可迭代对象，即该对象必须实现**iter**方法；**事实上iter函数与\**iter\**方法联系非常紧密，iter()是直接调用该对象的\**iter\**()，并把\**iter\**()的返回结果作为自己的返回值，故该用法常被称为“创建迭代器”。**
2）iter函数可以显示调用，或当执行“for i in obj:”，Python解释器会在第一次迭代时自动调用iter(obj)，之后的迭代会调用迭代器的next方法，for语句会自动处理最后抛出的StopIteration异常。



####  生成器





**生成器是一种特殊的迭代器，生成器自动实现了“迭代器协议”（即__iter__和next方法）**，不需要再手动实现两方法。

**生成器在迭代的过程中可以改变当前迭代值，而修改普通迭代器的当前迭代值往往会发生异常**，影响程序的执行。



看一个生成器的例子：

```python

#!/usr/bin/env python  
# coding=utf-8  
  
  
def myList(num):      # 定义生成器  
    now = 0           # 当前迭代值，初始为0  
    while now < num:  
        val = (yield now)                      # 返回当前迭代值，并接受可能的send发送值；yield在下面会解释  
        now = now + 1 if val is None else val  # val为None，迭代值自增1，否则重新设定当前迭代值为val  
  
my_list = myList(5)   # 得到一个生成器对象  
  
print my_list.next()  # 返回当前迭代值  
print my_list.next()  
  
my_list.send(3)       # 重新设定当前的迭代值  
print my_list.next()  
  
print dir(my_list)    # 返回该对象所拥有的方法名，可以看到__iter__与next在其中  
```



![image-20201103101933414](D:\code\python\installation_guide\python面试题\image-20201103101933414.png)

**具有yield关键字的函数都是生成器**，yield可以理解为return，返回后面的值给调用者。不同的是return返回后，函数会释放，而生成器则不会。在直接调用next方法或用for语句进行下一次迭代时，生成器会从yield下一句开始执行，直至遇到下一个yield。





#### 1.什么是迭代器？

> 一句话来说：在python中，内部含有'**iter**'方法并且含有'**next**'方法的对象就是迭代器。

#### 2.下列哪些对象是迭代器？

##### 2.1 str,list,tup,dict,set,文件句柄

> 直接说答案，只有文件句柄对象是迭代器。只有其内部含有'***\*iter\****'和'**next**'方法。

#### 3.将可迭代对象转化成迭代器。

```
l = [1,2,3,4]
obj = l.__iter__()

#obj = iter(l)
#这时候，就可以调用obj的__next__()方法迭代输出内部元素。
```

#### 4. for循环内部机制

> for循环的循环对象一定要是可迭代对象，但并不意味着可迭代对象就可以取值，因为for循环的内部机制是：将可迭代对象转换成迭代器，然后利用next进行取值，最后利用异常处理机制处理StopLieration抛出的异常。下面用while模拟for的内部循环机制。
>
> ```python
> l = [1,2,3,4,5]
> # 1. 将可迭代对象转化为迭代器.
> obj = iter(l)
> # 2. 利用while循环，next进行取值。
> while 1:
>     # 3. 利用异常处理终止循环。
>     try:
>         ptint(next(obj)
>     expect StopIteration:
>         break
> ```
>
> 



### 4.python2和python3的区别



​           **python2和python3中 int 和long区别**

​			Python3:Python3中int类型的范围是动态长度的,正整数或者负整数,用sys.getsizeof()可以看int占了几位.

​			Python2:Python2中long类型的范围是无限大小.

​			**xrange  range的区别**

​		 **xreadlines 和readlines区别**

​		xreadlines返回的是一个生成器类型,python3已经没有改方法.

​		readlines返回的是一个列表: [‘第一行\n’, ‘第二行\n’, ‘第三行’]

### 5.字符串，列表，元祖，字典每个常用的5个方法

### 6.谈谈python的深浅拷贝，以及实现方法和应用场景

### 7.python垃圾回收机制

#### 8.python的可变类型和不可变类型

### 9.常用模块有哪些

### 10.re的match和search区别？

### 11.什么是正则的贪婪匹配？

### 12 def func(a,b=[])这种写法有什么坑？

### 13.用python实现一个二分查找函数

### 14.谈谈对闭包的理解

### 15.os和sys模块的作用？

　    **os:**这个模块提供了一种方便的使用操作系统函数的方法。

　　**sys:**这个模块可供访问由解释器使用或维护的变量和与解释器进行交互的函数。

　　**总结:os模块负责程序与操作系统的交互，提供了访问操作系统底层的接口;sys模块负责程序与python解释器的交互，提供了一系列的函数和变量，用于操控python的运行时环境。**

**os 常用方法:**

os.remove(‘path/filename’) 删除文件

os.rename(oldname, newname) 重命名文件

os.walk() 生成目录树下的所有文件名

os.chdir('dirname') 改变目录

os.mkdir/makedirs('dirname')创建目录/多层目录

 

**sys 常用方法:**

 

sys.argv 命令行参数List，第一个元素是程序本身路径

sys.modules.keys() 返回所有已经导入的模块列表

sys.exc_info() 获取当前正在处理的异常类,exc_type、exc_value、exc_traceback当前处理的异常详细信息

sys.exit(n) 退出程序，正常退出时exit(0)

sys.path 返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值



### 16.如何生成一个随机数

### 17.面向对象深度优先和广度优先是什么？

### 18.functools函数的作用？

### 19.如何判断是函数还是方法？

通常我们认为在类中的函数为方法，类外面声明def为函数，这种说法有点片面

方法1：

```python
class Work(object):
    def show(self):
        print("执行show方法")

work = Work()
print(Work.show)
print(work.show)

结果：
<function Work.show at 0x000001CC55BC5268>
<bound method Work.show of <__main__.Work object at 0x000001CC55C2F240>>
```

可以看出通过类方法调用为函数，通过实例化对象调用为方法

方法2：

```python
from types import MethodType,FunctionType
print(isinstance(Work.show,FunctionType))
print(isinstance(Work.show,MethodType))
print(isinstance(work.show,FunctionType))
print(isinstance(work.show,MethodType))

结果：
True
False
False
True
```

 可以用内置的isinstance 来判断

### 20.静态方法和类方法区别？

**区别：**

静态方法装饰器下定义的方法属于函数（function）；

类方法装饰器下定义的方法属于方法（method）；

静态方法无需传入任何参数；

类方法传入的第一个参数必须是class本身cls；

静态方法与类方法一旦被调用，内存地址即确定。通过类调用和通过实例化对象调用的结果完全一样。





静态方法通过class调用或者通过实例化后的对象调用，是没有任何区别的，全部都是指向同一块内存地址。可以简单的理解成静态方法与类或者实例没有任何关系，一旦调用后，它的内存地址即确定。

类方法通过class调用或者通过实例化后的对象调用，是没有任何区别的，全部都是指向同一块内存地址。

### 21.什么是反射？以及应用场景

22.metaclass作用？以及应用场景

23.用尽量多的方法实现单例模式

24.装饰器的写法以及应用场景

25.什么是面向对象的mro

26.json序列化时，可以处理的数据类型有哪些，如何定制支持datetime类型？

dict、list、string、int、float、long、bool、None 可以被json序列化

对象，datetine不能json序列化



使用python的json模块序列化时间或者其他不支持的类型时会抛异常，例如下面的代码：

```python
from datetime import datetime
 
import json
 
if __name__=='__main__':
 now = datetime.now()
 json.dumps({'now':now})
```

意思是说datetime类不支持Json序列化

我们需要对json做下扩展，让它可以支持datetime类型。

```python
class ComplexEncoder(json.JSONEncoder):
  def default(self, obj):
    if isinstance(obj, datetime):
      return obj.strftime('%Y-%m-%d %H:%M:%S')
    elif isinstance(obj, date):
      return obj.strftime('%Y-%m-%d')
    else:
      return json.JSONEncoder.default(self, obj)
```

在调用json.dumps时需要指定cls参数为ComplexEncoder

例如：

```python
json.dumps({'now':now}, cls=ComplexEncoder)
```



### 27.json序列化时，默认遇到中文会转换成unicode,如果想要保留中文怎么办？

```python

import json
d = {"name":"英雄无敌7"}
 res = json.dumps(d)
# 打印res 会显示 {"name": "\u82f1\u96c4\u65e0\u654c7"}。原因是在默认情况下，调用json.dumps会对中文采用ASCII进行编码，故显示异常。
# 想要显示中文，需要更改默认的参数，如：
 res = json.dumps(d,ensure_ascii=False)
# print(res) 会显示  {"name": "英雄无敌7"}
```

### 28.简述yield和yield from关键字

## 网络和并发

### 1.简述三次握手，四次挥手流程

### 2.tcp,udp的区别

### 3.什么是粘包，socket中造成粘包的原因是什么？哪些情况会发生粘包现象？

在socket网络编程中，都是端到端通信，由客户端端口+服务端端口+客户端IP+服务端IP+传输协议组成的五元组可以明确的标识一条连接。在TCP的socket编程中，发送端和接收端都有成对的socket。发送端为了将多个发往接收端的包，更加高效的的发给接收端，于是采用了优化算法（Nagle算法），将多次间隔较小、数据量较小的数据，合并成一个数据量大的数据块，然后进行封包。那么这样一来，接收端就必须使用高效科学的拆包机制来分辨这些数据。

**1.Q：什么是TCP粘包问题**

TCP粘包就是指发送方发送的若干包数据到达接收方时粘成了一包，从接收缓冲区来看，后一包数据的头紧接着前一包数据的尾，出现粘包的原因是多方面的，可能是来自发送方，也可能是来自接收方。

**2.Q：造成TCP粘包的原因**

（1）发送方原因

TCP默认使用Nagle算法（主要作用：减少网络中报文段的数量），而Nagle算法主要做两件事：

1. 只有上一个分组得到确认，才会发送下一个分组
2. 收集多个小分组，在一个确认到来时一起发送

Nagle算法造成了发送方可能会出现粘包问题

（2）接收方原因

TCP接收到数据包时，并不会马上交到应用层进行处理，或者说应用层并不会立即处理。实际上，TCP将接收到的数据包保存在接收缓存里，然后应用程序主动从缓存读取收到的分组。这样一来，如果TCP接收数据包到缓存的速度大于应用程序从缓存中读取数据包的速度，多个包就会被缓存，应用程序就有可能读取到多个首尾相接粘到一起的包。

**3.Q：什么时候需要处理粘包现象**

1. 如果发送方发送的多组数据本来就是同一块数据的不同部分，比如说一个文件被分成多个部分发送，这时当然不需要处理粘包现象
2. 如果多个分组毫不相干，甚至是并列关系，那么这个时候就一定要处理粘包现象了

**4.Q：如何处理粘包现象**

（1）发送方

对于发送方造成的粘包问题，可以通过关闭Nagle算法来解决，使用TCP_NODELAY选项来关闭算法。

（2）接收方

接收方没有办法来处理粘包现象，只能将问题交给应用层来处理。

（2）应用层

应用层的解决办法简单可行，不仅能解决接收方的粘包问题，还可以解决发送方的粘包问题。

解决办法：循环处理，应用程序从接收缓存中读取分组时，读完一条数据，就应该循环读取下一条数据，直到所有数据都被处理完成，但是如何判断每条数据的长度呢？

1. 格式化数据：每条数据有固定的格式（开始符，结束符），这种方法简单易行，但是选择开始符和结束符时一定要确保每条数据的内部不包含开始符和结束符。
2. 发送长度：发送每条数据时，将数据的长度一并发送，例如规定数据的前4位是数据的长度，应用层在处理时可以根据长度来判断每个分组的开始和结束位置。

**5.Q：UDP会不会产生粘包问题呢**

TCP为了保证可靠传输并减少额外的开销（每次发包都要验证），采用了基于流的传输，基于流的传输不认为消息是一条一条的，是无保护消息边界的（保护消息边界：指传输协议把数据当做一条独立的消息在网上传输，接收端一次只能接受一条独立的消息）。

UDP则是面向消息传输的，是有保护消息边界的，接收方一次只接受一条独立的信息，所以不存在粘包问题。

举个例子：有三个数据包，大小分别为2k、4k、6k，如果采用UDP发送的话，不管接受方的接收缓存有多大，我们必须要进行至少三次以上的发送才能把数据包发送完，但是使用TCP协议发送的话，我们只需要接受方的接收缓存有12k的大小，就可以一次把这3个数据包全部发送完毕。

### 4.IO多路复用的作用？

### 5.select,poll,epoll模型的区别

### 6.什么是防火请以及作用？

​		在互联网上防火墙是一种非常有效的网络安全模型，通过它可以 **隔离风险区域** (即Internet或有一定风险的网络)与 **安全区域** (局域网)的连接，同时不会妨碍人们对 **风险区域的访问** 。所以它一般连接在核心交换机与外网之间。

​		防火墙可以监控进出网络的通信量，从而完成看似不可能的任务，仅让**安全**、核准了的信息**进入**，同时又抵制对企业构成威胁的数据，

7. 简述进程，线程，协程的区别以及应用场景？

8. GIL锁是什么？

9.threading.local的作用？

10.进程之间如何进行通信？

11.什么是并发和并行？

12. 生产者消费者模型应用场景及优势？