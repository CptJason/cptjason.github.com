---
layout: post
title: "python: SingleInstance"
modified:
categories: python
excerpt:
tags: [python]
comments: true
share: true
image:
  feature:
date: 2014-11-13T22:01:19+08:00
---
在Gang of Four的23中设计模式中，单例是最常使用的模式，通过单例模式可以保证系统中的一个类只有一个实例
而且该实例易于被外界访问，从而方便对实例个数的控制并节约资源。
每当大家想要实现一个名为XxxManager的类时，往往意味着这是一个单例。
在游戏编程中尤是如此：
比如在一个名为World的单例管理着游戏中的所有资源，包括一个名为Sun的单例，它给这个世界带来了光亮。

单例如此常见，所以不少现代编程语言将其加到了语言特性中，如scala和falcon语言都把object定义成关键词，并用其
声明单例。
如在scala中，一个单例如下：
{% highlight python %}
object Singleton {
    def show = println("I am a singleton")
}
{% endhighlight%}

object定义了一个名为Singleton的单例，它满足单例的3个需求：

* 只能是一个实例
* 必须自行创建实例
* 必须自行向整个系统提供这个实例

对于第三点，在任何地方都可以通过调用Singleton.show()来验证。
在scala中，单例没有显示的初始化操作，但并不是所有语法层面支持单例模式的编程语言都如此，比如falcon就不一样。

{% highlight python %}
object object_name [from class1, class2 ... classN]
    property_1 = expression
    property_2 = expression
    property_N = expression
    [init_block]
    function method_1([parameter_list])
    end
    ...
    function method_N([parameter_list])
    end
end
{% endhighlight%}

上面是falcon语言的单例语法，[init block] 能够让程序员手动控制单例的初始化代码。

## 问题来了

但是与scala和falcon相比，动态语言python就没有那么方便了，
python缺乏声明私有构造函数的语法元素，实例又带有类型信息。
比如以下方法是不可行的：

测试代码
{% highlight python %}
class Singleton(object):
    pass

S = Singleton()
del S
another = S.__class__() 
print type(another)
{% endhighlight %}
测试截图
![Smithsonian Image]({{ site.url }}/images/singleInstance1.jpg)

## 问题来了

虽然把Singleton的类定义删除了，但仍然有办法通过已有实例的__class__属性生成一个新实例。
那我们如何通过python真正创建实例的方法__new__来解决这个问题。

{% highlight python %}
class _Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance
s1 = Singleton()
s2 = Singleton()
assert id(s1) == id(s2)
{% endhighlight %}
测试截图
![Smithsonian Image]({{ site.url }}/images/singleInstance2.jpg)

## 问题来了

基本可以保证“只能有一个实例”的要求了，但是并发情况下可能会发生意外。
如何通过锁来解决问题？

{% highlight python %}
import threading
class Singleton(object):
    objs = {}
    objs_locker = threading.Lock()
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls in cls.objs:
            return cls.objs[cls]
        cls.objs_locker.acquire()
        try:
            if cls in cls.objs: ## double check locking
                return cls.objs[cls]
            cls.objs[cls] = object.__new__(cls)
        finally:
            cls.objs_locker.release()

s1 = Singleton()
s2 = Singleton()
assert id(s1) == id(s2)
{% endhighlight %}

利用经典的双检查锁机制，确保并发情况下Singleton的正确实现。

## 问题来了

* 如果Singleton子类重载了__new__()方法，会覆盖或者干扰Singleton类中__new__()的执行，虽然这种情况出现的概率极小。
* 如果子类有__init__()方法, 那么每次实例化该Singleton的时候，__init__()都会被调用到，这显然是不应该的，__init__()只应该在创建实例的时候被调用一次。

可以解决

* 子类化Singleton的时候，请务必记得调用父类的__new__()方法
* 替换掉__init__()方法来确保它只调用一次

做了大量水面之下的工作，引起python社区的反思，发现模块采用的其实是天然的单例的实现方式：

* 所有的变量都会绑定到模块
* 模块只初始化一次
* import机制是线程安全的（保证在并发状态下只有一个实例）

当我们要实现一个游戏世界时，只需要简单创建World.py就可以了。

{% highlight python %}
# World.py
import sun
def run():
    while True:
        Sun.rise()
        Sun.set()
{% endhighlight %}

然后入口文件main.py里倒入，并调用run()函数即可
{% highlight python %}
# main.py
import World
World.run()
{% endhighlight %}

# 问题来了

Alex Martelli认为单例模式要求“实例的唯一性”本身是有问题的，实际更之得关注的是实例的状态:
只要所有的实例共享状态（狭义理解为属性）、行为（狭义理解为方法）一致就可以了
所以他提出Borg模式（C#中又称为Monostate模式）

{% highlight python %}
class Borg:
    __shared_state = {}
    def __init__(self):
        self.__dict__ = self.__shared_state 
# and whatever else you want in your class -- that's all!
{% endhighlight %}

通过Borg模式，可以创建任意数量的实例，但因为它们共享状态，从而保证了行为一致。
但是Borg模式仅适用于古典类（classic classes）
python 2.2版本以后的新式类（new-style classes）需要使用__getattr__和__setattr__方法来实现，代码略。
