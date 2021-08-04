---
title: Dynamic Programming on Trees
created: 2021-08-01T12:21:21+05:30
updated: 2021-08-01T12:21:21+05:30
author: ashishdoneriya
description: Dynamic Programming on Trees
permalink: /dynamic-programming-on-trees.html
categories:
  - data-structures-and-algorithms
tags:
  - dynamic-programming
---

<https://www.youtube.com/watch?v=qZ5zayHSH2g&list=PL_z_8CaSLPWekqhdCPmFohncHwz8TY2Go&index=47>

## Problems

1. Diameter of a Binary Tree
2. Maximum path sum from any node to any node
3. Maximum path sum from leaf to leaf
4. Diameter of N-array tree

### 1. Diameter of a Binary Tree

<https://leetcode.com/problems/diameter-of-binary-tree/submissions/>

```java
class Solution {
    public int diameterOfBinaryTree(TreeNode root) {
        return getMaxHeightAndDiameter(root)[1];
    }
    
    /**
    *   returns int[] 0th index-> maxHeight, 1st index-> max diameter
    */
    public int[] getMaxHeightAndDiameter(TreeNode node) {
        if (node == null) {
            return new int[]{0, 0};
        }
        int[] leftSubTreeInfo = getMaxHeightAndDiameter(node.left);
        int[] rightSubTreeInfo = getMaxHeightAndDiameter(node.right);
        int[] response = new int[2];
        response[0] = leftSubTreeInfo[0] > rightSubTreeInfo[0] ? leftSubTreeInfo[0] : rightSubTreeInfo[0];
        response[0]++;
        response[1]  = leftSubTreeInfo[0] + rightSubTreeInfo[0];
        if (response[1] < leftSubTreeInfo[1]) {
            response[1] = leftSubTreeInfo[1];
        }
        if (response[1] < rightSubTreeInfo[1]) {
            response[1] = rightSubTreeInfo[1];
        }
        return response;
    }
}
```



### 2. Maximum path sum from any node to any node

<https://leetcode.com/problems/binary-tree-maximum-path-sum/>

```java
class Solution {
    
    int maxPathSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        getMaxHeight(root);
        return maxPathSum;
    }
    
    private int getMaxHeight(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int maxLeftPathSum = getMaxHeight(node.left);
        int maxRightPathSum = getMaxHeight(node.right);
        int temp = Math.max(node.val, node.val + Math.max(maxLeftPathSum, maxRightPathSum));
        int currentPathSum = Math.max(node.val + maxLeftPathSum + maxRightPathSum, temp );
        maxPathSum = Math.max(maxPathSum, currentPathSum);
        return temp;
    }
    
}
```



### 3. Maximum path sum from leaf to leaf


