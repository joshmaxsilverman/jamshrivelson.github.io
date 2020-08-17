---
layout: post
published: true
title: Broken Ruler
date: 2020/08/17
---

>**Question**: Quality control at your ruler factory has taken a turn for the worst and your latest shipment of rulers are all broken into 4 pieces! What is the average length of the shard that contains the $\text{6 inch}$ mark?

<!--more-->

([FiveThirtyEight](https://fivethirtyeight.com/features/are-you-hip-enough-to-be-square/amp/?__twitter_impression=true))

## Solution

At first I solved the \(4\)-piece case with a cobbled-together multivariable integral, expecting the $N$-piece case to involve sums of compound integrals. However, following compelling empirical results from Goh Pi Han, I was inspired to look for a simple perspective.

The approach here will be to find the probability distribution for the length of the shard that includes the $\text{6 inch}$ mark ($x=1/2$ for the purpose of this solution). The the main insight is that for a length $\ell$ interval to cover the point $x = 1/2,$ all we need is that two points are a distance $\ell$ apart and that no other points interrupt that interval (or else it would become a covering interval of length $\ell^\prime < \ell$).

### Does it cover?

The first non-trivial issue here is the probability that a random interval of length $\ell$ will cover the point $1/2.$ 

First of all, if $\ell \geq 1/2,$ then this probability is $1$ — there's no way to place an interval of length $\ell > 1/2$ without encompassing the middle of the ruler. So, whatever expression we find, we expect it to equal $1$ when $\ell = 1/2.$

For a ruler of length $\ell,$ the total length of the region where we can place the shard's leftmost point is $1-\ell$ (we can't place it any further, or its right most point would include points not on the original ruler). And if the shard's leftmost point starts more than a distance $\ell$ away, it won't reach $1/2.$ 

So, the probability that a random shard of length $\ell$ covers the halfway point is $P_\text{cover} = \ell/(1-\ell),$ which equals $1$ when $\ell = 1/2,$ as we had hoped. To summarize,

$$P_\text{cover}(\ell)=\begin{cases}
\dfrac{l}{1-l} & \text{when } \ell \lt 1/2 \\
1 & \text{when } \ell \geq 1/2.
\end{cases}$$

### Cases

There are two umbrella cases:

- where the shard is formed from two points where the ruler has broken. When $\ell < 1/2$, this is the only way for the shard to form. 
- when $\ell \geq 1/2$ it becomes possible for the shard to stretch all the way from an original endpoint of the ruler (say the $0\text{ inch}$ mark) past $1/2.$ 

### Two breakpoints

The first case involves several things: **a**. the random interval has to contain $1/2,$ **b**. no points can be within $\ell$ of the left end of the shard, **c**. the right end of the shard has to be a distance $\ell$ from the left end, **d**. the number of ways we can pick $2$ endpoints out of the $N$ breakpoints $\{x_1,x_2,\ldots,x_N\}.$

Parts **a** and **b** contribute $P_\text{cover}(\ell)\times \left(1-\ell\right)^{n-1}.$ Part **d** contributes $2\times\binom{N}{2}$ (the $2$ because $x_i$ on the left with $x_j$ on the right is distinct from the reverse). Part **c** is a bit tricky. The first and last point have to be chosen a distance $\ell$ apart. However, each point is a random draw of uniform probability. As we've already ensured that the other $\left(n-1\right)$ points are a distance of at least $\ell$ from the first, this factor is just $1.$

In total, the distribution of lengths $\ell$ for a shard with two breakpoints for ends is 

$$P_2(\ell) = 2\binom{N}{2}P_\text{cover}(\ell)\left(1-\ell\right)^{n-1}.$$

### One breakpoint

For the case where only one end of the shard is a breakpoint, the calculation is straightforward. The first point is picked with uniform probability, all $\left(n-1\right)$ remaining points have to be picked outside of the interval $\left[0,x\right),$ and there are $\binom{N}{1}$ ways to select the first point. Since there's a symmetric set of cases when the unbroken endpoint is at the $\text{12 inch}$ mark, we multiply by $2$. Though there is $100\%$ chance for this shard to cover $1/2$ (since such a shard must have $\ell\geq 1/2$), we include $P_\text{cover}(\ell)$ for ceremonial purposes.

Putting it all together, the one breakpoint shard's distribution is 

$$P_1(\ell) = 
\begin{cases}
0 & \text{when } \ell \lt 1/2 \\
2\binom{N}{1}P_\text{cover}(\ell)\left(1-\ell\right)^{n-1} & \text{when } \ell \geq 1/2.
\end{cases}$$

### Complete pdf

With these two cases in hand, the overall pdf is just $P_\text{total}(\ell) = P_1(\ell) + P_2(\ell)$ which has a jump discontinuity at $\ell=1/2$

<br>