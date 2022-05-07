---
layout: post
title: "Probably correct"
date:   2022-05-07 00:00:00 +0530
categories: probability
tags:
  - math
  - cs
---

Some problems in probability (and other related areas) could be challenging because using multiple seemingly correct approaches and your intuition could be at odds, each giving _very different_ results. Here are some interesting problems.

<!-- more -->

### Two envelopes problem

> Imagine you are given two identical envelopes, each containing money. One contains twice as much as the other. You may pick one envelope and keep the money it contains. Having chosen an envelope at will, but before inspecting it, you are given the chance to switch envelopes. Should you switch?

#### Reasoning #1
Intuitively, the problem might seem a little silly at first. _What difference could it possible make?_

Both the envelopes are identical in every way. Switching shouldn't make any difference since the whole situation is symmetric.

It doesn't matter whether you switch or not, the chances are 50:50.

Maybe you are right, but give me a chance to explain!

#### Reasoning #2
Suppose you picked `Envelope A` which contained &#8377;X. `Envelope B` could contain either &#8377;X/2 or &#8377;2X. Both of these are equally likely. Therefore, the expected value would be

$$0.5 * \frac X 2 + 0.5 * 2X = 1.25X$$ 

&#8377;1.25X > &#8377;X. Expected value is higher on switching!

#### Reconciliation
So, which is it exactly?

One observation: Suppose `Reasoning #2` were correct. After you switch the envelopes once, the other envelope is more desirable (by the same expected-value calculation). So, you were better off in the first place. 

This is absurd and further cements `Reasoning #1`. But this still doesn't explain what is wrong with `Reasoning #2`.

The problem with `Reasoning #2` is, when we say `Envelope B` has more money, the possible values for `Envelope A` & `Envelope B` are in the set {&#8377;X, &#8377;2X}. When we say `Envelope B` has less money, the set is {&#8377;X, &#8377;X/2}. So, we are essentially picking a value for `Envelope B` from different sample space in the equation above.

Hence, look at it this way. `Envelope B` either has more money or it has less money (duh!). So, the two cases are
- `Envelope A` has &#8377;Y and `Envelope B` has &#8377;2Y.
- `Envelope A` has &#8377;2Y and `Envelope B` has &#8377;Y.

This way the expected value is the same with or without switching, &#8377;3/2Y. Which is consistent with `Reasoning #1`!

Here is an _even more_ surprising variant. 

> What happens when you are allowed to inspect the contents of the envelope you choose first?

### Random point in a circle

I came across this problem while watching a video in `The Summer of Math Exposition` playlist.

> You are given a perfectly uniform random number generator `rng`. Devise an algorithm to pick a random point within a unit circle.

I paused the video and tried to come up with the solution.

First, I thought of placing unit circle in a plane centered at $$(0,0)$$. Then I came up with the easiest possible solution.

```C++
while (true) {
  double x = rng(-1, 1);
  double y = rng(-1, 1);
  if ((x*x + y*y) <= 1.0) {
    return {x, y};
  }
}
```
But I could overshoot the circle and land on fringes of the square. Probability of that would be $$\frac {4-\pi} 4$$. Over a number of iterations that would tend to 0 but still, it isn't perfect.

Then, I thought of leveraging $$(r, \theta)$$ representation.
```C++
double radius = rng(0, 1);
double theta = rng(0, 360);
return {radius*cos(theta), radius*sin(theta)};
```
Ah, the elegance! I smugly resumed the video.

This algorithm was faulty. Each radius [0, 1] has an equal probability of being picked. Imagine that every radius is mapped to a concentric ring. So, we basically pick a ring (via `radius`) and then pick a point on its circumference (via `theta`). 

Now, the probability of any ring being picked is the same. But the rings with higher radius have a lot more points in the circumference, so ideally they should be chosen more often. 

In other words, the points chosen by this algorithm will be concentrated to the center.

nubDotDev [proposed](https://youtu.be/4y_nmpv-9lI) an ingenious approach of converting the uniform `rng` to a non-uniform `rng`, that way the rings father away have a higher probability of being picked.

Moral: Be careful while calculating probabilities in an infinite sample space.

### Other paradoxes

- Bertrand posed a problem in his book, "What is the probability that a chord in unit circle is larger than $$\sqrt 3$$?". The problem setting is straightforward and yet multiple [simple solutions](https://youtu.be/mZBwsm6B280) yield different answers, it is baffling! Calculating probabilities in infinite sample space is hard indeed. 
- The most classic probability paradox - [Monty Hall problem](https://en.wikipedia.org/wiki/Monty_Hall_problem). Even mathematicians of Paul Erdos' calibre had a hard time wrapping their head around a problem popularized by a magazine column!

### References
- [Wikipedia Category: Probability theory paradoxes](https://en.wikipedia.org/wiki/Category:Probability_theory_paradoxes)