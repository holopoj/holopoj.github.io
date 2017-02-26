---
layout: post
title: First post.
published: true
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

My goal is to learn hierarchial structures over the patterns of ingredient usage based on ideas from compression.  For instance, if you just send me a recipe using text it will take however many bytes the text takes.  If you and I agree on a set of ingredients that can be used ahead of time to send any possible recipe, then you can send me a recipe by just saying Ingredient 5067, Ingredient 6403, etc.  If we agree on a binary format and there are say 4096 ingredients, then each ingredient can be sent in $$log_2 4096=14$$ bits.
