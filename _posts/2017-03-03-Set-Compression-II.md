---
layout: post
title: Set Compression Part 2
published: true
---
{% include mathjs %}
WORK IN PROGRESS
We're continuing the series on set compression started in the [previous post](../Set-Compression-I).  We saw how knowing the probabilities of ingredients could help us compress recipes.  However, we may be able to do better.  Imagine for instance someone could tell us first that this recipe is an Italian recipe, then we could use a different set of probabilities, associated with Italian recipes, and the encoding size of the recipe will likely be even shorter.  If there were say 32 types of cuisines, each equally likely, then the sender can inform us about the cuisine using $$log_2 32 = 5$$ bits.  As long as the the encoding of the ingredients can be sent using at least 5 fewer bits, then overall we will have a shorter encoding length. XXX Shorter encoding lengths correspond to better representing the underlying structure of the data. To see this, let's consider the following example.  Below we outline a data generating procedure for sets with two elements.
```
Let m1={ABC}, and let m2={CDE}
To generate a set:
  Choose m1 or m2 50-50
  output two items from chosen set.
```
In Python code: 
```python
m1 = ['A','B','C']
m2 = ['C', 'D', 'E']

from random import randint
msets = [m1, m2]
chosen_set = msets[randint(0,1)]
new_set = [chosen_set[randint(0, len(chosen_set)-1)], chosen_set[randint(0, len(chosen_set))-1]]
print("{}".format(new_set))
```
And generated sets look like this:
['C', 'A']       ['E', 'D']        ['A', 'C']       ['A', 'C']      ['B', 'A']
Strictly speaking we didn't enforce a unique constraint, so we'll use a loose definition of what a "set" is here (more strictly these would be bags).
If you know the model:
  encoding one pair only takes log2(2) + log2(3) bits = (2.58)
If you use a unigram model:
  A=1/6 B=1/6 C=2/6 D=1/6 E=1/6
  3.73 bits
If you use a model with disjoint sets:
  Data doesn't fit this model CD and BC conflict.