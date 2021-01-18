## java

### ==和equals的区别

== 比较的是值

比较基本的数据类型，比较的是数值

比较引用类型：比较引用指向的值（地址）

equals

**默认比较也是地址，因为这个方法的最初定义在Object上，默认的实现就是比较地址**

自定义的类，如果需要比较的是内容，那么就要学String，重写equals方法

代码案例：测试以下的每道题，你是否能够正确得到答案？

```java
String s1 = new String("zs");
String s2 = new String("zs");
System.out.println(s1 == s2);
String s3 = "zs";
String s4 = "zs";
System.out.println(s3 == s4);
System.out.println(s3 == s1);
String s5 = "zszs";
String s6 = s3+s4;
System.out.println(s5 == s6);
final String s7 = "zs";
final String s8 = "zs";
String s9 = s7+s8;
System.out.println(s5 == s9);
final String s10 = s3+s4;
System.out.println(s5 == s10);
```

### final的作用

final修饰类，表示类不可变，不可继承

比如，String，不可变性

final修饰方法，表示该方法不可重写

比如模板方法，可以固定我们的算法

final修饰变量，这个变量就是常量

注意：

修饰的是基本数据类型，这个值本身不能修改

**修饰的是引用类型，引用的指向不能修改**

**比如下面的代码是可以的**

```java
final Student student = new Student(1,"Andy");
student.setAge(18);//注意，这个是可以的！
```



### String，StringBuffer，StringBuilder区别

String 跟其他两个类的区别是

> String是final类型，每次声明的都是不可变的对象，
> 所以每次操作都会产生新的String对象，然后将指针指向新的String对象。

StringBuffer，StringBuilder都是在原有对象上进行操作

> 所以，如果需要经常改变字符串内容，则建议采用这两者。

StringBuffer vs StringBuilder

> 前者是线程安全的，后者是线程不安全的。
> 线程不安全性能更高，所以在开发中，优先采用StringBuilder.
> StringBuilder > StringBuffer > String

### 接口和抽象类的区别

这个问题，要分JDK版本来区分回答：

- JDK1.8之前：

- - 语法：

    - - 抽象类：方法可以有抽象的，也可以有非抽象, 有构造器
        - 接口：方法都是抽象，属性都是常量，默认有public static final修饰

    - 设计：

    - - 抽象类：同一类事物的抽取，比如针对Dao层操作的封装，如，BaseDao，BaseServiceImpl
        - 接口：通常更像是一种标准的制定，定制系统之间对接的标准
        - 例子：
        - 1，单体项目，分层开发，interface作为各层之间的纽带，在controller中注入IUserService，在Service注入IUserDao
        - 2，分布式项目，面向服务的开发，抽取服务service，这个时候，就会产生服务的提供者和服务的消费者两个角色
        - 这两个角色之间的纽带，依然是接口

- JDK1.8之后：

- - 接口里面可以有实现的方法，注意要在方法的声明上加上default或者static

最后区分几个概念：

- 多继承，多重继承，多实现

- - 多重继承：A->B->C（爷孙三代的关系）
    - 多实现：Person implements IRunable,IEatable（符合多项国际化标准）
    - 多继承：接口可以多继承，类只支持单继承