---
layout: post
title: Abusing Python's type system
---

I remember being impressed by the fact that the [Y
Combinator](https://en.wikipedia.org/wiki/Fixed-point_combinator#Fixed_point_combinators_in_lambda_calculus)
can be defined recursively as `Y = f (Y f)` in Haskell, but after some
experimentation I realized that you can do the exact same thing in Python:

`>>> Y = lambda f: lambda *args: f(Y(f))(*args)`

Of course, you also can use the straightforward non-recursive definition in
Python:

`>>> Y = lambda f: (lambda x: x(x))(lambda x: f(lambda *args: x(x)(*args)))`

You actually can’t do this in Haskell! The type system (rightly) realizes you
are constructing an infinite type, so you need to use a newtype wrapper to make
it work.

`ghci> (\f-> (\x -> f (x x)) (\x-> f (x x)))`

As it turns out, there are even more ways to abuse Python's type system. For
starters, there is a much easier way to create a value with an infinite type:


<code>
>>> x = (lambda: x)<br>
>>> x()<br>
<function <lambda> at 0x1005c36e0><br>
<br>
>>> x<br>
<function <lambda> at 0x1005c36e0><br>
</code>

Unlike the Y combinator, you can't really do anything with this value, though.

Alternatively, you can use a generator:

<code>
def f():<br>
&nbsp;&nbsp;yield f<br>
<br>
>>> f()<br>
<function f at 0x1007c36e0>
</code>

The object system has some similar holes. For example, you can make a class
it's own metaclass:

<code>
>>> import abc<br>
>>> class A(abc.ABCMeta): pass<br>
<br>
>>> A.__metaclass__ = A
</code>

(You can also use `A("A", (), {}).register(A)`.)

But wait, it gets even better:

<p><code>>>> class A(abc.ABCMeta): pass<br>
>>> class B(abc.ABCMeta): pass<br>
<br>
>>> A.__metaclass__ = B<br>
>>> B.__metaclass__ = A
</code></p>

I thought long and hard about how these tricks might be useful when I made
[Hask](https://github.com/billpmurphy/hask), but I still can't think of
anything.
