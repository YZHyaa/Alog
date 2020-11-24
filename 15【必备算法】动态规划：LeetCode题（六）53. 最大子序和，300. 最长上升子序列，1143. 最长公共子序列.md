## [53. 最大子序和¹](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例:**

```
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

### 解法：动态规划

* 思路：分解模型且存在重复子问题（==>求每个求每个位置的最大子序和，然后找最值）。递推公式，`problem(i) = max(problem(i - 1), 0) + nums[i]`，
  * 状态数组：`d[i]`。到第i位时的最大子序和
  * 初始状态：`d[0] = nums[0]`。第一个元素的最大子序和就是自己。
  * 状态方程：`d[i] = max(d[i - 1], 0) + nums[i]`。当前的子序和 = 前一位子序和大于0 ？d[i-1]+当前值 : 当前值
  * 最终状态：状态数组中最大的那个
* 复杂度
  * Time：O（n）
  * Space：O（n）

```java
public int maxSubArray(int[] nums) {
        int[] d = new int[nums.length];
        int max = Integer.MIN_VALUE;
    	// 初始状态
        d[0] = nums[0];
        // 状态递推
        for (int i = 1; i < nums.length; i++) 
            d[i] = Math.max(0, d[i - 1]) + nums[i];
    	// 找到最大的d[i]
        for (int i = 0; i < nums.length; i++) 
            max = Math.max(max, d[i]);
        return max;
}
```
这里可以对状态数组进行优化，优化成单个变量
```java
//  Time：O（n） Space：O（1）
public int maxSubArray(int[] nums) {
        int max = Integer.MIN_VALUE, cur = 0;
        for (int i = 0; i < nums.length; i++) {
            cur = cur <= 0 ? nums[i] : (cur + nums[i]);
            max = Math.max(max, cur);
        }
        return max;
}
```

## [300. 最长上升子序列²](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

给定一个无序的整数数组，找到其中最长上升子序列的长度。

**示例:**

```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```

**说明:**

- 可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
- 你算法的时间复杂度应该为 O(*n2*) 。

**进阶:** 你能将算法的时间复杂度降低到 O(*n* log *n*) 吗?

### 解法：动态规划
>注意，这里区分三个概念：
>* 子串："abc"的子串不包括“ac”
>* 子序列：[1,2,3]的子序列包括[1,3]
>* 连续子数组：[1,2,3]的连续子数组不包括[1,3]

* 思路：分解模型且存在重复子问题（==>求每个位置的最长上升序列长度，然后找最值）。递推公式，`problem(i) = problem(前一个比它小的元素) + 1	`。
  * 状态数组：`d[i]`。到第 i 位时的上升序列长度
  * 初始状态：` d[0] = nums[0]` ,`d[1] =  max(nums[0], nums[1])`。因为d[2]可能同时大于d[0]和d[1]，所以也要初始
  * 状态方程：`d(i) = max(d(i),d(前一个小)+1)`。本来是`d(i) = d(前一个比他小的元素) + 1` ，但可能存在多个比当前元素小的
  * 最终状态：状态数组中最大的那个

```java
public int lengthOfLIS(int[] nums) {
        if (nums.length == 0) return 0;

        int[] memo = new int[nums.length];
        // 初始状态
        for (int i = 0; i < nums.length; i++)
            memo[i] = 1;
        // 状态递推
        for (int i = 1; i < nums.length; i++) 
        	// 因为可能有多个小于当前元素的，所以要遍历[0,i]去找他们中d[i]最大的
            for (int j = 0; j < i; j++) 
                if (nums[j] < nums[i]) 
                    memo[i] = Math.max(memo[j] + 1, memo[i]);
        // 寻找最大的d[i]
        int res = 1;
        for (int i = 0; i < nums.length; i++) 
            res = Math.max(res, memo[i]);
        return res;
}
```
本题不能优化状态数组成单个变量，因为当前状态的递推需要前面所有的状态

## [1143. 最长公共子序列²](https://leetcode-cn.com/problems/longest-common-subsequence/)

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长公共子序列的长度。

一个字符串的 *子序列* 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。 

**示例 1:**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace"，它的长度为 3。
```

**示例 2:**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc"，它的长度为 3。
```

**示例 3:**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0。
```

**提示:**

- `1 <= text1.length <= 1000`
- `1 <= text2.length <= 1000`
- 输入的字符串只含有小写英文字符。

### 解法：动态规划

* 思路：分解模型&&重复子问题（==>求所有中间串的最长子序列）
	* 状态定义：`d[i][j]`。s1.sub(1,i)与s2.sub(1,j)的最长子序列（从1开始是要包含空串情况）
	* 初始状态：`d[i][0]=0, d[0][j]=0`。任意一个串长度为0，则无公共子序列（注：由于数组默认初始值为0，所以不用再显式声明）
	* 状态方程：`d[i][j]= s1.charAt(i) == s2.charAt(j) ? d[i-1][j-1] + 1 : max(d[i-1][j],[i][j-1])`
		* 若char(i)=char(j)，则长度+1
		* 若char(i)!=char(j)，则必有一个字符或两个字符（该情况已包含）都不在子序列中
	* 最终状态：`d[s1.len][s2.len]`
* 复杂度：
	* Time：O(n * m)
	* Space：O(n * m)

```java
public int longestCommonSubsequence(String text1, String text2) {
        // 状态数组
        int[][] d = new int[text1.length() + 1][text2.length() + 1];

        // 状态初始：d[i][0]=0, d[0][j]=0。不用再显式声明。
        
        // 状态递推
        for (int i = 1; i <= text1.length(); i++) 
            for (int j = 1; j <= text2.length(); j++) 
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) 
                    d[i][j] = d[i - 1][j - 1] + 1;
                else
                    d[i][j] = Math.max(d[i - 1][j], d[i][j - 1]);

        // 最终状态：d[s1.len][s2.len]
        return d[text1.length()][text2.length()];
    }         
```
