----
layout: post
title: Cabal tips
----

Haskell's [Cabal](https://www.haskell.org/cabal/) is notoriously hard to use,
and has a lot of hidden features that are not featured prominently in the
documentation. This post is a collection of useful Cabal how-tos and tips I've accrued over the years.


**Getting started**

As soon as you start a new project, create a new sandbox:

```
cabal sandbox init
```

This will create a new sandbox in `.cabal-sandbox`.

To create a cabal file for your project:

```
cabal init
```


**Building your application and using the Cabal REPL**

When you are ready to build your application:

```
cabal build
```

This will create executable files in `dist/build`.
