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



## django

参考：

https://developer.aliyun.com/ask/259900?spm=a2c6h.13706215.wenda.3.1c194b57B7MO83

### 1、什么是wsgi？

```js
是web服务网关接口，是一套协议。
是通过以下模块实现了wsgi协议：
    - wsgiref
    - werkzurg
    - uwsgi   关于部署
以上模块本质：编写socket服务端，用于监听请求，当有请求到来，则将请求数据进行封装，然后交给web框架处理。
```

### django、flask、tornado框架的比较？

```js
- django，大而全的框架，它的内部组件比较多，内部提供：ORM、Admin、中间件、Form、ModelForm、Session、缓存、信号、CSRF；功能也相当完善。

- flask，微型框架，内部组件就比较少了，但是有很多第三方组件来扩展它，定制化程度高。
　　比如说有那个wtform（与django的modelform类似，表单验证）、flask-sqlalchemy（操作数据库的）、flask-session、flask-migrate、flask-script、blinker可扩展强，第三方组件丰富。所以对他本身来说有那种短小精悍的感觉

- tornado，异步非阻塞。

django和flask的共同点就是，他们2个框架都没有写socket，所以他们都是利用第三方模块wsgi。
但是内部使用的wsgi也是有些不同的：django本身运行起来使用wsgiref，而flask使用werkzeug wsgi

还有一个区别就是他们的请求管理不太一样：django是通过将请求封装成request对象，再通过参数传递，而flask是通过上下文管理机制。


Tornado：
是一个轻量级的Web框架，异步非阻塞+内置WebSocket功能。
目标：通过一个线程处理N个并发请求(处理IO)。
内部组件
    #内部自己实现socket
    #路由系统
    #视图
  #模板
　　 #cookie
    #csrf
```

### 2、django请求的生命周期？

- 用户请求 --> wsgi --> jango的中间件(方法process_request) --> url路由匹配 --> 视图 --> orm数据库操作 --> 模板渲染
     --> 中间件(方法process_response) --> wsgi -->用户
- ![image-20201103162729897](D:\code\python\python_interview\python\image-20201103162729897.png)

### 3、列举django的内置组件？

- .Admin是对model中对应的数据表进行增删改查提供的组件
- .model组件：负责操作数据库
- .form组件：1.生成HTML代码2.数据有效性校验3校验信息返回并展示
- .ModelForm组件即用于数据库操作,也可用于用户请求的验证

```js
表单form：

- 对用户请求的数据进行校验
- 生成HTML标签

PS：
- form对象是一个可迭代对象。
- 问题：如何实现choice的数据实时更新？（动态数据，而不是写死）
- 解决：给该字段定义成ModelChoiceField的时候利用好"queryset"参数

  class UserForm(Form):
  　　ut_id = ModelChoiceField(queryset=models.UserType.objects.all())    　　# 从另一张依赖表中提取数据
            
  依赖表：
  class UserType(models.Model):
  　　title = models.CharField(max_length=32)
信号signal：

django的信号其实就是django内部为开发者预留的一些自定制功能的钩子。
只要在某个信号中注册了函数，那么django内部执行的过程中就会自动触发注册在信号中的函数。
如：

pre_save & post_save    在ORM模型的save()方法调用之前或之后发送信号
pre_delete & post_delete    在ORM模型或查询集的delete()方法调用之前或之后发送信号
request_started & request_finished    当接收和关闭HTTP请求时发送信号
m2m_changed    当多对多字段被修改时发送信号

场景:
在数据库某些表中添加数据时，可以进行日志记录。
中间件middleware：

对所有的【请求】进行【批量】处理，说得直白一点中间件是帮助我们在视图函数执行之前和执行之后都可以做一些额外的操作，它本质上就是一个自定义类。其影响的是全局，需谨慎使用。

应用：用户登录校验
问题：为甚么不使用装饰器？
如果不使用中间件，就需要给每个视图函数添加装饰器，太繁琐。
权限：

用户登录后，将权限放到session中，然后再每次请求进来在中间件里，根据当前的url去session中匹配，
判断当前用户是否有权限访问当前url，有权限就继续访问，没有就返回，
检查的东西就可以放到中间件中进行统一处理，在process_request方法里面做的，
我们的中间件是放在session后面，因为中间件需要到session里面取数据。
会话session：

cookie与session区别
（a）cookie是保存在浏览器端的键值对，而session是保存的服务器端的键值对，但是依赖cookie。（也可以不依赖cookie，可以放在url，或请求头但是cookie比较方便）
（b）以登录为例，cookie为通过登录成功后，设置明文的键值对，并将键值对发送客户端存，明文信息可能存在泄漏，不安全；
　　 session则是生成随机字符串，发给用户，并写到浏览器的cookie中，同时服务器自己也会保存一份。
（c）在登录验证时，cookie：根据浏览器发送请求时附带的cookie的键值对进行判断，如果存在，则验证通过；
　　 session：在请求用户的cookie中获取随机字符串，根据随机字符串在session中获取其对应的值进行验证
```

跨域请求cors（场景：前后端分离时，本地测试开发时使用）：

如果网站之间存在跨域，域名不同，端口不同会导致出现跨域，但凡出现跨域，浏览器就会出现同源策略的限制。
解决：在我们的服务端给我们响应数据，加上响应头 --> 在中间件加的。

```js
缓存cache：
   
常用的数据放在缓存里面，就不用走视图函数，请求进来通过所有的process_request，会到缓存里面查数据，有就直接拿，没有就走视图函数。
关键点：1：执行完所有的process_request才去缓存取数据
　　　　2：执行完所有的process_response才将数据放到缓存

关于缓存问题
1:为什么放在最后一个process_request才去缓存？
因为需要验证完用户的请求，才能返回数据

2:什么时候将数据放到缓存中？
第一次走中间件，缓存没有数据，会走视图函数，取数据库里面取数据，
当走完process_response,才将数据放到缓存里，因为，走process_response的时候可能给我们的响应加处理。
  3:为什么使用缓存？
  将常用且不太频繁修改的数据放入缓存。
  以后用户再来访问，先去缓存查看是否存在，如果有就返回
  否则，去数据库中获取并返回给用户（再加入到缓存，以便下次访问）
CSRF-TOKEN：
  目标：防止用户直接向服务端发起POST请求。
  对所有的post请求做验证，将jango生成的一串字符串发送给后台，一种是从请求体发过来，一种是放在隐藏的标签里面。
  方案：先发送GET请求时，将token保存到：cookie、Form表单中（隐藏的input标签），
  以后再发送请求时只要携带过来即可。
```

### 4、列举django中间件的5个方法？以及django中间件的应用场景？

```python
# 这些方法中的参数都是与视图函数参数对应的
process_request(self, request)  主要方法。请求刚进来时，执行视图函数之前调用。(无return)
process_view(self, request, callback, callback_args, callback_kwargs)　　URL路由匹配成功后，执行视图函数之前调用，拿到视图函数对象，及其所有参数。(无return)
process_exception(self, request, exception)　　执行视图函数中遇到异常时调用。(无return)
process_template_response(self, request, response)　　很少用。执行了render()渲染方法后调用。(有return)
process_response(self, request, response)　　主要方法。执行视图函数结束之后有响应时调用。(有return)
```

### 5、简述什么是FBV和CBV？

FBV和CBV本质是一样的，基于函数的视图叫做FBV，基于类的视图叫做CBV
 在python中使用CBV的优点：
 \- .提高了代码的复用性，可以使用面向对象的技术，比如Mixin（多继承）
 \- .可以用不同的函数针对不同的HTTP方法处理，而不是通过很多if判断，提高代码可读性

### FBV与CBV的区别

- 没什么区别，因为他们的本质都是函数。CBV的.as_view()返回的view函数，view函数中调用类的dispatch方法， 在dispatch方法中通过反射执行get/post/delete/put等方法。
- 非要说区别的话： CBV比较简洁，GET/POST等业务功能分别放在不同get/post函数中。FBV自己做判断进行区分。

### 6、django的request对象是在什么时候创建的？

`class WSGIHandler(base.BaseHandler):`
 `-------request = self.request_class(environ)`
 请求走到WSGIHandler类的时候，执行**cell**方法，将environ封装成了request

### 7、如何给CBV的程序添加装饰器？



```ruby
        from django.utils.decorators import method_decorator
        1、给方法加：
            @method_decorator(check_login)
            def post(self, request):
                ...
        2、给dispatch加：
            @method_decorator(check_login)
            def dispatch(self, request, *args, **kwargs):
                ...
        3、给类加：
            @method_decorator(check_login, name="get")
            @method_decorator(check_login, name="post")
            class HomeView(View):
                ...
```

### 8、列举django orm 中所有的方法（QuerySet对象的所有方法）



```xml
  <1> all():                  查询所有结果 
  <2> filter(**kwargs):       它包含了与所给筛选条件相匹配的对象。获取不到返回None
  <3> get(**kwargs):          返回与所给筛选条件相匹配的对象，返回结果有且只有一个。
                              如果符合筛选条件的对象超过一个或者没有都会抛出错误。
  <4> exclude(**kwargs):      它包含了与所给筛选条件不匹配的对象
  <5> order_by(*field):       对查询结果排序
  <6> reverse():              对查询结果反向排序 
  <8> count():                返回数据库中匹配查询(QuerySet)的对象数量。 
  <9> first():                返回第一条记录 
  <10> last():                返回最后一条记录 
  <11> exists():              如果QuerySet包含数据，就返回True，否则返回False
  <12> values(*field):        返回一个ValueQuerySet——一个特殊的QuerySet，运行后得到的
                              并不是一系 model的实例化对象，而是一个可迭代的字典序列
  <13> values_list(*field):   它与values()非常相似，它返回的是一个元组序列，values返回的是一个字典序列
  <14> distinct():            从返回结果中剔除重复纪录
```

### only()和defer()的区别？

only()查询指定的字段，defer()查询排除指定的字段。

### 9、select_related和prefetch_related的区别？

```js
# 相同点：它俩都用于连表查询，缓存查询结果，减少SQL查询次数。

# 不同点：
select_related 主要针对一对一和一对多关系进行优化。通过多表join关联查询，一次性获得所有数据，缓存在内存中，但如果关联的表太多，会严重影响数据库性能。
prefetch_related 主要针对多对多关系进行优化。通过分表，先获取各个表的数据，缓存在内存中，然后通过Python处理他们之间的关联。
```

### 10、filter和exclude的区别？

两者取到的值都是QuerySet对象,filter选择满足条件的,exclude:排除满足条件的.

### 11、列举django orm中三种能写sql语句的方法



```python
1.使用execute执行自定义的SQL
     直接执行SQL语句（类似于pymysql的用法）
        # 更高灵活度的方式执行原生SQL语句
        from django.db import connection
        cursor = connection.cursor()
        cursor.execute("SELECT DATE_FORMAT(create_time, '%Y-%m') FROM blog_article;")
        ret = cursor.fetchall()
        print(ret)
2.使用extra方法 ：queryset.extra(select={"key": "原生的SQL语句"})
3.使用raw方法
    1.执行原始sql并返回模型
    2.依赖model多用于查询
```

### Django ORM中如何设置读写分离？

```python
步骤一：写配置文件
class Router1:
　　#  指定到某个数据库读数据
    def db_for_read(self, model, **hints):
        if model._meta.model_name == 'usertype':
            return 'db1'
        else:
            return 'default'
　　　
　　# 指定到某个数据库写数据
    def db_for_write(self, model, **hints):
        return 'default'

再写到配置
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    },
    'db1': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
DATABASE_ROUTERS = ['db_router.Router1',]


步骤二：手动使用queryset的using方法
def index(request):
    models.UserType.objects.using('db1').create(title='普通用户')
　　# 手动指定去某个数据库取数据
    result = models.UserType.objects.all().using('db1')
    return HttpResponse('...')
```

### 12、values和values_list的区别？

- values : queryset类型的列表中是字典

- values_list : queryset类型的列表中是元组

    

### F和Q表达式的作用?

```python
F：主要用来对字段的值进行四则计算。
　Goods.objects.update(price=F("price")+10)  # 对于goods表中每件商品的价格都在原价格的基础上增加10元
Q：用来进行复杂查询，实现"与"、"或"、"非"查询。
  Q(条件1) | Q(条件2) 　　# 或
  Q(条件1) & Q(条件2) 　　# 且
  ~ Q(条件)　　　　　　　　# 非
```

### Django的Form和ModeForm的作用？

```python
- 作用：
      - 对用户请求数据格式进行校验
      - 自动生成HTML标签
 - 区别：
      - Form，字段需要自己手写。
          class Form(Form):
              xx = fields.IntegerField(.)
              xx = fields.CharField(.)
              xx = fields.EmailField(.)
              xx = fields.ImageField(.)

      - ModelForm，可以通过Meta进行定义
          class MForm(ModelForm):
              class Meta:
　　　　　　　　　　 model = UserInfo
                  fields = "__all__"
                                              
  - 应用：只要是客户端向服务端发送表单数据时，都可以进行使用，如：用户登录注册
```

### 13、cookie和session的区别：

- .cookie:
     cookie是保存在浏览器端的键值对,可以用来做用户认证
- .session：
     将用户的会话信息保存在服务端,key值是随机产生的字符串,value值是session的内容
     依赖于cookie将每个用户的随机字符串保存到用户浏览器上
- Django中session默认保存在数据库中：django_session表
- flask,session默认将加密的数据写在用户的cookie中

### 14、如何使用django orm批量创建数据？



```go
objs=[models.Book(title="图书{}".format(i+15)) for i in range(100)]
models.Book.objects.bulk_create(objs)
```

### 15、django的Form组件中,如果字段中包含choices参数，请使用两种方式实现数据源实时更新

```python
方式一：重写初始化方法，在构造方法中重新去数据库获取值
  class UserForm(Form):
      ut_id = fields.ChoiceField(choices=())

      def __init__(self, *args, **kwargs):
          super(UserForm, self).__init__(*args, **kwargs)
          self.fields['ut_id'].choices = models.UserType.objects.all().values_list('id', 'title')
  
方式二: ModelChoiceField字段
  class UserForm(Form):
      ut_id = ModelChoiceField(queryset=models.UserType.objects.all())    　　# 从另一张依赖表中提取数据

  依赖表：
      class UserType(models.Model):
          title = models.CharField(max_length=32)
```



### 16、django的Model中的ForeignKey字段中的on_delete参数有什么作用？

```python
在django2.0后，定义外键和一对一关系的时候需要加on_delete选项，此参数为了避免两个表里的数据不一致问题，不然会报错：

user=models.OneToOneField(User,on_delete=models.CASCADE)          --在老版本这个参数（models.CASCADE）是默认值
owner=models.ForeignKey(UserProfile,on_delete=models.CASCADE)     --在老版本这个参数（models.CASCADE）是默认值

参数说明：
on_delete有CASCADE、PROTECT、SET_NULL、SET_DEFAULT、SET()五个可选择的值

CASCADE：级联删除。
PROTECT：报完整性错误。
SET_NULL：把外键设置为null，前提是允许为null。
SET_DEFAULT：把设置为外键的默认值。
SET()：调用外面的值，可以是一个函数。
一般情况下使用CASCADE就可以了。
```

### Django中csrf的实现机制？

目的：防止用户直接向服务端发起POST请求
\- 用户先发送GET获取令牌csrf token: Form表单中一个隐藏的标签 + token；
\- 发起POST请求时，需要携带这个令牌csrf token；
\- 在中间件的process_view方法中进行令牌校验。

在html中添加{%csrf_token%}标签。

### 17、django的模板中自定义filter和simple_tag的区别？

- 自定义filter：{{ 参数1|filter函数名:参数2 }}
     1.可以与if标签来连用
     2.自定义时需要写两个形参



```python
    例子：自定义filter
            1. 在app01下创建一个叫templatetags的Python包
            2. 在templatetags的文件夹下创建py文件  myfilters
            3. 在py文件中写代码
                from django import template
                register = template.Library()
                
                @register.filter
                def add_sb(value,arg='aaa'):
                    return "{}_sb_{}".formart(value,arg)
                    
                @register.filter(name='sb')
                def add_sb(value,arg='aaa'):
                    return "{}_sb_{}".formart(value,arg)     
            4. 使用自定义filter
                {% load myfilters %}
                {{ name|add_sb:'xxx'}}
                {{ name|sb:'xxx'}}
```

- simple_tag:{% simple_tag函数名 参数1 参数2 %}
     1.可以传多个参数,没有限制
     2.不能与if标签来连用



```python
例子：自定义simpletag
    创建
        1 、在app01中创建一个名字是templatetags的包，
        2、在包中创建一个py文件
        3、在py文件中导入
              from django import template
              register = template.Library()
        4、写函数
              @register.simple_tag(name="plus")
              def plus(a,b,c):
                  return '{}+{}+{}'.format(a,b,c)
        5、加装饰器@register.simple_tag(name="plus")
  使用
      1、导入
            {% load mytag %}
      2、使用
           {% plus 1 2 3 %}
```



### 19、基于django使用ajax发送post请求时，都可以使用哪种方法携带csrf token？

- 1.后端将csrftoken传到前端，发送post请求时携带这个值发送



```css
data: {
        csrfmiddlewaretoken: '{{ csrf_token }}'
  },
```

- 2.获取form中隐藏标签的csrftoken值，加入到请求数据中传给后端



```css
data: {
          csrfmiddlewaretoken:$('[name="csrfmiddlewaretoken"]').val()
     },
```

- 3.cookie中存在csrftoken,将csrftoken值放到请求头中



```jsx
headers:{ "X-CSRFtoken":$.cookie("csrftoken")}
```

### 20、Django本身提供了runserver，为什么不能用来部署？(runserver与uWSGI的区别)

- 1.runserver方法是调试 Django 时经常用到的运行方式，它使用Django自带的
     WSGI Server 运行，主要在测试和开发中使用，并且 runserver 开启的方式也是单进程 。
- 2.uWSGI是一个Web服务器，它实现了WSGI协议、uwsgi、http 等协议。注意uwsgi是一种通信协议，而uWSGI是实现uwsgi协议和WSGI协议的 Web 服务器。uWSGI具有超快的性能、低内存占用和多app管理等优点，并且搭配着Nginx就是一个生产环境了，能够将用户访问请求与应用 app 隔离开，实现真正的部署 。相比来讲，支持的并发量更高，方便管理多进程，发挥多核的优势，提升性能。

### 21、Django如何实现websocket？

- django实现websocket官方推荐大家使用channels。channels通过升级http协议 升级到websocket协议。保证实时通讯。也就是说，我们完全可以用channels实现我们的即时通讯。而不是使用长轮询和计时器方式来保证伪实时通讯。他通过改造django框架，使django既支持http协议又支持websocket协议。

### 22. django中如何实现orm表中添加数据时创建一条日志

```python
# 使用Django的信号机制，可以在添加、删除数据前后设置日志记录：
pre_init  # Django中的model对象执行其构造方法前,自动触发
post_init  # Django中的model对象执行其构造方法后,自动触发
pre_save  # Django中的model对象保存前,自动触发
post_save  # Django中的model对象保存后,自动触发
pre_delete  # Django中的model对象删除前,自动触发
post_delete  # Django中的model对象删除后,自动触发

# 使用
@receiver(post_save, sender=Myclass)　　 　　　　# 信号接收装饰器。由于内置信号，所以直接接收
def signal_handler(sender, **kwargs):　　　　　　# 接收到信号后，在此处理
　　 logger = logging.getLogger()
    logger.success('保存成功')
```



### 23.Django缓存如何设置？

```python
Django中提供了6种缓存方式：
　　开发调试（默认缓存）
　　内存
　　文件
　　数据库
　　Memcache缓存
　　第三方库支持redis：django-redis


设置缓存：
# 全站缓存（中间件）
MIDDLEWARE = [
    ‘django.middleware.cache.UpdateCacheMiddleware’, 　　　　#第一个位置
    'django.middleware.common.CommonMiddleware',
    ‘django.middleware.cache.FetchFromCacheMiddleware’, 　　 #最后位置
]
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache', 　　 # 取决于您选择的Memcached绑定
        'LOCATION': ['127.0.0.1:11211', ],  　　# 缓存后端服务器位置，支持分布式，可多个
        'TIMEOUT': 5 * 60,  　　# 缓存超时，默认300s
	}
}
 

# 视图缓存
@cache_page(15)          #超时时间为15秒
def index(request):
   t=time.time()      　　#获取当前时间
   return render(request,"index.html",locals())
 

# 模板缓存
{% load cache %}

{% cache 2 'name' %} 　　# 存的key
   <h3>缓存:{{ t }}</h3>
{% endcache %}
```

三：配置缓存

1.开发调试

settings.py文件配置代码如下：

```python
CACHES = {
 'default': {
  'BACKEND': 'django.core.cache.backends.dummy.DummyCache',  # 缓存后台使用的引擎
  'TIMEOUT': 300,            # 缓存超时时间（默认300秒，None表示永不过期，0表示立即过期）
  'OPTIONS':{
   'MAX_ENTRIES': 300,          # 最大缓存记录的数量（默认300）
   'CULL_FREQUENCY': 3,          # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
  },
 }
}
```

2.内存缓存

settings.py文件配置代码如下

```python
CACHES = {
 'default': {
  'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',  # 指定缓存使用的引擎
  'LOCATION': 'unique-snowflake',         # 写在内存中的变量的唯一值 
  'TIMEOUT':300,             # 缓存超时时间(默认为300秒,None表示永不过期)
  'OPTIONS':{
   'MAX_ENTRIES': 300,           # 最大缓存记录的数量（默认300）
   'CULL_FREQUENCY': 3,          # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
  }  
 }
}
```



3.文件缓存

settings.py文件配置代码如下

```python
CACHES = {
 'default': {
  'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache', #指定缓存使用的引擎
  'LOCATION': '/var/tmp/django_cache',        #指定缓存的路径
  'TIMEOUT':300,              #缓存超时时间(默认为300秒,None表示永不过期)
  'OPTIONS':{
   'MAX_ENTRIES': 300,            # 最大缓存记录的数量（默认300）
   'CULL_FREQUENCY': 3,           # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
  }
 }   
}
```

4.数据库缓存

settings.py文件配置代码如下：

```python
CACHES = {
 'default': {
  'BACKEND': 'django.core.cache.backends.db.DatabaseCache',  # 指定缓存使用的引擎
  'LOCATION': 'cache_table',          # 数据库表    
  'OPTIONS':{
   'MAX_ENTRIES': 300,           # 最大缓存记录的数量（默认300）
   'CULL_FREQUENCY': 3,          # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
  }  
 }   
}
```



### 24.Django的缓存能使用redis吗？如果可以的话，如何配置？

```python
# 在setting添加配置文件
# 配置中间件同上。
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache", 　　# 缓存类型
        "LOCATION": "127.0.0.1:6379", 　　# 缓存服务器IP和端口
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",  
            "CONNECTION_POOL_KWARGS": {"max_connections": 100} 　　# 连接池最大连接数
            # "PASSWORD": "123456",
        }
    }
}

# 使用
def index(request):
　　conn = get_redis_connection("default")　　　　# 根据名字去连接池中获取连接
　　conn.hset('n1','k1','v1') 　　# 存数据
　　return HttpResponse('...')
```

### 25.Django路由系统中name的作用？

反向解析路由字符串.
url(r'^home', views.home, name='home')
在模板中使用：{ % url 'home' %}
在视图中使用：reverse('home'）



### 26.Django的模板中filter和simple_tag的区别？

filter: 过滤器，只能接受两个参数，第一个参数是|前的数据。 用于操作变量, 支持 if 条件。
simple_tag: 标签（简单标签）。 不限制传参，不支持 if 条件,用于操作模板模块。



### 27.Django-debug-toolbar的作用？

查看访问的速度、数据库的行为、cache命中等信息
尤其在Mysql访问等的分析上大有用处(sql查询速度)

### 28.Django中如何实现单元测试unittest？

单元测试是class类（继承TestCase），每一个测试方法必须以"test"开头。你可以重写setUp()（测试开始之前的操作）和tearDown()（测试结束之后的操作）方法。
常用的断言方法：assertEqual()。
会单独新建一个测试数据库来进行数据库的操作方面的测试，垃圾数据默认在测试完成后销毁。
Django单元测试时为了模拟生产环境，会修改settings中的变量，例如, 把DEBUG变量修改为True, 把ALLOWED_HOSTS修改为[*]。



### 29.解释ORM中 db first 和 code first的含义？

db first: 先创建库，再更新model
code first：先创建model，再更新库



### 30.Django中如何根据数据库表生成model中的类？

1、修改seting文件，在setting里面设置要连接的数据库类型和名称、地址
2、运行下面代码可以自动生成models模型文件
\- python manage.py inspectdb > app/models.py 　　　　

### 31.使用ORM和原生SQL的优缺点？

```js
SQL：
# 优点：
执行速度快
# 缺点：
编写复杂，开发效率不高
----------------------------------------------------
ORM：
# 优点：
让用户不再写SQL语句，提高开发效率
可以很方便地引入数据缓存之类的附加功能
# 缺点：
在处理多表联查、where条件复杂查询时，ORM的语法会变得复杂。
没有原生SQL速度快
```

### 32.简述MVC和MTV

MVC：model、view(显示)、controller(视图)
MTV：model、tempalte、view
二者本质没有区别。

### 33.Django的contenttype组件的作用？

　contenttypes 是Django内置的一个应用，可以追踪项目中所有app和model的对应关系，并记录在ContentType表中。可以使用他再加上表中的两个字段,实现一张表和N张表动态创建FK关系。
\- 字段：表名称
\- 字段：数据行ID

　　每当我们创建了新的model并执行数据库迁移后，ContentType表中就会自动新增一条记录。比如我在应用app01的models.py中创建表`class Electrics(models.Model): pass`。从数据库查看ContentType表，显示如下：

| id   | app_label              | model       |
| ---- | ---------------------- | ----------- |
| …    | admin, auth等内置应用… | …           |
| 5    | contenttypes           | contenttype |
| 6    | app01                  | electrics   |

### 34.谈谈你对Restfull规范的认识？

```js
Restful其实就是一套编写接口的'风格规范'，规定如何编写以及如何设置返回值、状态码等信息。
最显著的特点：
# 用Restful: 
    给用户一个url，再根据不同的method在后端做不同的处理
    比如：post创建数据、get获取数据、put和patch修改数据、delete删除数据。
# 不用Restful: 
    给调用者很多url，每个url代表一个功能，比如：add_user/delte_user/edit_user/
# 当然，还有其他的，比如：
    '版本' 来控制让程序有多个版本共存的情况，版本可以放在 url、请求头（accept/自定义）、GET参数
    '状态码' 200/300/400/500
    'url中尽量使用名词' restful也可以称为“面向资源编程”
    'api标示'
        api.luffycity.com
        www.luffycity.com/api/
```

### 接口的幂等性是什么意思？

```js
第一次访问一个接口后，再对该接口进行N次相同的访问时，对资源不造影响，就认为接口具有幂等性。
  GET，  #第一次获取结果、第二次也是获取结果对资源都不会造成影响，幂等。
  POST， #第一次新增数据，第二次也会再次新增，非幂等。
  PUT，  #第一次更新数据，第二次不会再次更新，幂等。
  PATCH，#第一次更新数据，第二次不会再次更新，非幂等。
  DELTE，#第一次删除数据，第二次不在再删除，幂等。
```

### 什么是RPC？

'远程过程调用协议'。
是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。
进化的顺序: 现有的RPC,然后有的RESTful规范

### Http和Https的区别？

```js
#Http: 80端口
#https: 443端口
#http信息是明文传输，https则是具有安全性的ssl加密传输协议。
#- 自定义证书 
    - 服务端：创建一对证书
    - 客户端：必须携带证书
#- 购买证书
    - 服务端： 创建一对证书，。。。。
    - 客户端： 去机构获取证书，数据加密后发给咱们的服务单
    - 证书机构:公钥给改机构
```

### 为什么要使用Django Rest Framework框架？

```js
# 在编写接口时可以不使用django rest framework框。
# 不使用：也可以做，可以用django的CBV来实现，开发者编写的代码会更多一些。
# 使用：内部帮助我们提供了很多方便的组件，我们通过配置就可以完成相应操作，如：
    '序列化'可以做用户请求数据校验+queryset对象的序列化称为json
    '解析器'获取用户请求数据request.data，会自动根据content-type请求头的不能对数据进行解析
    '分页'将从数据库获取到的数据在页面进行分页显示
    #还有其他组件：'认证'、'权限'、'访问频率控制'
```

### Django Rest Framework框架中都有那些组件？

```js
#- 路由，自动帮助开发者快速为一个视图创建4个url
        www.oldboyedu.com/api/v1/student/$
        www.oldboyedu.com/api/v1/student(?P<format>\w+)$
        www.oldboyedu.com/api/v1/student/(?P<pk>\d+)/$
        www.oldboyedu.com/api/v1/student/(?P<pk>\d+)(?P<format>\w+)$
#- 版本处理
    - 问题：版本都可以放在那里？
            - url
            - GET 
            - 请求头 
#- 认证 
    - 问题：认证流程？
#- 权限 
    - 权限是否可以放在中间件中？以及为什么？
#- 访问频率的控制
    匿名用户可以真正的防止？无法做到真正的访问频率控制，只能把小白拒之门外。
    如果要封IP，使用防火墙来做。
    登录用户可以通过用户名作为唯一标示进行控制，如果有人注册很多账号，则无法防止。
#- 视图
#- 解析器 ，根据Content-Type请求头对请求体中的数据格式进行处理。request.data 
#- 分页
#- 序列化
    - 序列化
        - source
        - 定义方法
    - 请求数据格式校验
#- 渲染器
```

### Django Rest Framework框架中的视图都可以继承哪些类?

```js
a. 继承APIView（最原始）但定制性比较强
    这个类属于rest framework中的顶层类，内部帮助我们实现了只是基本功能：认证、权限、频率控制，
但凡是数据库、分页等操作都需要手动去完成，比较原始。
    class GenericAPIView(APIView)
    def post(...):
          pass 

b.继承GenericViewSet（ViewSetMixin，generics.GenericAPIView）
　　首先他的路由就发生变化
    如果继承它之后，路由中的as_view需要填写对应关系
　　在内部也帮助我们提供了一些方便的方法：
　　get_queryset
　　get_object
　　get_serializer
　　get_serializer_class
　　get_serializer_context
　　filter_queryset
注意：要设置queryset字段，否则会抛出断言的异常。

代码
只提供增加功能 只继承GenericViewSet

class TestView(GenericViewSet):
　　serialazer_class = xxx
　　def creat(self,*args,**kwargs):
　　　　pass  # 获取数据并对数据

c. 继承  modelviewset  --> 快速快发
　　　　-ModelViewSet(增删改查全有+数据库操作)
　　　　-mixins.CreateModelMixin（只有增）,GenericViewSet
　　　　-mixins.CreateModelMixin,DestroyModelMixin,GenericViewSet
　　对数据库和分页等操作不用我们在编写，只需要继承相关类即可。
　　
示例：只提供增加功能
class TestView(mixins.CreateModelMixin,GenericViewSet):
　　　　serializer_class = XXXXXXX
*** 
　　modelviewset --> 快速开发，复杂点的genericview、apiview
```

![image-20201103160136047](D:\code\python\python_interview\python\image-20201103160136047.png)

#### 简述 Django Rest Framework框架的认证流程

```js
- 如何编写？写类并实现authenticators
　　请求进来认证需要编写一个类，类里面有一个authenticators方法，我们可以自定义这个方法，可以定制3类返回值。
　　成功返回元组，返回none为匿名用户，抛出异常为认证失败。

源码流程：请求进来先走dispatch方法，然后封装的request对象会执行user方法，由user触发authenticators认证流程
- 方法中可以定义三种返回值：
    - （user,auth），认证成功
    - None , 匿名用户
    - 异常 ，认证失败
- 流程：
    - dispatch 
    - 再去request中进行认证处理
```

#### Django Rest Framework如何实现的用户访问频率控制？

```js
# 对匿名用户，根据用户IP或代理IP作为标识进行记录，为每个用户在redis中建一个列表
    {
        throttle_10.1.1.1:[1526868876.497521, 152686885.497521, ...]，
        throttle_10.1.1.2:[1526868876.497521, 152686885.497521, ...]，
        throttle_10.1.1.3:[1526868876.497521, 152686885.497521, ...]，
    } 
 每个用户再来访问时，先去记录中剔除过期记录，再根据列表的长度判断是否可以继续访问。
 '如何封IP'：在防火墙中进行设置
--------------------------------------------------------------------------
# 对注册用户，根据用户名或邮箱进行判断。
    {
        throttle_xxxx1:[1526868876.497521, 152686885.497521, ...]，
        throttle_xxxx2:[1526868876.497521, 152686885.497521, ...]，
        throttle_xxxx3:[1526868876.497521, 152686885.497521, ...]，
    }
每个用户再来访问时，先去记录中剔除过期记录，再根据列表的长度判断是否可以继续访问。
如1分钟：40次，列表长度限制在40，超过40则不可访问
```