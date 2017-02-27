---
layout: post
title: First post.
published: false
---
{% include mathjs %}
I'd like to do a series of experiments on compressing sets of discrete items.  The concomitant hope is to discover latent structure within real-world sets.  To give a better idea of what the heck this means, consider a recipe as a set of ingredients:
-     "water",
-      "red pepper flakes",
-      "garlic",
-      "smoked paprika",
-      "ground turmeric",
-      "boneless chicken skinless thigh",
-      "ground black pepper",
-      "diced tomatoes",
-      "ground coriander",
-      "onions",
-      "tomato paste",
-      "garam masala",
-      "sea salt",
-      "cayenne pepper",
-      "coconut milk",
-      "ground cumin",
-      "kosher salt",
-      "vegetable oil",
-      "ginger",
-      "ground cardamom",
-      "chopped cilantro fresh"

If we have a large number of recipes, we can start to see patterns, because recipes are not just random selections of ingredients, they follow cuisines (Indian, Italian, Mexican, etc.), people make small variations of existing dishes and post them as new recipes, and because some ingredients just work better together.  These initial experiments will use recipes, because I found a nice collection of them over at Kaggle ([What's cooking?](https://www.kaggle.com/c/whats-cooking)), though hopefully the techniques I explore will apply to any sets with correlation and structure to them, such as the words in documents, or customer's baskets of goods as in [association rule mining](https://en.wikipedia.org/wiki/Association_rule_learning).  

My goal is to learn hierarchial structures over the patterns of ingredient usage based on ideas from compression.  For instance, if you just send me a recipe using text it will take however many bytes the text takes.  If you and I agree on a set of ingredients that can be used ahead of time to send any possible recipe, then you can send me a recipe by just saying Ingredient 5067, Ingredient 6403, etc.  If we agree on a binary format and there are say 6,714 ingredients, then each ingredient can be sent in $$log_2 4096 \approx 12.7$$ bits.

The first optimization we can make is to take into account that each ingredient is not eually likely.  "Onions" appear in many more recipes than does "ground turmeric" for instance.  What we need is a large dataset of recipe ingredient lists, so that we can calculate frequencies for each unique ingredient.  We start by downloading the ([What's cooking?](https://www.kaggle.com/c/whats-cooking)) data to the local disk.  The data is split into train.json and test.json.  We'll start with the train.json, which has 39,774 recipes. The data looks like this:

```
[
  {
    "id": 10259,
    "cuisine": "greek",
    "ingredients": [
      "romaine lettuce",
      "black olives",
      "grape tomatoes",
      "garlic",
      "pepper",
      "purple onion",
      "seasoning",
      "garbanzo beans",
      "feta cheese crumbles"
    ]
  },
  {
    "id": 25693,
    "cuisine": "southern_us",
    "ingredients": [
      "plain flour",
      "ground pepper",
```

There's no amounts or units or directions, but that's perfect for these experiments which focus on sets of items.  The code to load this in Python looks like this:

```python
import json, codecs, sys
recipeFile = "data/kaggle_whats_cookin/train.json"
instream = codecs.open(recipeFile, encoding='utf-8')
recipes = json.load(instream)
```

This code gives us the recipes in an array.  Counting how many times each ingredient is used, and dividing by the number of times any ingredient is used and sorting, we can find the frequency and probability of each ingredient:

Ingredient | Freq. | Probability
---------- | ----- | -----------
salt | 18049 | 4.2%
onions | 7972 | 1.9%
olive oil | 7972 | 1.9%
water | 7457 | 1.7%
garlic | 7380 | 1.7%
sugar | 6434 | 1.5%
garlic cloves | 6237 | 1.5%
butter | 4848 | 1.1%
ground black pepper | 4785 | 1.1%
all-purpose flour | 4632 | 1.1%

A couple notes.  First, the probability is out of all times any ingredient was mentioned, so even though 18,049 of the 39,774 recipes $$(\approx 45.4\%)$$ of all recipes use salt, only 4.2% of all ingredient slots of any recipe are filled by salt.  Recipes have more than one ingredient, and they will probably not be repeated.  Second, the ingredients haven't been fully normalized to a single standard name, so near the top of our list we see both "garlic" and "garlic cloves". This is a real issue, but one that we will overlook for now.

The ingredient probabilities allow us to better encode messages about recipes to recipients.  Instead of assigning each ingredient an equal length code, the code lengths can be variable, with more frequent ingredients like onions having a shorter code.  I won't go into the details of codes, but what is important here is that according to Shannon's [coding theory](https://en.wikipedia.org/wiki/Coding_theory), the minimum optimal code length for an item, $$i$$, whose probability is $$p_i$$ (representted as a fraction, between 0 and 1) will be $$-log_2 p_i$$.  So, for "olive oil", we have $$p_{OliveOil}=0.019$$, thus it's code length would be $$-log_2 0.019 \approx 5.72$$ bits. We will gloss over the fact you can't have a non-integer code length.  Recall that before, when we assumed 4096 ingredients, each equally likely, it took 12.7 bits to encode each ingredient.  Across these recipes, there are 482,275 usages of ingredients, so at 12.7 bits each, this would require 6,131,141 bits to encode all ingredients.  With the coding scheme based on ingredient probabilities this is reduced to 4,002,143.



