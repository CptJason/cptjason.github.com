---
layout: post
title: "python: MixinsMechanism"
modified:
categories: python
excerpt:
tags: []
comments: true
share: true
image:
  feature:
date: 2014-11-16T23:08:12+08:00
---
该机制的用途：动态修改对象（类或实例）的属性和方法。

在理解mixin之前，让我们复习一下模板方法模式。
所谓模板方法模式就是在一个方法中定义一个算法的骨架，并将一些步骤延迟到子类中。
模板方法可以使子类在不改变算法结构的情况写，重新定义算法中的某些步骤。（算法=行为？）

## 问题来了

模板方法模式在c++ 或其他语言中并无不妥，但是在python语言中，则颇有点画蛇添足的味道。
比如模板方法，需要先定义一个基类，而实现行为的某些步骤则必须在其子类中，在Python中并无必要。

{% highlight python %}
class People(object):
    def make_tea(self):
        teapot = self.get_teapot()
        teapot.put_in_tea()
        teapot.put_in_water()
        return teapot
{% endhighlight %}

在此例中，get_teapot()方法并不需要预先定义。
假设在上班是，使用的是简易茶壶，而在家里，使用的是功夫茶壶，那么可以这样编写代码：


{% highlight python %}
class OfficePeople(People):
    def get_teapot(self):
        return SimpleTeapot()
class HomePeople(People):
    def get_teapot(self):
        return KungfuTeapot()
{% endhighlight %}

这段代码工作很好，虽然看起来像模板方法，但基类并不需要预先声明抽象方法，甚至还带来调试代码的便利。
假定存在一个People的子类StreetPeople，用以描述“正走在街上的人”，作为“没有人会随身携带茶壶”的常识，
此类将不会实现get_teapot()方法，所以一调用make_tea()就会产生一个找不到get_teapot()方法的AttributeError。
由此程序员马上会想到“正走在街上的人” 边走边泡茶这样的需求是不合理，从而能够在更高层次上考虑业务的合理性，
在更接近本源的地方修正错误。

## 问题来了

老板（OfficePeople的一个实例）拥有巨大的办公室，购置了功夫茶具，他要在办公室喝功夫茶，怎么办？
答案两种：

* 从OfficePeople继承子类Boss, 重写它的get_teapot(), 使它返回功夫茶具
* 把get_teapot()方法提取出来，把它以多继承的方式做一次静态混入

以下很好解决老板在公司和功夫茶的需求。

{% highlight python %}
class UseSimpleTeapot(object):
    def get_teapot(self):
        return SimpleTeapot()
class UseKungfuTeapot(object):
    def get_teapot(self):
        return KungfuTeapot()
class OfficePeople(People, UseSimpleTeapot): pass
class HomePeople(People, UseKungfuTeapot): pass
class Boss(People, UseKungfuTeapot): pass
{% endhighlight %}

## 问题来了

但这样仍然没有把python的动态性表现出来：当新的需求出现时，需要更改类定义。
比如随着公司扩张，越来越多的人入职，OfficePeople的需求越来越多，开始出现有人不喝茶而是喝咖啡，也有人
既喜欢喝茶有喜欢喝咖啡，出现了喜欢在独立办公室抽雪茄的职业经理人......这些类越来越多，代码越发难已维护。
开始寄望生成动态实例：

{% highlight python %}
def simple_tea_people():
    people = People()
    people.__bases__ += (UseKungfuTeapot,)
    return people
def coffee_people():
    people = People()
    people.__bases__ += (UseCoffeepot,)
    return people
def tea_and_coffee_people():
    people = People()
    people.__bases__ += (UseSimpleTeapot, UseCoffeepot, )
def boss():
    people = People()
    people.__base__ += (KungfuTeapot, UseCoffeepot, )
    return people
{% endhighlight %}

这个代码能够运行的原理是，每一个类都有一个__bases__属性，它是一个元祖，用来存放所有的基类。
与其他静态语言不通，Python语言中的基类在运行中可以动态改变。
所以当我们向其中增加新的基类时，这个类就拥有了新的方法，也就是所谓的混入（mixin）。
这种动态性的好处在于代码获得了更丰富的扩展功能。
想象以下，你之前写好的代码并不需要个性，只要后期为它增加基类，就能增强功能（或替换原有行为）
这多么方便！
值得进一步探索的是，利用反射技术，甚至不需要修改代码。
假定我们在OA系统定义员工的时候，有一个特性选择页面，在里面可以勾选该员工的需求。
比如对于Boss，可以勾选功夫茶和咖啡，那么通过的代码可能如下：

{% highlight python%}
import mixins
def staff():
    people = People()
    bases = []
    for i in config.checked():
        bases.append(getattr(mixins, i))
    people.__bases__ += tuple(bases)
    return people
{% endhighlight%}

# 来两个例
## django中的一个例子

在python里面使用mixins非常有用的一个情况是：

* 当你从一个package导入一些相似的类，而需要去变更它们中的一个方法时

如何才能避免你逐个去修改，而只需修改一处method就行。

对于一个类，只要继承出来一个子类覆写其子类方法，就可以搞定。
但如果针对几个类，需要做同样的修改，那你就需要依赖mixin才可以简洁的实现该变更，然后应用至所有类。

以下是django的一个例子：
Django有几个generic view classes允许你们从数据库取出对象，供给前端html tempaltes渲染。

以下例子是用于展示一个指定对象的明细：

{% highlight python %}
from django.views.generic.detail import DetailView
{% endhighlight %}

该类有一个 get_object 方法，传入主键参数，即可从数据库里面取出对象。
我们需要修改该方法，来控制用户，只允许他们访问属于自己的对象。

首先实现一个独立的类Mixin仅实现method（我们即将重写它）

{% highlight python %}
class OwnedObjectMixin(object):
    def get_object(self, *args, **kwargs):
        obj = super(OwnedObjectMixin, self).get_object(*args, ** kwargs)
        if not obj.user == self.request.user:
            raise Http404
        return obj
{% endhighlight %}

然后我们创建一个子类，继承Mixin 和我们想要修改的类

{% highlight python %}
class ProtectedDetailView(OwnedObjectMixin, DetailView):
    pass
{% endhighlight %}

此类就使用OwnedObjectMixin类的方法get_object，覆写了类DetailView的get_object方法。
并且通过super调用父类DetailView方法get_object，所以有子类化DetailView的效果并覆写get_object方法。
当然我们可以应用同样的Mixin至其他类。

## SocketServer中的一个例子

{% highlight python %}
class ForkingUDPServer(ForkingMixIn, UDPServer): pass
class ForkingTCPServer(ForkingMixIn, TCPServer): pass

class ThreadingUDPServer(ThreadingMixIn, UDPServer): pass
class ThreadingTCPServer(ThreadingMixIn, TCPServer): pass
{% endhighlight %}

Reference:
https://www.python.org/download/releases/2.2/descrintro/#property
https://github.com/django/django/blob/master/django/forms/fields.py
