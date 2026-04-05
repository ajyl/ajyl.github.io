---
layout: post
title: 'Understanding the Role of MLP "Value Vectors"'
date: 2025-02-28 23:00:00 -0000
---

# Context

This is Part II of investigating how R1 does verifications.
For additional context, see [Part I](https://ajyl.github.io/2025/02/16/steering-R1.html).

# How does R1 know it's found a solution?

R1 will often claim that it's found a solution.
How does it represent the solution, or even the fact that it has found a solution?

Currently I am working with a hypothesis that R1 has some internal representation for "I've found and verified a solution" (let's call this TODO), and that this representation generates "solution tokens" (such as "Therefore the answer is...").

Here I present some of my thoughts on the latter part -- how "solution tokens" are generated.


## (Recap from [Part I](https://ajyl.github.io/2025/02/16/steering-R1.html) Model:

As a reminder, we are working with a model trained on a specific reasoning task (a countdown task).
Given 3 or 4 "operand" numbers (ex: 67, 90, 60, 12) and a target number (ex: 49), R1 needs to find an arithmetic combination that produces the target number.
See [Part I](https://ajyl.github.io/2025/02/16/steering-R1.html) for details.

Luckily, this means we know the exact token to look for when the model has found a solution.
Namely, the model always outputs in a structured format:

```
- 90 - 67 - 60 + 12 = -15 (not 49)
- 90 - 67 + 60 - 12 = 71 (not 49)
- 90 - 67 + 12 - 60 = -15 (not 49)
- 90 - 60 - 12 + 67 = 85 (not 49)
- 67 + 60 - 90 - 12 = 25 (not 49)
- 67 + 90 - 60 - 12 = 85 (not 49)
- 67 + 90 + 12 - 60 = 109 (not 49)
- 67 + 60 + 12 - 90 = 49 (this works)
So, the equation that equals 49 is 67 + 60 + 12 - 90. </think>
<answer> (67 + 60 + 12) - 90 </answer><|endoftext|>
```

Thus, we have a specific timestep (right when a "(" is predicted, and when "this" will be predicted next) where we can do a deep dive. 

## Good old Logitlens


So let's start by seeing what's encoded at each layer, right before "this works" is generated.
Here's an example:

(I recommend that you view it via this interactive [html link]("/assets/logitlens_this.html"))

<img src="{{ '/assets/blogs/mlp_value_vectors/logitlens_this.png' | relative_url }}" style="width: 50%; height:auto;">




So we see some interesting tokens show up in mid layers, like "success", "OK", "bingo", "yes".
In the later layers, the model decides on predicting "this".

Some possibilities:

(1) The same representation is promoting the mid layer tokens ("success", "OK", "bingo", "yes") as well as the actual predicted tokens ("this").

(2) The representation 

