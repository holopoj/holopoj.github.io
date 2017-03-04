---
layout: post
title: Set Compression Part 2
published: true
---
{% include mathjs %}

WORK IN PROGRESS
We're continuing the series on set compression started in the [previous post](../Set-Compression-I).  We saw how knowing the probabilities of ingredients could help us compress recipes.  However, we may be able to do better.  Imagine for instance someone could tell us first that this recipe is an Italian recipe, then we could use a different set of probabilities, associated with Italian recipes, and the encoding size of the recipe will likely be even shorter.  If there were say 32 types of cuisines, each equally likely, then the sender can inform us about the cuisine using $$log_2 32 = 5$$ bits.  As long as the the encoding of the ingredients can be sent using at least 5 fewer bits, then overall we will have a shorter encoding length. Shorter encoding lengths correspond to better representing the underlying structure of the data. To see this, let's consider the following example.  Below we outline a data generating procedure for sets with two elements.
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
Strictly speaking we didn't enforce a unique constraint, so we'll use a loose definition of what a "set" is here (more strictly these would be called bags, or [multisets](https://en.wikipedia.org/wiki/Multiset)).  If we know that this is the data generating procedure, we can encode messages about sets generated from this procedure more efficiently than simply knowing the alphabet of items (A,B,C,D,E), and more efficiently than knowing their probabilities of appearing in a message $$(A=1/2\times 1/3 = 1/6, B=1/6, D=1/6, E=1/6, C=1/2 \times 1/3 + 1/2 \times 1/3 = 1/3). 

So what would we expect our average message lenght to be using optimal codes, but asssuming each letter is equally likely?  It would simply be $$-(log_2 1/5 + log_2 1/5)\approx 3.22$$ bits.  And if we knew the probabilities of each letter? This gets trickier because 'C' appears in both our generating sets.  First, we can recognize the symmetry between sets m1 and m2 and the fact that sets generated from them are equally likely.  So the encoding length for a message from m1 should equal the encoding length from a message from m2. We need only analyze one of them in detail. XXX
Encoding an 'A', 'B', 'D', or 'E' will require $$-log_2 1/6\approx 1.79$$ bits, and encoding a 'C' will require $$-log_2 1/3\approx 1.10$$ bits.  We now determine the expectation of encoding length for one element: 

$$-(\mathbb{E}[A,B,D, or E] \times 1.79 + \mathbb{E}[C] \times 1.10)
= -((1/6 \times 4) \times 1.79 + 1/3 \times 1.10)
\approx 1.56 
$$
Since we need to encode two elements, total message length will be $$2 \times 1.56 = 3.12$$.  So we did in fact save some bits over the encoding scheme that treats each letter equally likely.  So what was different between these scenarios?  In the first, we assumed the only information we shared with the sender ahead of time was the alphabet.  In the second, we assumed we also shared knowledge about the probabilities of each letter.  However, you may have noticed, we ignored updated information we had about the second set item, once we already decoded the first item.  If the first item decoded was a 'D', then there is no way the second item can be an 'A', or a 'B'.  Let's determine the expected encoding length for messages when we take advantage of this observation.


KL Divergence

If you know the model:
  encoding one pair only takes log2(2) + log2(3) bits = (2.58)
If you use a unigram model:
  A=1/6 B=1/6 C=2/6 D=1/6 E=1/6
  3.73 bits
If you use a model with disjoint sets:
  Data doesn't fit this model CD and BC conflict.