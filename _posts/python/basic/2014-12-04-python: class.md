---
layout: post
title: "python: Class"
modified:
categories: python
excerpt:
tags: []
image:
  feature:
date: 2014-12-04T19:41:00+08:00
---
# Defining a Class
<hr>
{% highlight python %}
class ClassName:
    "Here is an explanation about your class"
    pass
{% endhighlight %}
以上命名是一种规范<br>
use CamelCase for classes <br>
and lower_case_with_underscores for functions and methods.<br> 

# Instance Construction
<hr>
{% highlight python %}
class Foo:
    "Foo is our new toy."
    pass
{% endhighlight %}
以上class为callable对象, 当调用它时Foo()可以创建实例。

{% highlight python %}
f = Foo()
{% endhighlight %}
以上创建了Foo实例，并创建了对该实例的引用。

# Class Members
<hr>
使用语法<class instance>.<member> 可以访问一个类实例的成员。
这当然也是可能的: 使用语法<class name>.<member> 可以访问一个类实例的成员。
#### methods
{% highlight python %}
class Foo:
    def setx(self,x):  
        self.x = x
    def bar(self):
        print self.x
{% endhighlight %}
在一个class里面，一个method就是一个function。
注意：
    这里的method必须至少有一个参数（即第一个参数）：
    {% highlight css %}
    类实例本身
    （the instance of the class on which the function is invoked.）
    {% endhighlight %}

#### Invoking Methods
调用method很像调用function, 不同的是传递实例本身作为它的第一个参数。
{% highlight python %}
f = Foo()
f.setx(5)
f.bar()
{% endhighlight%}

对于任意对象来说可用使用如下方法进行method调用：
通过把它作为一个类属性而不是类实例, 举证
{% highlight python %}
Foo.setx(f, 6)
Foo.bar(f)
{% endhighlight%}
![Smithsonian Image]({{ site.url }}/images/methods.jpg)

#### Dynamic Class Structure
如上述，运行时可以改变python class成员，不像c或者java 仅仅只是他们的值。

我们甚至可以在运行了以上代码后，直接删除f.x。 
{% highlight python %}
del f.x
f.bar()
{% endhighlight%}
![Smithsonian Image]({{ site.url }}/images/del_f.x.jpg)
另外一个特效：
我们可以在程序执行时，改变class Foo的类定义。
以下代码，我们将创建Foo class的类成员y，然后我们创建一个Foo实例，它将会拥有改新成员。
{% highlight python %}
Foo.y = 10
g = Foo()
g.y
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/Foo.y_g.y.jpg)

#### viewing Class Dictionaries
它的核心是一个dictionary，通过"vars(ClassName)"，你就可以访问它。
{% highlight python %}
vars(g)
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/vars_g.jpg)
结果为空dictionary，我们刚刚看到g它由成员y。
###问题来了
为什么它没有在member dictionary里面？
如果你还记得，我们将y搞进definition of Foo class, 而不是g。
{% highlight python %}
vars(Foo)
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/vars_Foo.jpg)
你看上面就是all members of Foo class definition。
当python检查g.member时，首先检查g's vars dictionary for "members"，然后才是Foo。

如果我们为g创建一个新的成员, 它将会添加值g's dictionary，而不是Foo's。
{% highlight python %}
g.setx(5)
vars(g)
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/g_setx.jpg)
如果我们现在给g.y赋值，而不是给Foo.y。Foo.y将会还是10，而g.y却没有覆盖掉Foo.y。
{% highlight python %}
g.y = 9
vars(g)
vars(Foo)
{% endhighlight %}
非常确认，以下我们检测以下它的值
{% highlight python %}
g.y
Foo.y
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/g.y_Foo.y.jpg)
注意
    f.y将会被修改成10，因为python没有在vars(f)中找到y，所以它将会从vars(Foo)中取得y值。

你可能已经发现class Foo里面的方法出现在class dictionary，跟那些变量x，y在一起。
如果你还记得lambda形式，我们把functions看成variables.
这意味着
{% highlight python %}
我们可以在运行的时候将methods赋给class，就像我们赋值variables时。
{% endhighlight %}
如果我们这样做的话，还记得我们调用class instance.method，第一个传给method的参数
将会是the class instance itself.
Take a step back这里介绍一下<a href="{{ site.url }}/python/python: function/"><strong>function: </strong></a>

#### Changing Class Dictionaries

我们使用__dict__ member of the class，可以访问the members dictionary of a class。
{% highlight python %}
g.__dict__
{% endhighlight %}
如果我们从g.__dict__中进行add，remove，or change key-value pairs，这个和我们对
the members of g做同样的变更效果一样。
{% highlight python %}
g.__dict__['z'] = -4
g.z
{% endhighlight %}

# New Style Classes
<hr>
New style classes 是从python2.2开始引进的。new-style class 的父类最普遍就是object。
从底层上看，old-style class 与 new-style class最大不同就是their type：

* old-style 实例类型：instance
* new-style 实例类型：x.__class__

用户使用这些内置对象，使用python面向对象编程就更加轻松愉快了。
old-style class 已计划被废弃在python3。
所以建议大家使用new style class。（new-style class添加成员：properties和static methods 于Java类似。）

Old/classic Class
{% highlight python %}
class ClassicFoo:
    def __init__(self):
        pass
{% endhighlight %}


New Style Class
{% highlight python %}
class ClassicFoo(object):
    def __init__(self):
        pass
{% endhighlight %}
#### Properties
什么是Properties?
带有setter和getter methods的attributes。
{% highlight python %}
class SpamWithProperties(object):
    def __init__(object):
        self.__egg = "MyEgg"
    def get_egg(self):
        return self.__egg
    def set_egg(self, egg):
        self.__egg = egg
    egg = property(get_egg, set_egg)
sp = SpamWithProperties()
sp.egg
sp.egg = "Eggs With Spam"
sp.egg
{% endhighlight %}
自从python2.6以后，就产生了@property 修饰符decorator。
{% highlight python %}
class Properties(object):
    def __init__(self):
        self.__egg = "Myegg"
    @property
    def egg(self):
        return self.__egg
    @egg.setter
    def egg(self, egg):
        self.__egg = egg
{% endhighlight %}

#### static method
{% highlight python %}
class StaticSpam(object):
    def StaticNoSpam():
        print "You can't have have the spam, spam, eggs and spam without any spam... that's disgusting"
    NoSpam = Staticmethod(StaticNoSpam)
StaticSpam.NoSpam()
{% endhighlight %}

当然你可以使用修饰符@staticmethod
{% highlight python %}
class StaticSpam(object):
    @staticmethod
    def StaticNoSpam():
        print "You can't have have the spam, spam eggs and spam without any 
        spam...that's disgusting"
{% endhighlight %}
    
# Inheritance
<hr>
和其他语言一样，Python也一样提供继承机制。
继承是一种简单使用其他class扩展facilities，多类继承使用如下格式:
{% highlight python %}
class ClassName(superclass1, superclass2, superclass3,...):
    pass
{% endhighlight %}
这样subclass就拥有了superclasses的所有members。
如果superclass和subclass都定义好了method, subclass 中的method将会覆盖
superclass中的。
#### 问题来了
在以上情况下，我们如何使用superclass中定义的method，这样有必要把method
作为attribute来 调用，比如Foo.setx(f, 5)。
{% highlight python %}
class Foo:
    def bar(self):
        print "I'm doing Foo.bar"
    x = 10
class Bar(Foo):
    def bar(self):
        print "I'm doing Bar.bar()"
        Foo.bar(self)
    y = 9
g = Bar()
Bar.bar(g)
g.y
g.x
vars(g)
vars(Bar)
vars(Foo)
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/Inheritance.jpg)

# Special Methods
Take a step back这里介绍一下<a href="{{ site.url }}/python/python: class-special-methods/"><strong> 类特殊方法 </strong></a>
