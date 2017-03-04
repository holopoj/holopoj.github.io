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
Strictly speaking we didn't enforce a unique constraint, so we'll use a loose definition of what a "set" is here (more strictly these would be called bags, or [multisets](https://en.wikipedia.org/wiki/Multiset)).  If we know that this is the data generating procedure, we can encode messages about sets generated from this procedure more efficiently than simply knowing the alphabet of items (A,B,C,D,E), and more efficiently than knowing their probabilities of appearing in a message $$(A=1/2\times 1/3 = 1/6, B=1/6, D=1/6, E=1/6, C=1/2 \times 1/3 + 1/2 \times 1/3 = 1/3)$$. 

So what would we expect our average message lenght to be using optimal codes, but asssuming each letter is equally likely?  It would simply be $$-(log_2 1/5 + log_2 1/5)\approx 4.64$$ bits.  And if we knew the probabilities of each letter? This gets trickier because 'C' appears in both our generating sets.  First, we can recognize the symmetry between sets m1 and m2 and the fact that sets generated from them are equally likely.  So the encoding length for a message from m1 should equal the encoding length from a message from m2. We need only analyze one of them in detail. XXX
Encoding an 'A', 'B', 'D', or 'E' will require $$-log_2 1/6\approx 2.58$$ bits, and encoding a 'C' will require $$-log_2 1/3\approx 1.58$$ bits.  We now determine the expectation of encoding length for one element: 

$$-(\mathbb{E}[A,B,D, or E] \times 2.58 + \mathbb{E}[C] \times 1.10)
= -((1/6 \times 4) \times 2.58 + 1/3 \times 1.58)
\approx 2.25 
$$
Since we need to encode two elements, total message length will be $$2 \times 2.25 = 4.5$$.  So we did in fact save some bits over the encoding scheme that treats each letter equally likely, which required 4.64 bits.  So what was different between these scenarios?  In the first, we assumed the only information we shared with the sender ahead of time was the alphabet.  In the second, we assumed we also shared knowledge about the probabilities of each letter.  However, you may have noticed, we ignored updated information we had about the second set item, once we already decoded the first item.  If the first item decoded was a 'D', then there is no way the second item can be an 'A', or a 'B'.  Let's determine the expected encoding length for messages when we take advantage of this observation.  The encoding of the first item will still be the same: $$2.25$$ bits.  Now for the second item, thankfully both m1 and m2 are symmetric so the encoding length is the same. We need to determine the probabilities for the second item, given the first item, which we will call $$x_1$$.  If $$x_1$$ is a 'C', then we learn nothing about the second item, $$x_2$$, ('C' is equally likely for both m1 and m2, otherwise we would learn a little something), and the encoding for $$x_2$$ is the same as before: 1.56 bits.  If $$x_1$$ is not a 'C', then we know which generating set was used, either m1 if $$x_1 \in {A,B}$$ or m2 if $$x_1 \in {D,E}$$.  Then each of the three items in the generating set are equally likely to come next.  Let's bring these pieces of information together to compute the expected encoding length for the second item: $$-1/3 \times 2.25 - 2/3 log_2 1/3 \approx 1.81 $$
Bringing our total for encoding both items to $$2.25 + 1.81 = 4.06$$ bits. So we saved even more than just knowing the probabilities, which encoded in 4.5 bits.

But wait, there's more.  We can ask the sender to inform us about whether m1 or m2 was chosen before sending any information about the items.  Then we can encode each item in only $$-log_2 1/3 \approx 1.58$$ bits.  Encoding of the choice between m1 or m2 is 1 bit, because each is equally likely.  So, our total encoding length will be $$1 + 1.58 \times 2 = 4.16$$ bits.  Another significant improvement.  In fact, this is the optimal encoding scheme for this data generating method, of course, assuming you can send fractions of a bit at a time.


KL Divergence

With uniqueness

Given enough generated smaples can we determine the generating scheme.

If you know the model:
  encoding one pair only takes log2(2) + log2(3) bits = (2.58)
If you use a unigram model:
  A=1/6 B=1/6 C=2/6 D=1/6 E=1/6
  3.73 bits
If you use a model with disjoint sets:
  Data doesn't fit this model CD and BC conflict.