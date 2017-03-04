---
layout: post
title: Set Compression Part 2
published: true
---
{% include mathjs %}
WORK IN PROGRESS
We're continuing the series on set compression started in the [previous post](../Set-Compression-I).  We saw how knowing the probabilities of ingredients could help us compress recipes.  However, we may be able to do better.  Imagine for instance someone could tell us first that this recipe is an Italian recipe, then we could use a different set of probabilities, associated with Italian recipes, and the encoding size of the recipe will likely be even shorter.  If there were say 32 types of cuisines, each equally likely, then the sender can inform us about the cuisine using $$log_2 32 = 5$$ bits.  As long as the the encoding of the ingredients can be sent using at least 5 fewer bits, then overall we will have a shorter encoding length. XXX Shorter encoding lengths correspond to better representing the underlying structure of the data.
