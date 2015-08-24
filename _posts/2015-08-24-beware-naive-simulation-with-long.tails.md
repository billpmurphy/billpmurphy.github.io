---
layout: post
title: Beware naive simulation with long tails
---

Suppose a casino offers you the chance to play the following game:

> You are given an initial stake of $2. A fair coin is flipped. If the result
> is TAILS, you keep the current stake. If the result is HEADS, the stake
> doubles and the coin is flipped again. That is, the coin is flipped until the
> first TAILS appears, at which point the game ends and you exit with the
> stake.

What price would you be willing to pay the casino to play this game?

It might be tempting to just run a simple Monte Carlo simulation, look at the
expected value, and then set your price slightly below the EV of the game. We
can write this up in Python very easily:

```python
import random

def flip_coin():
    return random.choice(["HEADS", "TAILS"])


def play_game(stake=2):
    if flip_coin() == "HEADS":
        return play_game(stake * 2)
    return stake
```

Now let's run 100 of trials of 100,000 games each, and take the mean winnings
of each trial.

```
import numpy as np
import pandas as pd

trials = [np.mean([play_game() for j in range(10**5)])
          for i in range(100)]

pd.Series(trials).describe()
```

And we get:

```
count    100.000000
mean      27.006648
std       36.184733
min       14.257340
25%       16.389855
50%       19.434740
75%       24.321040
max      1355.49450
```


Uh-oh. Our estimates of the expected value of the game range from 10\*\*1 to
10\*\*3. Maybe we need to run more tests? If we run 1000 trials of 100,000 games
each, we get the following:

```
count     1000.00000
mean       74.006964
std       536.743463
min        13.853920
25%        16.688840
50%        18.874780
75%        22.625120
max      5387.429780
```

This clearly isn't working. Instead, let's examine the problem in more detail.
Here are the possible outcomes after 3 flips:

![tree
diagram](https://github.com/billpmurphy/billpmurphy.github.io/blob/master/_assets/tree1.png)

Using this, we can break down the problem into a series. With probability 1/2,
we win $2. With probability 1/4, we win $4. With probability 1/8, we win $8. So
the expected value can be computed as follows:

![EV
calculation](https://github.com/billpmurphy/billpmurphy.github.io/blob/master/_assets/EV_calc1.png)

Aha! As it turns out, The expected value doesn't actually converge, so
simulating and trying to find the mean doesn't shed any light on the structure
of the problem. In addition, the majority of the expected value comes from
extremely unlikely outcomes __ad infinitum__, so simply adding more simulations
didn't improve our accuracy.

So what's the actual solution? How much should you be willing to play to pay
this game? Well...there isn't really an answer. This is the imfamous [St.
Petersburg Paradox](https://en.wikipedia.org/wiki/St._Petersburg_paradox); it
turns out that even brilliant mathematicians are confused by this one.

The moral of this silly example is that you need to be __very careful__ using
Monte Carlo methods when you are dealing with long tail risks, especially if
they are compounding or involve an infinite series. If most of your EV is in
low-probability branches of your simulation, just running lots and lots of
tests isn't going to help--you need to step back and find an analytical
solution.

In practice, though, if anyone does offer you this game, you should remember
that [your utility probably isn't linear in
money](https://en.wikipedia.org/wiki/St._Petersburg_paradox#Expected_utility_theory)
and consider the counterparty risk--can the casino __really__ pay out if you
win big?
