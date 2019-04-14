---
title: "An introdcution to Monte-Carlo simulation"
date: 2018-06-01
tags: [python, data science, simulation]
excerpt: "Mathematically simulated version of Sims game for solving big problems"
---

This time I will not dive into the mathematical side of things (although I would really love to do it) and keep things simple. 

### Isn't Monte-Carlo a city name?

Of course, it is, yes. They say that this method also named after Monte-Carlo, the gambling capital of Europe. You'll soon see why. Monte-Carlo method is basically simulating the finite number of "real-world" events in order to calculate the probability of different possible outcomes.

Nowadays, this method is quite widely used. In production, it is used to model the operational behaviour; in physics, chemistry, and biology it is used to generate near real-world conditions which help to find out the outcomes before making this experiments; in game theory, it is used to model the gaming AI; in finance, it helps to forecast the outcome of the usage of different financial tools and etc. This model is used almost everywhere. 

### Brief history

The modern version of this method formed during **Manhattan Project** in order to model the distance that neutrons could cover in different materials. Before that, the idea of generating random samples in order to solve mathematical problems was already around. **Comte de Buffon** suggested to use this as a problem which was named after him, [**Buffon needle**](https://en.wikipedia.org/wiki/Buffon%27s_needle).

### Implementation

One of the biggest advantages of this method is that it can take into account the randomness and complexity of the real world. Also, it is robust to change the parameters, e.g sample distribution. **Law of large numbers** is based on this method.

In order to understand it even better, let's use one simplified real-world example. What is the probability that I will get tails when I flip the coin? You will answer that it is roughly **50%** and you will be right. Yet, let's assume that we do not have any prior knowledge about the probability theory. How can we solve this problem? Now, Monte-Carlo simulation will come handy. Let's proceed to code part:

``` python

import random # we will use it to generate coin flips
from collections import Counter # this we will use to count the outcomes

random.seed(1) # in order to make our randomness reproducable

def coin_flip():
    return random.choice([0,1]) # this function will return either 0 or 1. Let's assume 0 is tails.

result_list = list() # we will keep the results in this list

for _ in range(10**5): # we will flip the coin 100k times and save the results
    result_list.append(coin_flip())

Counter(result_list)
```
And the result will be:
> `Counter({0: 50022, 1: 49978})`

As you see, the numbers of getting heads or tails are roughly equal, 50% for each. Of course, this number is not the real probability. Yet, according to the **law of large numbers**, if we fil the coin `n = ∞` times, these numbers will be equal.

### Disadvantage

Except for aforementioned advantages of this model, Monte-Carlo has its drawbacks. The main problem is the generation of random populations. The example I used above is the simplest possible. However, in physics, finance, and biology, the population may have more than hundred or thousand features and generating them all is very complex and computationally heavy task.

However, taking into account the computational power of modern machines I can clearly state that, this method will become more and more popular in the near future.