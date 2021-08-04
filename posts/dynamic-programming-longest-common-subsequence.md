---
title: Longest Common Subsequence
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Longest Common Subsequence problem types
permalink: /dynamic-programming-longest-common-subsequence.html
categories:
  - data-structures-and-algorithms
tags:
  - dynamic-programming
---

<https://www.youtube.com/watch?v=4dMlCZTONj8&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=18>

## Problems

1. Longest Common Substring (not subsequence)
2. Print LCS
3. Shortest Common Super Sequence
4. Print SCS
5. Min no. of insertion and deletion
6. Longest Repeating subsequence
7. Length of largest subsequence of which is a substring
8. Subsequence pattern matching
9. Count how many time a appear as subsequence in b
10. Largest Palindrome subsequence
11. Largest Palindrome substring
12. Count of Palindrome Substring
13. Min no. of deletions in a string to make it a Palindrome
14. Min no. of insertions in a string to make it a Palindrome



Given two strings `text1` and `text2`, return *the length of their longest **common subsequence**.* If there is no **common subsequence**, return `0`.

A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

* For example, `"ace"` is a subsequence of `"abcde"`.

A **common subsequence** of two strings is a subsequence that is common to both strings.

Eg. LCS for input Sequences “ABCDGH” and “AEDFHR” is “ADH” of length 3.
LCS for input Sequences “AGGTAB” and “GXTXAYB” is “GTAB” of length 4.

https://leetcode.com/problems/longest-common-subsequence/

### Recursive Approach

For writing recursive code, 3 things are required - 

* Inputs
* How to make input small in recursive call (or branch condition)
* Base condition

In this case the inputs are the 2 strings and their length, which has to be decreased in recursive call. In the recursive method we will check the last characters of the two strings. After that during recursive call we can either decrease first string length or second string length (but not both because it will be covered in these two scenarios) and we will take the max of these two.

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        return lcs(text1.toCharArray(), text2.toCharArray(), 0, 0);
    }
    
    private int lcs(char[] arr1, char[] arr2, int i1, int i2) {
        if (i1 == arr1.length || i2 == arr2.length) {
            return 0;
        }
        if (arr1[i1] == arr2[i2]) {
            return 1 + lcs(arr1, arr2, i1 + 1, i2 + 1);
        } else {
             return Math.max(lcs(arr1, arr2, i1 + 1, i2), lcs(arr1,arr2,i1,i2 + 1));
        }
    }
}
```

### Memoization Approach

```java
class Solution {
	public int longestCommonSubsequence(String text1, String text2) {
		char[] arr1 = text1.toCharArray();
		char[] arr2 = text2.toCharArray();
		return lcs(arr1, arr2, 0, 0, new Integer[arr1.length + 1][arr2.length + 1]);
	}

	private int lcs(char[] arr1, char[] arr2, int i1, int i2, Integer[][] dd) {
		if (i1 == arr1.length || i2 == arr2.length) {
			return 0;
		}
		if (dd[i1][i2] != null) {
			return dd[i1][i2];
		}
		if (arr1[i1] == arr2[i2]) {
			dd[i1][i2] = 1 + lcs(arr1, arr2, i1 + 1, i2 + 1, dd);
		} else {
			dd[i1][i2] = Math.max(lcs(arr1, arr2, i1 + 1, i2, dd), lcs(arr1, arr2, i1, i2 + 1, dd));
		}
		return dd[i1][i2];
	}
}
```

### Top Down Approach

```java
class Solution {
	public int longestCommonSubsequence(String text1, String text2) {
		char[] arr1 = text1.toCharArray();
		char[] arr2 = text2.toCharArray();
		int[][] dd = new int[arr1.length + 1][arr2.length + 1];
		for (int i = 0; i <= arr1.length; i++) {
            for (int j = 0; j <= arr2.length;j++) {
              	if (i == 0 || j == 0) {
                  dd[i][j] = 0;
                  continue;
                }
                if (arr1[i - 1] == arr2[j - 1]) {
                    dd[i][j] = dd[i - 1][j - 1] + 1;
                } else {
                    dd[i][j] = Math.max(dd[i - 1][j], dd[i][j - 1]);
                }
            }
        }
        return dd[arr1.length][arr2.length];
	}
}
```



## 2. [Largest Common Substring](https://www.youtube.com/watch?v=HrybPYpOvz0&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=23)

https://leetcode.com/problems/maximum-length-of-repeated-subarray/

[1,2,3,2,1]
[3,2,1,4,7]

|       | 0    | 3    | 2    | 1    | 4    | 7    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| **0** | 0    | 0    | 0    | 0    | 0    | 0    |
| **1** | 0    | 0    | 0    | 1    | 0    | 0    |
| **2** | 0    | 0    | 1    | 0    | 0    | 0    |
| **3** | 0    | 1    | 0    | 0    | 0    | 0    |
| **2** | 0    | 0    | 2    | 0    | 0    | 0    |
| **1** | 0    | 0    | 0    | 3    | 0    | 0    |



```java
class Solution {
    public int findLength(int[] nums1, int[] nums2) {
		int[][] dd = new int[nums1.length + 1][nums2.length + 1];
        for (int i = 0; i <= nums1.length; i++) {
              dd[i][0] = 0;
        }
         for (int j = 0; j <= nums2.length; j++) {
              dd[0][j] = 0;
        }
        int max = 0;
		for (int i = 1; i <= nums1.length; i++) {
            for (int j = 1; j <= nums2.length;j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dd[i][j] = dd[i - 1][j - 1] + 1;
                } else {
                    dd[i][j] = 0;
                }
                 max = Math.max(max, dd[i][j]);
            }
        }
        return max;
    }
}
```



##  3. [Print LCS](https://www.youtube.com/watch?v=x5hQvnUcjiM&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=23) (Longest Common Subsequence string)

```java
class Solution {
	public static void main(String[] args) {
		System.out.println(longestCommonSubsequence("acdaf", "acbcf"));
	}
	public static String longestCommonSubsequence(String text1, String text2) {
		char[] arr1 = text1.toCharArray();
		char[] arr2 = text2.toCharArray();
		int[][] dd = new int[arr1.length + 1][arr2.length + 1];
		for (int i = 0; i <= arr1.length; i++) {
			for (int j = 0; j <= arr2.length; j++) {
				if (i == 0 || j == 0) {
					dd[i][j] = 0;
					continue;
				}
				if (arr1[i - 1] == arr2[j - 1]) {
					dd[i][j] = dd[i - 1][j - 1] + 1;
				} else {
					dd[i][j] = Math.max(dd[i - 1][j], dd[i][j - 1]);
				}
			}
		}
		int maxLength = dd[arr1.length][arr2.length];
		if (maxLength == 0) {
			return "";
		}
		char[] subsequence = new char[maxLength];
		int i = arr1.length, j = arr2.length, currentIndex = maxLength - 1;
		while (i > 0 && j > 0 && currentIndex >= 0) {
			if (arr1[i - 1] == arr2[j - 1]) {
				subsequence[currentIndex--] = arr1[i - 1];
				i--;
				j--;
				continue;
			}
			if (dd[i - 1][j] > dd[i][j - 1]) {
				i--;
			} else {
				j--;
			}
		}
		return new String(subsequence);
	}
}
```



## 3. [Shortest Common Super Sequence](https://www.youtube.com/watch?v=823Grn4_dCQ&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=24)

<https://leetcode.com/problems/shortest-common-supersequence/>

Given two strings `str1` and `str2`, return the shortest string that has both `str1` and `str2` as subsequences

Eg.

```
Input: str1 = "abac", str2 = "cab"
Output: "cabac"
Explanation: 
str1 = "abac" is a subsequence of "cabac" because we can delete the first "c".
str2 = "cab" is a subsequence of "cabac" because we can delete the last "ac".
The answer provided is the shortest such string that satisfies these properties.
```

**Solution :** To find the length of the shortest common supersequence among two strings. 

1. Find the common sequence among the two strings.
2. Lets say length of first string is `s1`, second is `s2` and the common sequence is `l`. So the length of the shortest common super sequence is `l + (s1 - l) + (s2 - l)`



## 4. [Print Shortest Common Super Sequence](https://www.youtube.com/watch?v=VDhRg-ZJTuc&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=29)

<https://leetcode.com/problems/shortest-common-supersequence/submissions/>

```java
class Solution {
	public static void main(String[] args) {
		System.out.println(shortestCommonSupersequence("acdcfcq", "acbcfwq")); //acdbcfcwq
	}
	public static String shortestCommonSupersequence(String text1, String text2) {
		char[] arr1 = text1.toCharArray();
		char[] arr2 = text2.toCharArray();
		int[][] dd = new int[arr1.length + 1][arr2.length + 1];
		for (int i = 0; i <= arr1.length; i++) {
			for (int j = 0; j <= arr2.length; j++) {
				if (i == 0 || j == 0) {
					dd[i][j] = 0;
					continue;
				}
				if (arr1[i - 1] == arr2[j - 1]) {
					dd[i][j] = dd[i - 1][j - 1] + 1;
				} else {
					dd[i][j] = Math.max(dd[i - 1][j], dd[i][j - 1]);
				}
			}
		}
		// longest common subsequence
		int lcsLength = dd[arr1.length][arr2.length];
		// shortest common super sequence
		int scsLength = lcsLength + (arr1.length - lcsLength) + (arr2.length - lcsLength);
		char[] subsequence = new char[scsLength];
		int i = arr1.length, j = arr2.length, currentIndex = scsLength - 1;
		while (i > 0 && j > 0 && currentIndex >= 0) {
			if (arr1[i - 1] == arr2[j - 1]) {
				subsequence[currentIndex--] = arr1[i - 1];
				i--;
				j--;
				continue;
			}
			if (dd[i - 1][j] > dd[i][j - 1]) {
				subsequence[currentIndex--] = arr1[i - 1];
				i--;
			} else {
				subsequence[currentIndex--] = arr2[j - 1];
				j--;
			}
		}
		while (i > 0) {
			subsequence[currentIndex--] = arr1[i - 1];
			i--;
		}
		while (j > 0) {
			subsequence[currentIndex--] = arr2[j - 1];
			j--;
		}
		return new String(subsequence);
	}
}
```



## 5. [Min no. of insertion and deletion](https://www.youtube.com/watch?v=-fx6aDxcWyg&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=25)

Lets say we have to convert string a to string b. We have to find minimum number of insertions and deletions in the string a so that it could be converted to string b.

**Solution :** 

min no. of insertions = length(lcs of a & b) + length(b) - length(lcs of a & b)

min no. of deletions = length(lcs of a & b) + length(a) - length(lcs of a & b)



## 6. [Longest Repeating subsequence](https://www.youtube.com/watch?v=hbTaCmQGqLg&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=30)

```java
class Solution {
    public int findLength(int[] nums1) {
    int[] nums2 = nums1.clone();
		int[][] dd = new int[nums1.length + 1][nums2.length + 1];
        for (int i = 0; i <= nums1.length; i++) {
              dd[i][0] = 0;
        }
         for (int j = 0; j <= nums2.length; j++) {
              dd[0][j] = 0;
        }
        int max = 0;
		for (int i = 1; i <= nums1.length; i++) {
            for (int j = 1; j <= nums2.length;j++) {
                if (nums1[i - 1] == nums2[j - 1] && i != j) {
                    dd[i][j] = dd[i - 1][j - 1] + 1;
                } else {
                    dd[i][j] = 0;
                }
                 max = Math.max(max, dd[i][j]);
            }
        }
        return max;
    }
}
```



## 8. [Subsequence pattern matching](https://www.youtube.com/watch?v=QVntmksK2es&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=31)

Check whether string a is present as a subsequence in string b.

It can be solved using 2 pointers. DP not necessary



## 10. [Largest Palindrome subsequence](https://www.youtube.com/watch?v=wuOOOATz_IA&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=26)

<https://leetcode.com/problems/longest-palindromic-subsequence/>

Given a string `s`, find *the longest palindromic **subsequence**'s length in* `s`.

A **subsequence** is a sequence that can be derived from another sequence by deleting some or no elements without changing the order of the remaining elements.

Example:

```
Input: s = "bbbab"
Output: 4
Explanation: One possible longest palindromic subsequence is "bbbb".
```

**Solution :** Create reverse of the given string and find the longest common subsequence between the string and its reverse.

## 13. [Min no. of deletions in a string to make it a Palindrome](https://www.youtube.com/watch?v=CFwCCNbRuLY&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=28)

**Solution :** Create reverse of the given string and find the longest common subsequence between them.

Min no. deletions = length(string) - length(Largest Palindrome subsequence)

## 14. [Min no. of insertions in a string to make it a Palindrome](https://www.youtube.com/watch?v=AEcRW4ylm_c&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=32)

Create reverse of the given string, find the length of shortest super sequence. 

Min no. of Insertions = length(shortest super sequence) - length(string)

**OR**

Min no. of Insertions = Min no. deletions

