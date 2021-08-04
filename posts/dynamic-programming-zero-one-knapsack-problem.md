---
title: 0-1 Knapsack Problem
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Dynamic programming problem types
permalink: /dynamic-programming-zero-one-knapsack-problem.html
categories:
  - data-structures-and-algorithms
tags:
  - dynamic-programming
---

To identify whether it is a knapsack problem or not we would be given choice either to add or to ignore numbers.

Knapsack problems are of three type

* Fractional Knapsack
* 0/1 Knapsack
* Unbounded Knapsack

## Ways of Solving Knapsack problems

**Problem :** We are given 2 arrays of weight and value & we have to find max profit (Burgler and the max weight that he could pick)

### Knapsack Recursive

**Solution**

```java
/**
 * vArr is value array
 * wArr is weight array
 * W is weight available
 * arrSize is array size
 *
*/
public int getMaxProfit(int[] vArr, int[] wArr, int W, int arrSize) {
	// base condition
	if (arrSize == 0 || W == 0) {
		return 0;
	}
	int currItemWeight = wArr[arrSize - 1];
	int currIteamProfit = vArr[arrSize - 1];
	if (currItemWeight > W) {
		return getMaxProfit(vArr, wArr, W, arrSize - 1);
	}
	// Two choices either to include this weight or to exclude this weight
  
	// Include this weight
	int max1 = currIteamProfit + getMaxProfit(vArr, wArr, W - currItemWeight, arrSize - 1);
	// Exclude this weight and leave it to the other elements
	int max2 = getMaxProfit(vArr, wArr, W, arrSize - 1);
	// return max weight
	return max1 > max2 ? max1 : max2;
}
```

### Knapsack Memoization

You can also use memoization technique. On analysis we can see only last two variables are changing. Thefore create an array of `dd[numberOfValues + 1][W + 1]` and initialize all cells to -1. So before calling the recursive function check if value exists in array

### Knapsack Topdown

Lets say `vArr` = {3,4,2,5,1} and `wArr` = {1, 2, 2, 4, 2} and max weight is 8 (ie. `W` = 8). To represent it in table/matrix format -
1. Number of rows = N + 1; where N is size of array
2. Number of columns = W + 1;
3. Since for max weight 0 (ie. W = 0), max value/profit is also 0. Therefore initialize all first column values as 0.
4. Since no element is present in first row therefore for any weight profit is 0. Hence initialize all column values in first row as 0.

|                  | W = 0 | W = 1 | W = 2 | W = 3 | W = 4 | W = 5 | W = 6 | W = 7 | W = 8 | W = 9 |
| ---------------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **v = 0, w = 0** | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     |
| **v = 3, w = 1** | 0     |       |       |       |       |       |       |       |       |       |
| **v = 4, w = 2** | 0     |       |       |       |       |       |       |       |       |       |
| **v = 2, w = 2** | 0     |       |       |       |       |       |       |       |       |       |
| **v = 5, w = 4** | 0     |       |       |       |       |       |       |       |       |       |
| **v = 1, w = 2** | 0     |       |       |       |       |       |       |       |       |       |

A single cell represent the max value for a particular weight. Lets say the array is called `dd[N + 1][W + 1]`

the recursive code can be rewritten as

```java
int[][] dd = new int[N + 1][W + 1];
for (int j = 0; j <= W; i++) {
	dd[0][j] = 0;  
}
for (int i = 0; i <= N; i++) {
  dd[i][0] = 0;
}
for (int i = 1; i <= N; i++) {
  for (int j = 1; j <= W; j++) {
  	int currItemWeight = wArr[i - 1];
    int currItemValue = vArr[i - 1];
    // If current item weight is greater then get max value of its predecessor numbers, here j represents the weight
    if (currItemWeigth > j) {
      dd[i][j] = dd[i - 1][j];
      continue;
    }
    int max1 = dd[i - 1][j];
    int max2 = currItemValue + dd[i - 1][j - currItemWeight];
    dd[i][j] = max1 > max2 ? max1 : max2;
  }
}
return dd[N][W];
```





##  Type of Knapsack Problems

1. Subset Sum

2. Equal Sum Partition

3. Count of Subset Sum

4. Minimum Subset Sum Diff

5. Target Sum

6. Number of Subet with given difference

   

### [Subset Sum Problem](https://www.youtube.com/watch?v=_gPcYovP7wc&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=7)

Given a set of non-negative integers, and a value *sum*, determine if there is any subset whose sum equal to given *number (sum)*. 

```bash
Input: set[] = {3, 34, 4, 12, 5, 2}, sum = 9
Output: True  
There is a subset (4, 5) with sum 9.

Input: set[] = {3, 34, 4, 12, 5, 2}, sum = 30
Output: False
There is no subset that add up to 30.
```

In this scenario first column cells are true and the first row values are false. This is because at W = 0 there is a subset always present whose weight is equal to 0 that is empty subset.

|         | W = 0 | W = 1 | W = 2 | W = 3 | W = 4 | W = 5 | W = 6 | W = 7 | W = 8 | W = 9 |
| ------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| ***0*** | T     | F     | F     | F     | F     | F     | F     | F     | F     | F     |
| **3**   | T     |       |       |       |       |       |       |       |       |       |
| **34**  | T     |       |       |       |       |       |       |       |       |       |
| **4**   | T     |       |       |       |       |       |       |       |       |       |
| **12**  | T     |       |       |       |       |       |       |       |       |       |
| **5**   | T     |       |       |       |       |       |       |       |       |       |
| **2**   | T     |       |       |       |       |       |       |       |       |       |

**Solution**

```java
int[] set = new int[]{3, 34, 4, 12, 5, 2};
boolean[][] dd = new boolean[N + 1][W + 1];
for (int j = 0; j <= W; i++) {
	dd[0][j] = 0;
}
for (int i = 0; i <= N; i++) {
  dd[i][0] = 0;
}
for (int i = 1; i <= N; i++) {
  for (int j = 1; j <= W; j++) {]
    int currItemValue = set[i - 1];
    // If current item weight is greater then get max value of it predecessor numbers, here j represents whether set contains subset of particular weight or not
    if (currItemWeight > j) {
      dd[i][j] = dd[i - 1][j];
      continue;
    }
    boolean max1 = dd[i - 1][j];
    boolean max2 = dd[i - 1][j - currItemWeight];
    dd[i][j] = max1 || max2;
  }
}
return dd[N][W];
```



### [Equal Sum Partition](https://www.youtube.com/watch?v=UmMh7xp07kY&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=8)

Given a **non-empty** array `nums` containing **only positive integers**, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

Lets say `total` is the sum of all the elements of array. If `total` is odd number then it is not possible to divide the array into two equal parts.

This problem is much like Subset Sum problem. If we have to divide the array into two equal parts (based on their sum) then it can be safe to say that the sum of one partition is equal to `total / 2`. Now we just have to find out a subset in the array whose sum is equal to `total / 2`. So we can solve this problem using Subset Sum problem.

**Practice : ** <https://leetcode.com/problems/partition-equal-subset-sum/>



#### Memoization

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int total = IntStream.of(nums).sum();
        if (total % 2 > 0) {
            return false;
        }
        Boolean[][] memo = new Boolean[nums.length][total + 1];
        return isSumAvailable(nums, total / 2, 0, memo);
    }
    
    private boolean isSumAvailable(int[] nums, int total, int indexToStart, Boolean[][] memo) {
        if (total == 0) {
            return true;
        }
        if (indexToStart == nums.length) {
            return false;
        }
        if (memo[indexToStart][total] != null) {
            return memo[indexToStart][total];
        }
        if (nums[indexToStart] > total) {
            memo[indexToStart][total] = isSumAvailable(nums, total, indexToStart + 1, memo);
        } else {
            memo[indexToStart][total] = (isSumAvailable(nums, total, indexToStart + 1, memo) || isSumAvailable(nums, total - nums[indexToStart], indexToStart + 1, memo));
        }
        return memo[indexToStart][total];
    }
}
```



#### Top Down DP

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int total = 0;
        for (int num: nums) {
            total += num;
        }
        if (total % 2 == 1) {
            return false;
        }
        int W = total / 2;
        int N = nums.length;
        boolean[][] dd = new boolean[N + 1][W + 1];
        for (int j = 1; j <= W; j++) {
            dd[0][j] = false;
        }
        for (int i = 0; i <= N; i++) {
            dd[i][0] = true;
        }
        for (int i = 1; i <= N; i++) {
            int currNum = nums[i - 1];
            for (int j = 1; j <= W; j++) {
                if (currNum > j) {
                  	// If the current item is greater than j, then it will be dependent on its predecessor items to satisfy the condition
                    dd[i][j] = dd[i - 1][j];
                } else {
                  	// If the current item i
                    dd[i][j] = dd[i - 1][j] || dd[i - 1][j - currNum];
                }
            }
        }
        return dd[N][W];
    }
}
```

### [Count of Subset Sum](https://www.youtube.com/watch?v=F7wqWbqYn9g&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=9)

In the Subset Sum problem we just had to find that whether there is any subset present in the array whose sum is equal to a particular number. In this problem (Count of Subset Sum) we have to find the total number of subsets whose sum is equal to the given number.

Eg. `{2, 3, 5, 6, 8, 10}`. Lets say we have to find total number of subsets whose sum is 10. So there are 3 subsets whose sum is 10 - `{2, 3, 5}`, `{2, 8}`, `{10}`.

In the previous problem we were using or operator. In the problem we will be using sum (+) operator.

|         | W = 0 | W = 1 | W = 2 | W = 3 | W = 4 | W = 5 | W = 6 | W = 7 | W = 8 | W = 9 | W = 10 |
| ------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ------ |
| ***0*** | 1     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0      |
| **2**   | 1     | 0     | 1     | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0      |
| **3**   | 1     | 0     | 1     | 1     | 0     | 1     | 0     | 0     | 0     | 0     | 0      |
| **5**   | 1     | 0     | 1     | 1     | 0     | 2     | 0     | 1     | 1     | 0     | 1      |
| **6**   | 1     | 0     | 1     | 1     | 0     | 2     | 1     | 1     | 2     | 1     | 1      |
| **8**   | 1     | 0     | 1     | 1     | 0     | 2     | 1     | 1     | 3     | 1     | 2      |
| **10**  | 1     | 0     | 1     | 1     | 0     | 2     | 1     | 1     | 3     | 1     | 3      |

```java
public int subarraySum(int[] nums, int target) {
    if (nums == null || nums.length == 0 || target == 0) {
        return 0;
    }
  
    int[][] dd = new int[nums.length + 1][target + 1];
    for (int i = 0; i <= nums.length; i++) {
        dd[i][0] = 1;
    }
    for (int j = 1; j <= target; j++) {
        dd[0][j] = 0;
    }
    for (int i = 1; i <= nums.length; i++) {
        for (int j = 1; j <= target; j++) {
            if (nums[i - 1] > j) {
                dd[i][j] = dd[i - 1][j];
            } else {
                dd[i][j] = dd[i - 1][j] + dd[i - 1][j - nums[i - 1]];
            }
        }
    }
    return dd[nums.length][target];
}
```



### [Minimum Subset Sum Difference](https://www.youtube.com/watch?v=-GtpxG6l_Mc&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=10)

In this problem there is an array given and we have to partition the array into two subsets such that the difference between them is minimum.

The smallest subset value is less than or equal to TotalSumOfArray / 2. Now just use Subset Sum Problem with `dd[arraySize + 1][TotalSumOfArray / 2]` and in the last row from j = TotalSumOfArray / 2 to j = 1 check first true. Then the minimum difference is `TotalSumOfArray - 2 * (last element having true)`

### [Count the number of subset with a given difference](https://www.youtube.com/watch?v=ot_XBHyqpFc&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=11)

This is the combination of Count of Subset sum and Minimum subset sum difference problem.

**Practice:** <https://leetcode.com/problems/target-sum/>

Total - S1 = S2

S1 - S2 = Diff

Total - S2 = S2 + Diff

S2 = (Total - Diff) / 2

```java
class Solution {
	public int findTargetSumWays(int[] nums, int target) {
		if (nums == null || nums.length == 0) {
			return 0;
		}
		if (nums.length == 1) {
			return nums[0] == target ? 1 : 0;
		}
		int total = 0;
		for (int num : nums) {
			total += num;
		}
		target = (total - target) / 2;

		int[][] dd = new int[nums.length + 1][target + 1];
		for (int i = 0; i <= nums.length; i++) {
			dd[i][0] = 1;
		}
		for (int j = 1; j <= target; j++) {
			dd[0][j] = 0;
		}
		for (int i = 1; i <= nums.length; i++) {
			for (int j = 1; j <= target; j++) {
				if (nums[i - 1] > j) {
					dd[i][j] = dd[i - 1][j];
				} else {
					dd[i][j] = dd[i - 1][j] + dd[i - 1][j - nums[i - 1]];
				}
			}
		}
		return dd[nums.length][target];
	}
}
```



### [Target sum](https://www.youtube.com/watch?v=Hw6Ygp3JBYw&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=12)

We have to assign + and - signs in front of elements and after adding the numbers, find number of combinations such that it is equal to the target sum.

**Practice:** <https://leetcode.com/problems/target-sum/>

