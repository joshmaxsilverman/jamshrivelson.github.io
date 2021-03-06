---
layout: post
published: true
title: Fast Spelling Bee
date: 2020/01/06
---

>**Question:** The NYTimes has a new word game called Spelling Bee. The idea of the game is, on a board like the one below, to make words as using **only** the letters on the board under the constraint that the central letter must be used. Words are worth their length in letters (with a minimum length of $4$), but words that use all $7$ letters — known as **pangrams** — are awarded $7$ bonus points. The valid word list is Peter Norvig's which contains some 172820 distinct words. The object is to find the game board with the highest possible maximum score.
>
>![](/img/2020-01-06-honeycomb.png){: width="450px" class="image-centered"}


<!--more-->

([FiveThirtyEight](https://fivethirtyeight.com/features/can-you-solve-the-vexing-vexillology/))

## Solution

### The numbers

$$\begin{array}{|c|c|} \hline
\textbf{Quantity} & \textbf{Value} \\ \hline
\text{words} & 172820 \\ \hline
\text{pangrams} & 7\binom{26}{7} = 4604600 \\ \hline
\end{array}$$

On first glance, these numbers are daunting. A naive search would need to generate some $\approx 5\times10^6$ games, and compare them against $\approx 1.5\times10^5$ words, a hefty $\approx 10^{12}$ comparisons. Simply looping over a list that long (without any string operations) would take about $10\ \text{hours}$ in a Colab notebook. Hopefully there is some structure we can exploit.


### Plan

The big idea here is to only ever make linear passes over the word list, and avoid looping the list of possible pangrams entirely. The logic of the two major passes is

1. accumulate the possible pangrams and populate a data structure that can receive score increments for given `middle letter/pangram` pairs as we loop over the word list a second time.
2. loop over the words and distribute their scores to the relevant destination in `pangram_scores`

```python
from collections import defaultdict
from itertools import combinations
import urllib

words = urllib.request.urlopen('https://norvig.com/ngrams/enable1.txt')
words = [word.decode('utf-8').rstrip() for word in words]

word_to_pangram = defaultdict(set)
```

`valid_words` filters the word list for those that are valid, and `pangrams` is a list of the relevant pangrams found by filtering the word list for all words with exactly 7 distinct letters. The `subsets` function takes a set an returns all of its possible (ordered) subsets.

```python
valid_words = [word for word in words 
               if len(word) >= 4 
               and 's' not in word 
               and len(set(word)) < 8]

pangrams = [''.join(sorted(set(_))) for _ in valid_words if len(set(_)) == 7]

def subsets(s):
    for size in range(len(s) + 1):
        yield from combinations(s, size)
```

### Speed-enabling data structure

This code creates a dictionary from all ordered stems of pangrams to their corresponding pangrams. For example, for the pangram `ABCDEF` this would associate the keys `'A'`, `'AB'`, `'ABC'`, `'ABCD'`, `'ABCDE'`, and `'ABCDEF'` to `"ABCDEF"`. 

This is the key to the speed of this approach. Instead of testing which pangrams a word is a subset of, we can directly map from the letters in a word to all the relevant pangrams.

```python
for pangram in pangrams:
    keys = [''.join(tup) for tup in list(subsets(sorted(set(pangram))))]
    for key in keys:
        word_to_pangram[key].add(pangram)
```

This initializes the two-tier dictionary to keep track of `middle letter/pangram` pairs.

```python
pangram_scores = defaultdict(lambda: defaultdict(int))

def word_score(word):
    return (1 if len(word) == 4 else len(word)) + (7 if len(set(word)) == 7 else 0)
```

### Accumulate the scores

This code goes through the list of words, and adds the word score to every relevant pangram. The pangram score dictionary is keyed by a tuple of `(middle_letter, pangram)` so I loop over the word to distribute the scores to all relevant `(middle_letter, pangram)` destinations.

```python
for word in valid_words:
    word_key = ''.join(sorted(set(word)))
    for pangram in word_to_pangram[word_key]:
        for letter in word_key:
            pangram_scores[pangram][letter] += word_score(word)
```

### Find the max

This code loops over the two level dictionary to find the max.

```python
tmp_max = 0
tmp_pangram = (1,1)
for key1 in pangram_scores:
    for key2 in pangram_scores[key1]:
        if pangram_scores[key1][key2] > tmp_max:
            tmp_max = pangram_scores[key1][key2]
            tmp_pangram = (key1, key2)

print(tmp_max, tmp_pangram)
```





<br>
