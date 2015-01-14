---
layout: post
title: "python: del"
modified:
categories: python
excerpt:
tags: []
image:
  feature:
comments: true
share: true
date: 2014-11-17T10:45:02+08:00
---
有一种方式可以从一个列表中删除指定项，只要给定列表的索引而非它的值。
del 语句。
当然它也可以从列表中删除一个切片。

## 删除变量/变量成员/变量成员切片 

测试代码
{% highlight python %}
a = [-1, 1, 66.6, 333, 333, 1234.5]
del a[0]
print a
del a[2:4]
print a
del a
print a
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/del_variables.jpg)
在这之后引用a会报错，直到另一个值被赋予。

测试代码
{% highlight python %}
import sys,pprint
pprint.pprint(sys.path[0:4])
del sys
pprint.pprint(sys.path)
# diff
import pprint
from sys import path
pprint.pprint(path[0:4])
del path
pprint.pprint(path[0:4])
# reload module
import foo
while True:
    # Do some things.
    if is_changed(foo):
        foo = reload(foo)
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/del_module.jpg)

## 删除对象属性

有必要先了解一下对象属性

测试代码
{% highlight python %}
class C(object):
    def __init__(self):
        self._x = None
    def getx(self):
        return self._x
    def setx(self, value):
        self._x = value
    def delx(self):
        del self._x
    x = property(getx, setx, delx, "I'm the 'x' property.")
c = C()
c.x # invoke the getter
c.x = 5 # invoke the setter
del c.x  # similarly delattr(c, 'x') 
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/del_property.jpg)

测试代码
{% highlight python %}
class Parrot(object):
    def __init__(self):
        self._voltage = 100000
    @property # turns the voltage() method into a “getter” for a read-only attribute with the same name.
    def voltage(self):
        """Get the current voltage."""
        return self._voltage
{% endhighlight%}

测试截图
![Smithsonian Image]({{ site.url }}/images/del_property1.jpg)

测试代码
{% highlight python %}
class C(object):
    def __init__(self):
        self._x = None
    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x
    @x.setter
    def x(self, value):
        self._x = value
    @x.deleter
    def x(self):
        del self._x
c = C()
c.x # invoke the getter
c.x = 5 # invoke the setter
del c.x  # similarly delattr(c, 'x') 
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/del_property2.jpg)





