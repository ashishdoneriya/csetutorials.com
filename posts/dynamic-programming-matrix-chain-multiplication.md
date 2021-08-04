---
title: Matrix Chain Multiplication
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Matrix Chain Multiplication problem types
permalink: /dynamic-programming-matrix-chain-multiplication.html
categories:
  - data-structures-and-algorithms
tags:
  - dynamic-programming
---

<https://www.youtube.com/watch?v=D7AFvtnDeMU&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=33>


## Problems

1. MCM
2. Printing MCM
3. Evaluating expression to True / Boolean Parenthesization
4. Min/Max value of an expression
5. Palindrome Partitioning
6. Scramble String
7. Egg. dropping problem



## 1. MCM

Lets say there are two metrics A, B

A = P * Q

B = Q * R

So AB = P * R

No. of multiplication required = P * Q * R

The two metrics can only be multiplied if one dimension common in both metrics.

In metrics chain multiplication problem there are dimensions of metrics given in the form of array. We have to find the most efficient way to multiply these matrices together. The problem is not actually to perform the multiplications, but merely to decide in which order to perform the multiplications.

<https://www.geeksforgeeks.org/matrix-chain-multiplication-dp-8/>

```java
Input: p[] = {40, 20, 30, 10, 30}   
Output: 26000  
There are 4 matrices of dimensions 40x20, 20x30, 30x10 and 10x30.
Let the input 4 matrices be A, B, C and D.  The minimum number of 
multiplications are obtained by putting parenthesis in following way
(A(BC))D --> 20*30*10 + 40*20*10 + 40*10*30
```

### Recursive Approach

```java
/* A naive recursive implementation that simply follows
   the above optimal substructure property */
class MatrixChainMultiplication {
	// Matrix Ai has dimension p[i-1] x p[i] for i = 1..n
	static int MatrixChainOrder(int p[], int i, int j) {
		if (i == j)
			return 0;

		int min = Integer.MAX_VALUE;

		// place parenthesis at different places between
		// first and last matrix, recursively calculate
		// count of multiplications for each parenthesis
		// placement and return the minimum count
		for (int k = i; k < j; k++) {
			int count = MatrixChainOrder(p, i, k) 
					+ MatrixChainOrder(p, k + 1, j) 
					+ p[i - 1] * p[k] * p[j];

			if (count < min)
				min = count;
		}

		// Return minimum count
		return min;
	}

	// Driver code
	public static void main(String args[]) {
		int arr[] = new int[] { 1, 2, 3, 4, 3 };
		int n = arr.length;

		System.out.println("Minimum number of multiplications is " 
				+ MatrixChainOrder(arr, 1, n - 1));
	}
}
```



### Memoization Approach

```java
import java.util.Arrays;

class GFG {

	static int[][] dp = new int[100][100];

	// Function for matrix chain multiplication
	static int matrixChainMemoised(int[] p, int i, int j) {
		if (i == j) {
			return 0;
		}
		if (dp[i][j] != -1) {
			return dp[i][j];
		}
		dp[i][j] = Integer.MAX_VALUE;
		for (int k = i; k < j; k++) {
			dp[i][j] = Math.min(dp[i][j],
						matrixChainMemoised(p, i, k) 
						+ matrixChainMemoised(p, k + 1, j) + p[i - 1] * p[k] * p[j]);
		}
		return dp[i][j];
	}

	static int MatrixChainOrder(int[] p, int n) {
		int i = 1, j = n - 1;
		return matrixChainMemoised(p, i, j);
	}

	// Driver Code
	public static void main(String[] args) {

		int arr[] = { 1, 2, 3, 4 };
		int n = arr.length;

		for (int[] row : dp)
			Arrays.fill(row, -1);

		System.out.println("Minimum number of multiplications is " 
		+ MatrixChainOrder(arr, n));
	}
}
```



## 5. [Palindrome Partitioning Recursive](https://www.youtube.com/watch?v=szKVpQtBHh8&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=36)

<https://www.geeksforgeeks.org/palindrome-partitioning-dp-17/>

Given a string, a partitioning of the string is a *palindrome partitioning* if every substring of the partition is a palindrome. For example, “aba|b|bbabb|a|b|aba” is a palindrome partitioning of “ababbbabbababa”. Determine the fewest cuts needed for a palindrome partitioning of a given string. For example, minimum of 3 cuts are needed for “ababbbabbababa”. The three cuts are “a|babbbab|b|ababa”. If a string is a palindrome, then minimum 0 cuts are needed. If a string of length n containing all different characters, then minimum n-1 cuts are needed. 

## 6. [Scrambled String](https://www.youtube.com/watch?v=SqA0o-DGmEw)

<https://www.geeksforgeeks.org/check-if-a-string-is-a-scrambled-form-of-another-string/>

