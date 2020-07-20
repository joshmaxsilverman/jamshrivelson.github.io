---
layout: post
published: true
title: A Trip Down Rubber Band Road
date: 2020/07/19
---

>Question

<!--more-->

([FiveThirtyEight](URL))

## Solution

### Insight

On the regulation issue road that doesn't magically extend by $12$ miles every minute, the problem is a bit simpler. Because the Hare can cover $5/4$ as much ground as the Tortoise in the same amount of time, they should wait until the distance the Tortoise has to go is $4/5$ of the distance that the Hare has to go. In other words, $4/5\times 10\,\text{miles} = 8\,\text{miles}$.

As it happens, this is the key to the magical road as well. 

When the Tortoise starts walking down the magic road, they make it $1\,\text{mile}$, or $10\%$ of the way, before the road stretches. While this stretch adds road to the path before them, it does nothing to affect the fractional progress the Tortoise has made along the road. 

In other words, whenever the Hare starts down the road, they are dealing with the same problem the Tortoise is, just longer. All of the proportional stretching from that point on will be identical for Tortoise and Hare. 

So, as on the non-magical road, Hare should start when Tortoise is $20\%$ of the way to the end of the road.

### Tortoise

Every minute, the tortoise travels a distance $\Delta_T = v_T\times \text{1 min}$ and then the road stretches by the factor $(t+1)/t$. This makes the tortoise position as a function of time (using integer numbers of minutes):

$$d_\text{tortoise}(t) = \left[d_\text{tortoise}(t-1) + \Delta_T\right]\frac{t+1}{t}$$

Recursing one step, 

$$\begin{align}
d_\text{tortoise}(t) &= \left[\left[d_\text{tortoise}(t-2) + \Delta_T\right]\frac{t}{t-1} + \Delta_T\right]\frac{t+1}{t} \\
&= d_\text{tortoise}(t-2)\frac{t+1}{t-1} + \Delta_T\frac{t+1}{t-1} + \Delta_T\frac{t+1}{t}
\end{align}$$

Continuing on in this way we get to

$$d_\text{tortoise}(t) = d_\text{tortoise}(0)\left(t+1\right) + \Delta_T\left(t+1\right)\left(1 + \frac12 + \frac13 + \cdots + \frac1t\right)$$

but since $d_\text{tortoise}(0) = 0$ and the series is the Harmonic series, this is just 

$$d_\text{tortoise}(t) = \Delta_T\left(1+t\right)H(t)$$

Unless they hit the finish line in a multiple of minutes, they'll have a small fraction of the journey they happens after the last road stretching. With this in mind, Tortoise's distance becomes:

$$d_\text{tortoise}(t_\text{finish}) = \Delta_T\left(1+\lfloor t_\text{finish}\rfloor\right)H(\lfloor t_\text{finish}\rfloor) + \Delta_T\left(t_\text{finish} - \lfloor t_\text{finish}\rfloor\right)$$

###The finish line

The position of the finish line is just $\Delta_L\left(1+t\right)$ which means that Hare should begin when $d_\text{tortoise}(t) = 20\% \times \left(1 + \lfloor t\rfloor\right)\Delta_L$ or 

$$\Delta_T\left(1+\lfloor t_\text{finish}\rfloor\right)H(\lfloor t_\text{finish}\rfloor) + \Delta_T\left(t_\text{finish} - \lfloor t_\text{finish}\rfloor\right) = 20\%\times \Delta_L\left(1+\lfloor t_\text{finish}\rfloor\right)$$

The fractional part is, by definition, less than $1$, so we can drop it and see when the harmonic pieces alone wouldNo harmonic number is equal to $8$ and this raises the fact issue of non-integer driving times for the tortoise (and later the hare).

<br>