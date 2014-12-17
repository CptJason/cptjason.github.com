---
layout: post
title: "python: __future__"
modified:
categories: python
excerpt:
tags: []
image:
  feature:
comments: true
share: true
date: 2014-11-20T14:35:18+08:00
---
python将来版本将会用到的特性，尤其是将来python版本3的特性如图：
{% highlight python %}
import __future__
__future__.
__future__.<tab>
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/future.jpg)

可以导入使用的功能有哪些：
{% highlight python %}
import __future__
print __future__.all_feature_names
{% endhighlight %}
![Smithsonian Image]({{ site.url }}/images/future_importable.jpg)

# 下面详细介绍一下以上几个要点

* division: 改变‘/’用法，将不再取整
* print_function: 将print变成一个函数（之前的用法将不能用）
* unicode_literals: 对字符串使用unicode字符
* nested_scopes: 改变名空间的搜索过程
* generators:使用生成器.能够产生能保存当前状态的函数
* division:精确的除法
* absolute_import:包含绝对路径.方便include
* with_statement:安全的打开文件

## division

测试代码
{% highlight python %}
1/5
1//5
1.0/5.0
1.0//5.0
from __future__ import division
1/5
1//5
1.0/5.0
1.0//5.0
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/division.jpg)

## print_function

测试代码
{% highlight python %}
print "test print_function"
from __future__ import print_function
print('test print_function')
print 'test print_function'
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/print_function.jpg)

## unicode_literals

测试代码
{% highlight python %}
print('中文')
from __future__ import unicode_literals
print('中文')
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/unicode_literals.jpg)

## nested_scopes

当一行代码查找一个变量的值，python会按照如下顺序在可用namespace里查找

1. local namespaces: 当前function或者class method。
2. global namespaces: 当前module.
3. built-bin namespaces: All Modules.

如果python在以上namespaces无法找到x 变量，它将放弃并raises错误NameError,
并带有错误消息：There is no variable named 'x'，如图
![Smithsonian Image]({{ site.url }}/images/NameError.jpg)

nested_scopes的作用：<a href="https://www.python.org/dev/peps/pep-0227/" class="btn">引用pep227原话</a><br>
" The addition of nested scopes allows
resolution of unbound local names in enclosing functions' namespaces.
"
大家看完以下代码就明白了。

{% highlight python %}
def make_adder(base):
    def adder(x):
        print 'x', x
        print 'base', base
        print '--adder--'
        return base + x
    print base
    print '--make_adder--'
    return adder

add5 = make_adder(5)
add5(6)
add5(11)
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/nested_scopes_1.jpg)

测试代码
{% highlight python %}
def make_fact():
    def fact(n):
        if n == 1:
            print '--fact--'
            return 1L
        else:
            print '--fact--'
            return n * fact(n - 1)
    print '--make_fact--'
    return fact

fact = make_fact()
fact(7)  
fact(8)
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/nested_scopes_2.jpg)

测试代码
{% highlight python %}
def make_wrapper(obj):
    class Wrapper:
        def __getattr__(self, attr):
            if attr[0] != '_':
                return getattr(obj, attr)
            else:
                raise AttributeError, attr
    return Wrapper()

class Test:
    public = 2
    _private = 3

w = make_wrapper(Test())
w.public
w._private 
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/nested_scopes_3.jpg)

在python2.2里面,
当你引用一个变量在一个nested funciton或者lambda function, python首先会在
当前的（nested 或者lambda）function里面查找。

python3.x测试代码
{% highlight python %}
def outer():
    string = ""
    def inner():
        nonlocal string
        string = "String was changed by a nested function!"
    inner()
    return string
outer()
a = outer()
print a
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/nested_scopes_py3.jpg)


python2.x测试代码
{% highlight python %}
def outer():
    string = [""]
    def inner():
        string[0] = "String was changed by a nested function!"
    inner()
    return string[0]
outer()
a = outer()
print a
{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/nested_scopes_py2.jpg)

## generators
   
generators的作用：<a href="https://www.python.org/dev/peps/pep-0255/" class="btn">引用pep255原话</a><br>
"
This PEP introduces the concept of generators to Python, as well
    as a new statement used in conjunction with them, the "yield"
    statement.
"

测试代码
{% highlight python %}
def fib():
    a, b = 0, 1
    while 1:
        yield b
        a, b = b, a+b

a = fib()

for i in a:
    if i < 10:
        print i
    else:
        break

a = fib()
        
for i in a:
    if i < 10:
        print i
    else:
        break

{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/generators1.jpg)

测试代码
{% highlight python %}
def fib():
    a, b = 0, 1
    while 1:
        yield b
        a, b = b, a+b

a = fib()

for i in a:
    if i < 10:
        print i
    else:
        break
        
for i in a:
    if i < 100:
        print i
    else:
        break

{% endhighlight %}

测试截图
![Smithsonian Image]({{ site.url }}/images/generators2.jpg)

## absolute_import

absolute_import的作用：<a href="https://www.python.org/dev/peps/pep-0328/" class="btn">引用pep328原话</a><br>
{% highlight css %}
The import statement has two problems:
1. Long import statements can be difficult to write, requiring various contortions to fit Pythonic style guidelines.
2. Imports can be ambiguous in the face of packages; within a package, it's not clear whether import foo refers to a module within the package or some module outside the package. (More precisely, a local module or package can shadow another hanging directly off sys.path .)
For the first problem, it is proposed that parentheses be permitted to enclose multiple names, thus allowing Python's standard mechanisms for multi-line values to apply. For the second problem, it is proposed that all import statements be absolute by default (searching sys.path only) with special syntax (leading dots) for accessing package-relative imports.
{% endhighlight %}

When use absolute_import: 

From pep328:
{% highlight css %}
Once absolute imports are the default, import string will always find the standard library’s version. 
It’s suggested that users should begin using absolute imports as much as possible, 
so it’s preferable to begin writing from pkg import string in your code.
{% endhighlight %}

测试代码
![Smithsonian Image]({{ site.url }}/images/absolute_import1.jpg)

测试截图
![Smithsonian Image]({{ site.url }}/images/absolute_import2.jpg)


## with_statement 

with_statement的作用：<a href="https://www.python.org/dev/peps/pep-0343/" class="btn">引用pep343原话</a><br>
"
This PEP adds a new statement "with" to the Python language to make
it possible to factor out standard uses of try/finally statements.

In this PEP, context managers provide __enter__() and __exit__()
methods that are invoked on entry to and exit from the body of the
with statement.
"

测试代码
{% highlight python%}
with open("/tmp/data.txt") as f:
    data = f.read()
    #do something with data
    print data
{% endhighlight %}


测试截图
![Smithsonian Image]({{ site.url }}/images/with_statement.jpg)

