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

# Contenct
<hr>

* [Initialization and Deletion](#InitializationDeletion)
    
    * [__init__](#__init__)
    * [__del__](#__del__)
* [Representation](#Representation)

    * [__str__](#__str__)
    * [__repr__](#__repr__)

* [Attributes](#Attributes)

    * [__setattr__](#__setattr__)
    * [__getattr__](#__getattr__)
    * [__delattr__](#__delattr__)
* [Operator Overloading](#OperatorOverloading)

    * [Binary Operator](#BinaryOperator)
    * [Unary Operators](#UnaryOperators)
    * [Item Operators](#ItemOperators)
    * [Other Overrides](#OtherOverrides)

### <a name='InitializationDeletion'>Initialization and Deletion</a>
    
######  <a name="__init__">__init__</a>

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
diff: 使用__init__ 和  不用__init__
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

######  <a name="__del__">__del__</a>

作用：当instance已无用可以被销毁时需要调用__del__()。

###  <a name="Representation">Representation</a>

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

######  <a name="__str__">__str__</a>

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

######  <a name="__repr__">__repr__</a>

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

###  <a name="Attributes">Attributes</a>

Attribute Override Functions

<table id="mytbid">
  <tr>
    <th width="10%"> Function </th> <th width="25%"> Indirect form </th> <th width="20%"> Direct Form </th> <th  width="35%"> Use </th>
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

######  <a name="__setattr__">__setattr__</a>

{% highlight python %}
class Unchangable:
    def __setattr__(self, name, value):
        print "Nice try"
u = Unchangable()
u.x = 9
u.x
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__setattr__.jpg)

######  <a name="__getattr__">__getattr__</a>

{% highlight python %}
class HiddenMembers:
    def __getattr__(self, name):
        return "You don't get to see " + name
h = HiddenMembers()
h.anything
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/SpecialMethods__getattr__.jpg)

######  <a name="__delattr__">__delattr__</a>

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

###  <a name="OperatorOverloading">Operator Overloading</a>

######  <a name="BinaryOperator">Binary Operator</a>

Binary Operator Override Functions

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

{% highlight python %}
class FakeNumber:
    n = 5
    def __add__(A,B):
        return A.n + B.n

c = FakeNumber()
d = FakeNumber()
d.n = 7
c + d
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/binary_operator1.jpg)
To override the augmented assignment operators, merely add 'i' in front of the normal binary operator, 
i.e. for '+=' use '__iadd__' instead of '__add__'. 

{% highlight python %}
c = FakeNumber()
c += d
c
{% endhighlight %}
This will work as expected, with "__add__" being called for "+=" and so on.“
![Smithsonian Image]({{ site.url }}/images/binary_operator2.jpg)

######  <a name="UnaryOperators"> Unary Operators </a>


{% highlight python %}
FakeNumber.__neg__ = lambda A : A.n + 6
-d
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/binary_operator3.jpg)

######  <a name="ItemOperators"> Item Operators </a>

<table id="mytbid">
  <tr>
    <th width="15%"> Function </th> <th width="15%"> Operator </th> 
  </tr>
  <tr>
    <td> __getitem__ </td> <td>  C[i] </td>
  </tr>
  <tr class="alt">
    <td> __setitem__ </td> <td> C[i] = v </td>
  </tr>
  <tr>
    <td> __delitem__ </td> <td>  del C[i] </td>
  </tr>
  <tr class="alt">
    <td> __getslice__ </td> <td> C[s:e] </td>
  </tr>
  <tr>
    <td> __setslice__ </td> <td> C[s:e] = v </td>
  </tr>
  <tr class="alt">
    <td> __delslice__ </td> <td> del C[s:e] </td>
  </tr>
</table>

{% highlight python %}
class FakeList:
    def __getitem__(self,index):
        return index * 2

f = FakeList()
f['a']
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/item_operator1.jpg)

{% highlight python %}
class FakeList:
    def __setitem__(self,index,value):
        self.string = index + " is now " + value

f = FakeList()
f['a'] = 'gone'
f.string
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/item_operator2.jpg)

{% highlight python %}
class FakeList:
    def __getslice__(self,start,end):
        return str(start) + " to " + str(end)

f = FakeList()
f[1:4]
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/item_operator3.jpg)

{% highlight python %}
f[:]
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/item_operator4.jpg)

######  <a name="OtherOverrides"> Other Overrides </a>

Other Override Functions

<table id="mytbid">
  <tr>
    <th> Function </th> <th> Operator </th> 
  </tr>
  <tr>
    <td> __cmp__ </td> <td> cmp(x, y) </td> 
  </tr>
  <tr class="alt">
    <td> __hash__ </td> <td> hash(x) </td> 
  </tr>
  <tr>
    <td> __nonzero__ </td> <td> bool(x) </td> 
  </tr>
  <tr class="alt">
    <td> __call__ </td> <td> f(x) </td> 
  </tr>
  <tr>
    <td> __iter__ </td> <td> iter(x) </td> 
  </tr>
  <tr class="alt">
    <td> __reversed__ </td> <td> reversed(x) (2.6+)</td> 
  </tr>
  <tr>
    <td> __divmod__ </td> <td> divmod(x, y) </td> 
  </tr>
  <tr class="alt">
    <td> __int__ </td> <td> int(x)</td> 
  </tr>
  <tr>
    <td> __long__ </td> <td> long(x) </td> 
  </tr>
  <tr class="alt">
    <td> __float__ </td> <td> float(x)</td> 
  </tr>
  <tr>
    <td> __complex__ </td> <td> complex(x) </td> 
  </tr>
  <tr class="alt">
    <td> __hex__ </td> <td> hex(x) </td> 
  </tr>
  <tr>
    <td> __oct__ </td> <td> oct(x) </td> 
  </tr>
  <tr class="alt">
    <td> __index__ </td> <td> </td> 
  </tr>
  <tr>
    <td> __copy__ </td> <td> copy.copy(x) </td> 
  </tr>
  <tr class="alt">
    <td> __deepcopy__ </td> <td> copy.deepcopy(x) </td> 
  </tr>
  <tr>
    <td> __sizeof__ </td> <td> sys.getsizeof(x) (2.6+)</td> 
  </tr>
  <tr class="alt">
    <td> __trunc__ </td> <td> math.trunc(x) (2.6+) </td> 
  </tr>
  <tr>
    <td> __format__ </td> <td> format(x, ...) (2.6+) </td> 
  </tr>
</table>
