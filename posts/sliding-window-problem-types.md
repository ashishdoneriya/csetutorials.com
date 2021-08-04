---
title: Sliding Window Syllabus
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Sliding Window problem types
permalink: /sliding-window-problem-types.html
categories:
  - data-structures-and-algorithms
tags:
  - sliding-window
---

<https://www.youtube.com/watch?v=EHCGAZBbB88&list=PL_z_8CaSLPWeM8BDJmIYDaoQ5zuwyxnfj&index=1>

**Problems :**
1. Maximum Sum Subarray of Size K
2. First Negative number in every Window of Size K
3. Count occurances of Anagrams
4. Maximum of all subarrays of size K
5. Variable Size sliding Window
6. Largest subarray of sum K
7. Longest substring with K unique characters
8. Longest substring without repeating characters
9. Pick Toys
10. Minimum window substring

### 1. [Maximum Sum Subarray of Size K](https://www.youtube.com/watch?v=KtpqeN0Goro&list=PL_z_8CaSLPWeM8BDJmIYDaoQ5zuwyxnfj&index=3)

In this problem wer are given an array and window of size K. We have to find the maximum sum among all windows.

### 2. [First Negative number in every Window of Size K](https://www.youtube.com/watch?v=uUXXEgK2Jh8&list=PL_z_8CaSLPWeM8BDJmIYDaoQ5zuwyxnfj&index=4)

In this problem we have to find the first negative number in every (sliding) window

### 3. [Count occurances of Anagrams](https://www.youtube.com/watch?v=MW4lJ8Y0xXk&list=PL_z_8CaSLPWeM8BDJmIYDaoQ5zuwyxnfj&index=5)

<https://www.geeksforgeeks.org/count-occurrences-of-anagrams/>

Given a word and a text, return the count of the occurrences of anagrams of the word in the text(For eg: anagrams of word for are for, ofr, rof etc.))

Examples: 

```
Input : forxxorfxdofr
        for
Output : 3
Explanation : Anagrams of the word for - for, orf, 
ofr appear in the text and hence the count is 3.

Input : aabaabaa
        aaba
Output : 4
Explanation : Anagrams of the word aaba - aaba, 
abaa each appear twice in the text and hence the
count is 4.
```

