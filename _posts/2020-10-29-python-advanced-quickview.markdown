---
layout: post
title:  "Python Advanced Quickview"
date:   2020-10-29 00:25:35 +0530
categories: python
---
Grok the GIL: <https://opensource.com/article/17/4/grok-gil>

Understand multiprocessing, threading, cooperative multitasking: <https://sprksh.github.io/notes/2020/04/18/multiprocessing-multithreading-cooperative-multi-tasking-coroutines-gevent.html>

A list of useful python functions and implementations to make your life easier. Fast-track your way to becoming a better python developer.

**Dynamically Typed**: If the type variable are checked/inferred at runtime. Statically typed language do type checking at compile time rather than runtime. All non compiled language are dynamically typed (mostly?)
{% highlight python %}
def silly(a):
    if a > 0:
        print 'Hi'
    else:
        print 5 + '3'
{% endhighlight %}

# this will throw error at runtime in python. but the same kind of function would throw error ar compile time only in statically typed language like cpp

**Strongly Typed**: It refers to how strictly types are distinguished in a language.

in javascript, 2 + '2' = 4

in python, 2 + '2' >> TypeError: unsupported operand type(s) for +: 'int' and 'str'

javascript is weakly typed and python is strongly typed.

**Duck Typing**:

### Difference between Python2 & 3:

print is a function in 3 and a statement in 2

python 3 usesbanker's rounding, that is rounding to the nearest even.

Python 2 has ASCII str() types, separate unicode(), but no byte type. In Python 3, we have Unicode (utf-8) strings, and 2 byte classes: byte and bytearrays.

**Some Simple tricks:**

# you need to append in a list and return in on statement. Difficult because list.append(x) returns None

>>> l = [1,2,3]
>>> l.append(1) out: None
>>> l.append(1) or l out: [1,2,3,1]

# You can do the same with extend, update a dict etc.

# use setdefault to skip checking if something exists in a dict.

>>> a = {}
>>> a.setdefault('b', {}).setdefault('c', []).append(1)
>>> out: {'b': {'c': [1]}}

otherwise you would have to check if a['b']['c'] is none, set a['b']['c'] = []

Also check nested dict: https://stackoverflow.com/questions/635483/what-is-the-best-way-to-implement-nested-dictionaries

Use try > except > finally: to ensure running of the finally block in any case. Normally used to tear down the context:

{% highlight python %}
try:
    do_something()
except Exception:
    print("Exception")
finally:
    thank_god_in_any_case()

# Also, use try > except > else > finally: else is to be run only in case of no exception

# there is for-else thing in python:

>>> for i in []:
>>>     print(i)
>>> else:
>>>     print('chill')

# out: "chill"

ls1 = [1, 2, 3, 4, 5]
ls2 = [4, 5, 6, 7, 8]

elements_in_both = list( set(ls1) & set(ls2) )

>>> {1,2,3} | {3,4,6} # out: {1, 2, 3, 4, 6}
>>> {1,2,3} & {3,4,6} # out: {3}

list comprehension > [_ for _ in some_iterable]

list comprehension > {k: v for k, v in some_iterable}

generator comprehension > (_ for _ in some_iterable)
{% endhighlight %}

Datatypes:

set, dict, list, tuple, frozenset

Here, frozenset and tuple are immutable while set, list and dict are mutable. You cannot use mutables as keys to a dict as they are not hashable.

### decorator

wraps a function.

{% highlight python %}
def deco(f):
    def wrap(*args, **kwargs):
        print("some action before execution")
        r = f(*args, **kwargs)
        print("some action after execution")
        return r
    return wrap

@deco
def do_something(a, b):
    return a+b

# Here, do_something has been wrapped by the decorator already. Key thing is deco is returning definition of wrap (not the result of wrap, wrap()) function in place of decorated function. Now when you call do_something(), you are actually calling wrap() and that's why wrap() takes args and kwargs supplied to do_something().

# Simple demo:

def deco(f):
    print(f)
    def wrap(*args, **kwargs):
        return f(*args, **kwargs)
    print(wrap)
    return wrap

# Now during defining only (before calling), do_something will execute the two print statements.

@deco
def do_something(a, b):
    return a+b

<function adder at 0x10c3b16a8>
<function deco.<locals>.wrap at 0x10c3b1620>

# also, if you try:

>>> do_something.__name__
>>> out: 'wrap'

# Which makes it clear that do_something has already been replaced by wrap

# To solve this problem, we use functools.wraps

from functools import wraps

def deco(f):
    print(f)
    @wraps(f)
    def wrap(*args, **kwargs):
        print("uuuu")
        r = f(*args, **kwargs)
        print("lll")
        return r
    print(wrap)
    return wrap

<function adder at 0x111bdf510>
<function adder at 0x11199c6a8>

# Here you can see that the returned function is again adder although a different one, (the wrapped one)
{% endhighlight %}


Order of decorators: You need to decide the order of execution and based on that order of decorators. Obviously, the decorator at the top will wrap the decorator below it which is wrapping the actual function.

{% highlight python %}
# Let's have a look at decorator with args/kwargs

def decorator_factory(x=None):
    def decorator(f):
        def _wrapper(*args, **kwargs):
            return f(*args, **kwargs) + x
        return _wrapper
    return decorator

@decorator_factory(x=5)
def adder(a, b):
    return a+b

>>> adder(2,3)
>>> out: 10

>>> adder
>>> Out: <function __main__.decorator_factory.<locals>.decorator.<locals>._wrapper(*args, **kwargs)>

>>> decorator_factory
>>> Out: <function __main__.decorator_factory(x=None)>

# here decorator factory is actually creating a decorator within

# the decorator with arguments should return a function that will take a function and return another function. So it should really return a normal decorator.
{% endhighlight %}

<https://stackoverflow.com/a/5929165/4510252>

Hopefully that's all you need to know about decorators.

### Yield

It's a keyword you use to preemptively release the control. (In simpler terms, yield suspends the processing and remembers the state of the function. You call it next time, it starts from where it left.)

We use it in variety of scenarios. Basically, yield keyword gives us a generator which is an iterable and an iterator. Iterator is something you can iterate only once.

{% highlight python %}
# iterator and iterable are similar yet different things.

>>> l = [1,2,3,4]
>>> next(l)

TypeError: 'list' object is not an iterator

# although iter() method converts an iterable into an iterator.

>>> next(iter(l))
>>> out: 1
{% endhighlight %}

### generator

Generators are iterators.

When you are playing with lots of data, you cannot afford to have the entire thing in memory. So you use generator to get data when asked for and preserve the state of the function after yielding the control.

When you need generator:

1.  the iterable is too big and you need to access entries one by one
2.  the iterable is too big and you need to access nth entry
3.  lazy loading
4.  reading/processing streaming or large external dataset (csv/db)

{% highlight python %}
>>> type((a for a in range(5)))
out: generator
>>> type([a for a in range(5)])
out: list

# range object itself gives iterator on calling iter().

>>> a = iter(range(5))
>>> next(a) # out: 0

# creating an infinite generator:

def gen():
    x = 0
    while 1:
        yield x
        x += 1

>>> g = gen()
>>> next(g) # keeps giving higher values infinitely

# handling big external dataset:

def csv_reader(file_name):
    file = open(file_name)
    result = file.read().split("\n")
    return result

# this will result in memory error if the file is too big

def csv_reader(file_name):
    for row in open(file_name, "r"):
        yield row

# this will return a generator and won't cause memory issue
{% endhighlight %}

### Coroutines:

Coroutines can consume/receive data as opposed to generators that only produce/return data, prermptively.

More about it: <https://sprksh.github.io/notes/2020/04/18/multiprocessing-multithreading-cooperative-multi-tasking-coroutines-gevent.html>

### lambda functions:

>>> l = lambda x, y : x*y # is an anonymous/lambda function which takes 2 args x & y and returns x*y
>>> l(2,3)
>>> out: 6
>>> (lambda x: x*2)(2)
>>> out: 4

Basically, you define lambda and call it with the args it takes.

Also, lambda can only contain expressions, never a statement. So, return cannot be there in lambda as it is a statement. Function calls are expressions in python. So you can have

lambda x: [f1(x), f2(x), f3(x), x+1] # which will return a list. Within each function you can have statements.

### map-filter-reduce:

**map** will take a function and a list of args. And it will map all the args one by one to that function. In simple words, it will call the function with each arg. We will use lambda function instead of a normal function.

>>> list(map(lambda x: x*2, [1,3,4,5]))
>>> out: [2,6,8,10]

If we had not called list() on it, it would have returned a map object, which is basically a generator. In python2 it used to be a list. But generators are better, aren't they?

>>> a = map(lambda x: x*2, [1,3,4,5])
>>> type(a) # out: map
>>> next(a) # out: 2
>>> next(a) # out: 6

**filter** will take a filter condition function and a list of args

>>> list(filter(lambda n: n%2 == 0, [1,2,3,4,5,6]))
>>> out: [2,4,6]
>>> list(filter(lambda n: n%2, [1,2,3,4,5,6]))
>>> out: [1,3,5]

Like map, filter also returns a generator.

**reduce** applies a function of 2 arguments cumulatively to the elements of an iterable.

{% highlight python %}
from functool import reduce
result = reduce(function, iterable, initial_value)

>>> reduce(lambda x, y: x+y, [1,2,3,4], 0)
>>> out: 10

# Good way to use reduce, call the first variable agg (for aggregated)

>>> reduce(lambda agg, x: f'{agg} | {x}', ['cat', 'dog', 'cow'])
>>> out: 'cat | dog | cow'
{% endhighlight %}

What happens is: to the lambda function, 0,1 is passed which returns 1 and then in the next step, 1,2 are passed and so on.

and if you don't pass the initialization arg, that is 0, in the first step itself, 1,2 are passed to the lambda function.

{% highlight python %}
# A complicated example doing a simple thing.

reduce(lambda x, y: print(x) or (x.extend([(y*2)]) or x), [1,2,3,4], [])
[]
[2]
[2, 4]
[2, 4, 6]
[2, 4, 6, 8]
{% endhighlight %}

# Basically it executed print statement but print returns nothing so always returned the list

### Cool Builtin Functions

**sorted** (uses timsort in c)

list.sort() is a list function that sorts it and returns nothing.

sorted takes 3 args, iterable, key and reverse. reverse is s boolean. What makes sorted() so powerful is that key can be any custom function on the basis of which the iterable can be sorted.

{% highlight python %}
>>> sorted([1,-2,3,-4], key=abs) # out: [1,-2,3,-4]
>>> sorted({'a':1, 'b':3, 'c':5}) # out: ['a', 'b', 'c'] # gives sorted keys
>>> d = {'a':1, 'b':7, 'c':5} >>> sorted(d, key=d.get) # out: ['a', 'c', 'b']
>>> sorted(d.items(), key=lambda x: x[1]) # out: [('a', 1), ('c', 5), ('b', 7)]

# basically you can use lambda oeration also as a fuction to sort values on

>>> sorted(['banana', 'pie', 'Washington', 'book'], key=lambda x: x[::-1]) # sorts reverse of a string

# also, sort on multiple keys:

>>> s = sorted(s, key = lambda x: (x[1], x[2]))
{% endhighlight %}

Also, sort() and sorted () both work with same args. Only difference is that list.sort() sorts the list in place and doesn't return anything while sorted does not sort the iterable in place and the iterable is still intact after this method is called.

Also, while sorting a dict, you need to understand that you need to convert the dict to list of tuples of key and values.

>>> d = {'a': 1, 'b': 7, 'c': 5}
>>> d.items() # out: dict_items([('a', 1), ('b', 7), ('c', 5)])

Now this can be treated as a list to be sorted. And you can use lambda func to get val as lambda x: x[1] directly or any attribute, lookup or a function associated with the values of the dict to sort.

sorted also works on generators but would be slower than list.

Any case you cannot sort an iterator without having every element in memory even.

And lazy sorting would be a really cool thing. We can use heap ds for that. python provides heapq.

<https://ondergetekende.nl/lazy-sorting-in-python.html>

>>> heapq.nlargest(2, [1,2,-3,4,-5], key=abs) # out: [5, 4]

**bisect**
{% highlight python %}
# used to perform binary search related operation in sorted list
import bisect

bisect.bisect_left([1,2,4,5], 2)    # 1
# returns the index if it exists
bisect.bisect_left([1,2,4,5], 3)    # 2
# returns the next index if it does not exist

{% endhighlight %}
**zip**

Zip joins multiple tuples together. But why use zip? Iteration will happen outside gil in C

{% highlight python %}
>>> fields = ["id", "name", "location"]
>>> values = ["13", "bill", "redmond"]
>>> dict(zip(fields, values))

{'location': 'redmond', 'id': '13', 'name': 'bill'}
{% endhighlight %}

Zip ignores the values after the length of the minimum iterator in case of unequal iterators.

Other great resources in python:

**itertools**

Simple tools to play with iterables. And the best thing is that it returns a generator instead of a list.

{% highlight python %}
import itertools
>>> list(itertools.combinations([1,2,3,45,], 3)) # out: [(1, 2, 3), (1, 2, 45), (1, 3, 45), (2, 3, 45)] # nCr
>>> list(itertools.permutations([1,2,3], 2)) # out: [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)] # nPr
>>> list(itertools.accumulate([1,2,3,3,2])) # out: [1, 3, 6, 9, 11]

# groupby is a great function to group data based on values and may prove very helpful in regrouping api data

>>> for k, v in itertools.groupby(sorted(d.items(), key=lambda x: x[1]['s']), key=lambda x: x[1]['s']):
...:    for x in v:
...:        print(x)
...:    print('')

out:

('a', {'s': 'a'})
('d', {'s': 'a'})

('b', {'s': 'v'})
('c', {'s': 'v'})

All available functions are:

accumulate, combinations_with_replacement, cycle, groupby, product, takewhile, chain, compress, dropwhile, islice, repeat, tee(), combinations, count, filterfalse, permutations, starmap, zip_longest
{% endhighlight %}

**operators** are another such great set of tools.

>>> operator.itemgetter(1,2,3)('asdfghjk')
>>> out: ('s', 'd', 'f')
>>> s = sorted(s, key = operator.itemgetter(1, 2))

# basically, operator.itemgetter(1, 2) will give you a function that returns elements at index 1&2 of an iterator.

### collections

a collection of high performance container datatype

{% highlight python %}
import collections

>>> collections.Counter([1,2,3,45,1,2,3,5,67,]) # out: Counter({1: 2, 2: 2, 3: 2, 45: 1, 5: 1, 67: 1})

# defaultdict >> doesn't throw key error on non existent key

>>> s = 'mississippi'
>>> d = defaultdict(int)
>>> for k in s:
...     d[k] += 1
out: defaultdict(int, {'m': 1, 'i': 4, 's': 4, 'p': 2})

# similarly, you can use it to append to a list etc.
# ordereddict >> maintains order
>>> c = collections.OrderedDict([('a', 1), ('b', 2), ('c', 3)])
>>> c['a'] # out: 1
# deque > list optimized for inserting and removing items

>>> d = collections.deque(['a', 'b', 'c', 1,2,34,])

d.popleft(), d.appendleft()

# namedtuple >> more readable tuple

Student = namedtuple('Student', 'fname, lname, age')
s1 = Student('John', 'Clarke', '13')
>>> print(s1.fname, s1.age)
out: John, '13'
{% endhighlight %}

**metaclass:**

So everything is an object in python. metaclass is the class that creates all the objects.

<https://stackoverflow.com/a/6581949/4510252> great anwer explaining metaclass in detail.

>>> type(type). out: type
>>> class Foo: pass
>>> type(Foo). out: type

so basically type is that god thing, the metaclass
now you create a

>>> class Basemeta(type): pass

and then use it as the metaclass to create your classes and ensure certain things like presence of an attribute or method in the class at the time of definition only. (don't do this, btw)

>>> class MyClass(metaclass=Basemeta): pass

**contextmanager**: <https://jeffknupp.com/blog/2016/03/07/python-with-context-managers/>

Context manager helps you build and teardown a context before and after an action. A good example is read a file, you need to open it before and close it afterwards no matter what. Even if there is an exception while reading the file etc.

Simply speaking, a context manager helps you abstract out try: .. finally: ..  construct in that no matter how the block exits.

{% highlight python %}
# You can have a class based context manager or a function based.

# a basic structure would be

class ManagedFile:
    def __init__(self, name):
        self.name = name
    
    def __enter__(self):
        self.file = open(self.name, 'w')
        return self.file
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()

# so when you do:

with ManagedFile:
    do_something()

# __enter__() and __exit__() are called before and after do_something() respectively.
# you can use it to build and tear down the context or anything you want.

from contextlib import contextmanager

@contextmanager
def open_file(filename):
    opened_file = open(filename)
    try:
        yield opened_file
        # this function pauses here and restarts when control is given back to it.
    finally:
        opened_file.close()

# here the decorator converts this open_file function into a proper context manager, that is add an __enter__ & __exit__ function to be called. (without the decorator, if you use `with open_file():` it throws AttributeError: __enter__
{% endhighlight %}

MonkeyPatching: It's a language agnostic concept of replacing the function/method/class by some other implementation at runtime:

>>> import os
>>> os.chroot = lambda x: "i will not do that! :P"
>>> os.chroot(':P'). # out: "i will not do that! :P"

Python mock libraries used for testing essentially monkey-patch the mocked functions.

Great talk: <https://www.youtube.com/watch?v=7lmCu8wz8ro>

Covers metaclass, & contextmanagers among other things.

Also, you should know about pep (python enhancement proposals) <https://www.python.org/dev/peps/>

