---
layout: post
title: Abusing Python's type system
---

I remember being impressed by the fact that the [Y
combinator](https://en.wikipedia.org/wiki/Fixed-point_combinator#Fixed_point_combinators_in_lambda_calculus)
can be defined recursively as `Y = f (Y f)` in Haskell, but after some
experimentation I realized that you can do the exact same thing in Python:

```python
>>> Y = lambda f: lambda *args: f(Y(f))(*args)
```

Of course, you also can use the straightforward non-recursive definition in
Python:

```python
>>> Y = lambda f: (lambda x: x(x))(lambda x: f(lambda *args: x(x)(*args)))
```

You actually can’t do this in Haskell! The type system (rightly) realizes you
are constructing an infinite type, so you need to use a newtype wrapper to make
it work.

```haskell
ghci> (\f-> (\x -> f (x x)) (\x-> f (x x)))

<interactive>:3:19:
    Occurs check: cannot construct the infinite type: t1 = t1 -> t0
    Expected type: t1 -> t0
        Actual type: (t1 -> t0) -> t0
    In the first argument of `x', namely `x'
    In the first argument of `f', namely `(x x)'
    In the expression: f (x x)

<interactive>:3:34:
    Occurs check: cannot construct the infinite type: t1 = t1 -> t0
    In the first argument of `x', namely `x'
    In the first argument of `f', namely `(x x)'
    In the expression: f (x x)
```

As it turns out, there are even _more_ ways to abuse Python's type system. For
starters, there is a much easier way to create a value with an infinite type:

```python
>>> x = (lambda: x)
>>> x()
<function <lambda> at 0x1005c36e0>

>>> x
<function <lambda> at 0x1005c36e0>
```

Unlike the Y combinator, you can't really do anything with this value, though.

Alternatively, you can use a generator:

```python
def f():
    yield f

>>> f()
<function f at 0x1007c36e0>
```

The object system has some similar holes. For example, you can make a class
it's own metaclass:

```python
>>> import abc
>>> class A(abc.ABCMeta): pass

>>> A.__metaclass__ = A
```

or:

```python
>>> A("A", (), {}).register(A)
```

But wait, it gets even better:

```python
>>> class A(abc.ABCMeta): pass
>>> class B(abc.ABCMeta): pass

>>> A.__metaclass__ = B
>>> B.__metaclass__ = A
```

I thought long and hard about how these tricks might be useful when I made
[Hask](https://github.com/billpmurphy/hask), but I still can't think of
anything.
