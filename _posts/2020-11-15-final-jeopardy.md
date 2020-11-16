---
layout: post
published: true
title: Final Jeopardy
date: 2020/11/15
---

>Question

<!--more-->

([FiveThirtyEight](URL))

## Solution

In both strategies, the thresholded bonus rule is in play: if the player has accumulated less than $\\$1000$ by the time they hit the Daily Double, they get house money to be with in the amount of $\\$1000,$ otherwise they can use their own winnings to bet. The row by row strategy has just $30$ cases to consider since there is one guessing order and $30$ possible locations for the Daily Double. However, the random strategy has $30! \approx 2.7\times 10^{32}$ possible orderings, each of which can be interrupted by the Daily Double at $30$ different points. 

## Row by row

For the row by row strategy, we can just go one tile at a time. If the accumulated winnings by the Daily Double tile are less than $\$1000,$ then we get $\$1000$ to bet with. Otherwise, we bet all our accumulated winnings, doubling our money. Also, we do not get the face value of the tile we hit the Daily Double on. Saving the math for the extra credit, we can just code up the logic above with a short Python script. It shows that the expected value of this strategy is $\$23,800:$

```python
import numpy as np

tiles = 6*[200] + 6*[400] + 6*[600] + 6*[800] + 6*[1000]
prizes = []

for _ in range(0, len(tiles)):
    temp_total = 0
    DD_bonus = 0
    if sum(tiles[:_]) < 1000:
        DD_bonus = 1000
    else:
        DD_bonus = sum(tiles[:_])
    temp_total = DD_bonus + sum(tiles[:_]) + sum(tiles[(_+1):])
    prizes.append(temp_total)
    
print(np.mean(prizes))
```

## Random guessing

For random guessing, there are a bunch of cases to consider — between the orderings and the Daily Double placement, there are some $30\times30!$ possible games. Our task is to calculate the average winnings, which is a sum of the base value of the tiles and the bonus:

$$W = \text{Base value of tiles} + \text{Daily Double bonus}.$$

### Simple model

We can make a first pass at the answer with an audacious approximation. The number of cases where the thresholded bonus gets applied (handing the player $\$1000$ to bet with) are relatively few, so if we ignore them it shouldn't have a huge impact on the answer.

Putting them to the side, we can focus on the core of the bonus mechanism: allowing the player to bet their accumulated winnings. 

In this case, the player gets the combined total of all the tiles on the board plus the total of the tiles they'd won by the time they hit the daily double. Ignoring the discrete nature of the tiles, we can say that the player hit the daily double when they were a fraction $f$ to accumulating all the value on the board. If each tile's value is $t_i$ then the total value is $T = \sum_i t_i$ and the player's winnings come to

$$W^\prime = T + f\times T$$

Because the daily double is placed randomly, the expected value of $f$ is $1/2$ and $$\langle W^\prime \rangle = 3T/2 = \$27,000.$$

### Small fixes

Of course, this is wrong. We've neglected a few things: we do not get the value of the Daily Double tile, the tiles are discrete, and the threshold ensures that nobody bets with less than $\$1000$ at their disposal.

Let's deal with the first thing first. The total value of the tiles is $T = \sum_i t_i,$ but we don't get the full value of $T,$ we skip out on the value of the Daily Double tile $t_d.$ Since $t_d$ is equally likely to be any of the tiles, which are evenly split between $\$200,$ $\$400,$ $\$600,$ $\$800,$ and $\$1000,$ we have to subtract off the average value of a single tile:

$$\langle \text{Base value of tiles}\rangle = T - \frac{$\$200$ + $\$400$ + $\$600$ + $\$800$ + $\$1000$}{5} = \$17400 $$

To get a handle on the bonus we can split it into two pieces, the core winnings betting piece, and the adjustment for the players who are under the threshold. Concretely, if a player has $\$400$ when they hit the daily double, we can imagine that they bet their $\$400,$ win it, and then receive an one time bonus of $\$600$ to make up for the gap between their accumulated winnings and the $\$1000$ floor. 

So, everyone gets to double their winnings. The daily double can fall at any point between Tile $0$ (when the player would have $\$0$ accumulated) and Tile $30$ (when the player would be close to the maximum amount they could accumulate). On Tile $30$ the player will have collected all but one of the tiles, so they'd have an average of $\$18,000 - \$600 = \$17400.$ The distribution is symmetric about the middle, so the average amount gained by betting their winnings is 

$$\frac{\$0 + \$17,400}{2} = \$8,700.$$

### Big fixes

So far, we've calculated the base amount that any player can expect due to collecting the tiles and the winnings betting mechanism and those contributions come to $\$17,400 + \$8,700 = \$26,100,$ slightly under the level of the naive model. However, we still have to add in the adjustments due to the players who hit the Daily Double when they were under the threshold. 

In particular, all players who hit the Daily Double on their first guess will have had $\$0$ at that point, and therefore need to be credited a full $\$1,000.$ Players who hit the Daily Double on their second tile will also be under the threshold unless they happened to land on a $\$1,000$ tile on their first turn. In general, this is a combinatorics problem, counting the number of ways a player could have been under the threshold when they hit the Daily Double.

$$\{\boxed{t_1}\}$$

As we said, all players who hit the Daily Double on their first tile will recieve the full $\$1,000.$ Since a player has a probability of $1/30$ to experience this outcome, it contributes $\$1,000/30$ to the expected value. 

$$\{t_1, \boxed{t_2}\}$$

For players who hit the Double on their second tile, there are $24$ ways for them to be under the betting threshold: if they hit one of the six $\$200$ tiles, one of the six $\$400$ tiles, one of the six $\$600$ tiles, or one of the six $\$800$ tiles on their first tile. 

$$\begin{array}{c|c|c} \\ \hline
W & \text{ways} & \Delta \\ \hline
\$200 & 6 & \$800 \\ \hline
\$400 & 6 & \$600 \\ \hline
\$600 & 6 & \$400 \\ \hline
\$800 & 6 & \$200 \\ \hline
\end{array}$$

Since there are $30$ ways to pick the first tile, this contributes 

$$\frac{1}{30}\left(6\times \$200 + 6\times \$400 + 6\times \$600 + 6\times \$800\right) = \$400$$

to the expected value of the threshold adjustment.


<br>