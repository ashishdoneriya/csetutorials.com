---
title: Unbounded Knapsack
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Unbounded Knapsack problem types
permalink: /dynamic-programming-unbounded-knapsack.html
categories:
  - data-structures-and-algorithms
tags:
  - dynamic-programming
---

<https://www.youtube.com/watch?v=aycn9KO8_Ls&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=13>

In 0/1 knapsack problem there was a condition that if you select an iteam then you won't be able to select it again. But in unbounded knapsack there is no such condition. It means we could select an iteam again and again. If an item gives max profit then we should take as many as same items.

**Formula**

```java
t[i][j] = max(val[i - 1] + t[i][j - wt[i - 1]]), t[i - 1][j])
```


## Unbounded Knapsack problems

1. Rod Cutting
2. Coin Change I
3. Coin Change II

### 1. [Rod Cutting Problem](https://www.youtube.com/watch?v=SZqAQLjDsag&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=14)

Given a rod of length n inches and an array of prices that includes prices of all pieces of size smaller than n. Determine the maximum value obtainable by cutting up the rod and selling the pieces. For example, if the length of the rod is 8 and the values of different pieces are given as the following, then the maximum obtainable value is 22 (by cutting in two pieces of lengths 2 and 6) 

### 2. [Coin Change](https://www.youtube.com/watch?v=I4UR2T6Ro3w&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=15)

<https://www.youtube.com/watch?v=DJ4a7cmjZY0>

Given a value N, if we want to make change for N cents, and we have infinite supply of each of S = { S1, S2, .. , Sm} valued coins, how many ways can we make the change? The order of coins doesn’t matter.

<https://leetcode.com/problems/coin-change-2/>



Three cases - 1. Adding current element and sticking to the same element

2. Skipping current element and moving to the next element
3. Adding current element and moving to the next element 

Since the 3rd case is the combination of 1st and 2nd case therefore we will skip case 3.

|         | W = 0 | W = 1 | W = 2 | W = 3 | W = 4 | W = 5 | W = 6 | W = 7 | W = 8 | W = 9 | W = 10 |
| ------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ------ |
| ***0*** | 1     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0      |
| **1**   | 1     | 1     | 1     | 1     | 1     | 1     | 1     | 1     | 1     | 1     | 1      |
| **2**   | 1     | 1     | 2     | 2     | 3     | 3     | 4     | 4     | 5     | 5     | 6      |
| **5**   | 1     | 1     | 2     | 2     | 3     | 4     | 5     | 6     | 7     | 8     | 10     |

### 3. [Coin Change II](https://www.youtube.com/watch?v=I-l6PBeERuc&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=16)

<https://www.youtube.com/watch?v=jgiZlGzXMBw>

Return the fewest number of coins that you need to make up that amount.

https://leetcode.com/problems/coin-change/

