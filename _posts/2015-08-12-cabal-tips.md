---
layout: post
title: Cabal tips
---


Haskell's [Cabal](https://www.haskell.org/cabal/) is notoriously hard to use,
and has a lot of hidden features that are not featured prominently in the
documentation. This post is a collection of useful Cabal how-tos and tips I've accrued over the years.


**Getting started**

Before you start a new project, make sure you have the most recent version of Cabal by running `cabal update`.

As soon as you start a new project, create a new sandbox with `cabal sandbox
init`. This will create a new sandbox in `.cabal-sandbox`.

To create a cabal file and configure your project:

```
cabal init
cabal configure
```

----

**Getting help**

Getting help for cabal options is somewhat tedious. To view all of the
available cabal commends, use `cabal -h`. To view the available flags for each
option, use `cabal <command> -h`, e.g. `cabal build -h`.

----

**Building your application and using the Cabal REPL**

When you are ready to build your application, use `cabal build`. This will
create executable files in `dist/build`.

Note that `cabal build` actually has three `verbose` flags. Use `-v3` for
maximum verbosity.

Once you have a working build, you can use the Cabal REPL rather than GHCi.
This is just GHCi, but with your project already built and imported for you. In
the Cabal REPL, use `:r` to rebuild and reload all files. This way, you can
work on editing your project files while also playing around with them in the
REPL, with no wasted time to reopen GHCi.

To load a particular library, you can use `cabal repl <filename>`.


----

**When you get stuck in dependency hell**

First, clean up any object/executable files. Second, delete the current sandbox
and packages installed in your profile.

```
cabal clean
cabal sandbox delete
rm -rf ~/.ghc ~/.cabal/
```

Next, re-create your sandbox and install the dependencies.

```
cabal sandbox init
cabal update

cabal configure --enable-tests
cabal install --only-dependencies --enable-tests
```

Now you can rebuild your project:

```
cabal build -v3
```

