---
layout: post
title: "Python: Class Special Methods"
modified:
categories: python
excerpt:
tags: []
image:
  feature:
date: 2014-12-12T00:07:18+08:00
---

# Special Methods
<hr>

#### Initialization: __init__()
作用：constructing an instance
初始化instance时，在返回instance之前，__init__()会被调用。
{% highlight python %}
class A:
    def __init__(self):
        print 'A.__init__()'
a = A()
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__init__.jpg)

__init__()可以带arguments, 需要将arguments传给cls，以便
cls能够创建实例。
{% highlight python %}
class Foo:
    def __init__(self, printme):
        print printme
foo = Foo('Hi!')
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__init__with_argument.jpg)

diff: 使用__init__() &  不用__init__()
{% highlight python %}
class Foo:
    def __init__(self, x):
        print x
foo = Foo('Hi!')
class Foo2:
    def setx(self, x):
        print x
f = Foo2()
Foo2.setx(f, 'Hi!')
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/diff_with_or_without__init__.jpg)

#### Deletion: __del__()
作用：当instance已无用可以被销毁时需要调用__del__()。

#### Representation:

<table id="mytbid">
  <tr>
    <th width="15%"> Function </th> <th width="15%"> Operator </th> <th> Use</th>
  </tr>
  <tr>
    <td> __str__ </td> <td>  str(A) </td> <td>Converting an object to a string, return a formatted version of the objects content. This will NOT usually be something that can be executed.</td>
  </tr>
  <tr class="alt">
    <td> __repr__ </td> <td> repr(A) </td> <td>Much like __str__(), if __str__ isn't present but this one is, this function's output is used instead fo printing.__repr__is used to return a representation of the object in string form. In general, it can be executed to get back the original object.</td>
  </tr>
  <tr>
    <td> __unicode__ </td> <td>  unicode(x) <br> (2.x only)</td> <td>unicode version of __str__. </td>
  </tr>
</table>

{% highlight python %}
class Bar:
    def __init__(self, iamthis):
        self.iamthis = iamthis
    def __str__(self):
        return self.iamthis
bar = Bar('apple')
print bar
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__str__.jpg)

{% highlight python %}
class Bar:
    def __init__ (self, iamthis):
        self.iamthis = iamthis 
    def __repr__(self):
        return "Bar('%s')" % self.iamthis 
bar = Bar('apple')
bar
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__repr__.jpg)

#### Attributes

<table id="mytbid">
  <tr>
    <th width="15%"> Function </th> <th width="15%"> Indirect form </th> <th width="15%"> Direct Form </th> <th> Use </th>
  </tr>
  <tr>
    <td> __setattr__ </td> <td>setattr(C, Prop, Value) </td> <td> C.Prop = Value </td> <td>in charge of setting attributes of a class. Each class comes with a default __setattr__ which simply sets the value of the variable, but we can override it.</td>
  </tr>
  <tr class="alt">
    <td> __getattr__ </td> <td>getattr(C, Prop) </td> <td> C.Prop </td> <td> Similar to __setattr__, except it's called when we try to access a class member, and the default simply return the value.</td>
  </tr>
  <tr>
    <td> __delattr__ </td> <td>delattr(C, Prop) </td><td> del C.Prop</td> <td>iit's called to delete an attribute.</td>
  </tr>
</table>

{% highlight python %}
class Unchangable:
    def __setattr__(self, name, value):
        print "Nice try"
u = Unchangable()
u.x = 9
u.x
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__setattr__.jpg)

{% highlight python %}
class HiddenMembers:
    def __getattr__(self, name):
        return "You don't get to see " + name
h = HiddenMembers()
h.anything
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__getattr__.jpg)

{% highlight python %}
class Permanent:
    def __delattr__(self, name):
        print name, "cannot be deleted"
p = Permanent()
p.x = 9
del p.x
p.x
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__delattr__.jpg)

#### Binary Operator

<table id="mytbid">
  <tr>
    <th width="15%"> Function </th> <th width="15%"> Operator </th> 
  </tr>
  <tr>
    <td> __add__ </td> <td>  A + B </td>
  </tr>
  <tr class="alt">
    <td> __sub__ </td> <td> A - B </td>
  </tr>
  <tr>
    <td> __mul__ </td> <td>  A * B </td>
  </tr>
  <tr class="alt">
    <td> __truediv__ </td> <td>  A / B </td>
  </tr>
  <tr>
    <td> __floordiv__ </td> <td> A // B </td>
  </tr>
  <tr class="alt">
    <td> __mod__ </td> <td>  A % B </td>
  </tr>
  <tr>
    <td> __pow__ </td> <td>  A ** B </td>
  </tr>
  <tr class="alt">
    <td> __and__ </td> <td>  A & B </td>
  </tr>
  <tr>
    <td> __or__ </td> <td>  A | B </td>
  </tr>
  <tr class="alt">
    <td> __xor__ </td> <td>  A ^ B </td>
  </tr>
  <tr>
    <td> __eq__ </td> <td>  A == B </td>
  </tr>
  <tr class="alt">
    <td> __ne__ </td> <td>  A != B </td>
  </tr>
  <tr>
    <td> __gt__ </td> <td>  A > B </td>
  </tr>
  <tr class="alt">
    <td> __lt__ </td>  <td> A < B </td>
  </tr>
  <tr>
    <td> __ge__ </td> <td>  A >= B </td>
  </tr>
  <tr class="alt">
    <td> __le__ </td> <td>  A <= B </td>
  </tr>
  <tr>
    <td> __lshift__ </td> <td>  A << B </td>
  </tr>
  <tr class="alt">
    <td> __rshift__ </td> <td>  A >> B </td>
  </tr>
  <tr>
    <td> __contains__ </td> <td> A in B or A not in B </td>
  </tr>
</table>
