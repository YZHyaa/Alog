
## [343. 整数拆分²](https://leetcode-cn.com/problems/integer-break/)

给定一个正整数 *n*，将其拆分为**至少**两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。

**示例 1:**

```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1。
```

**示例 2:**

```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36。
```

**说明:** 你可以假设 *n* 不小于 2 且不大于 58。

### 解法一：分治（超时）

分解模型（==>求所有约数(分解后的)最大乘积）。递推公式，`f(n) = max(i*(n-i), i*f(n-i)) （i∈[0,n]）`
> 注：`i*(n-i)` 表示(n-i)不用分解的情况
```java
class Solution {
    public int integerBreak(int n) {
        return work(n);
    }

    private int work(int n) {
        if (n == 1) return 1;

        int res = 1;
        for (int i = 1; i < n; i++) {
			// 只能有一个最值，所以res也要比较
            res = max(res, i * (n - i), i * work(n - i)); 
        }
        return res;
    }
	
	// 辅助函数，求三个数的最大值
    private int max(int x, int y, int z) {
        return Math.max(Math.max(x, y), z);
    }
}
```

### 解法二：记忆递归

```java
class Solution {
    public int integerBreak(int n) {
        return work(n,new int[n + 1]);
    }

    private int work(int n, int[] memo) {
        if (n == 1) return 1;

        if (memo[n] == 0) {
            for (int i = 1; i < n; i++) 
            memo[n] = max(memo[n], i * (n - i), i * work(n - i, memo)); 
        }
        return memo[n];
    }

    private int max(int x, int y, int z) {
        return Math.max(Math.max(x, y), z);
    }
}
```

### 解法三：动态规划

* 思路：
  * 状态数组：`d[i]`。分割出的 i 可拆分出的最大乘积
  * 初始状态：`d(1) = 1`。1 分割出的最大乘积就是 1 
  * 状态方程：`d(i) = max(d(1-i）* nums[i]，d(i))`。这里一定注意，别忘了不分割也可能是最大
  * 最终状态：`d[n]`。
```java
class Solution {
    public int integerBreak(int n) {
        int[] memo = new int[n + 1];
        memo[1] = 1;
        for (int i = 2; i <= n; i++) 
            for (int j = 1; j < i; j++) 
                memo[i] = max(memo[i], j * (i - j), j * memo[i - j]);
        return memo[n];
    }
    private int max(int x, int y, int z) {
        return Math.max(Math.max(x, y), z);
    }
}
```
不能将空间复杂度优化到O（1），因为每个状态在后面都可能用上


## [279. 完全平方数²](https://leetcode-cn.com/problems/perfect-squares/)

给定正整数 *n*，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 *n*。你需要让组成和的完全平方数的个数最少。

**示例 1:**

```
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```

**示例 2:**

```
输入: n = 13
输出: 2
解释: 13 = 4 + 9.
```

### 解法：动态规划
* 思路：分解模型&&重复子问题（==>求每个因数的可分解的最少完全平方数）
	* 状态数组： `d[i]`。数字 i 分解出的最少完全平方数
	* 初始状态：`d[0]=0`。0不能分解出完全平方数
	* 状态方程：`d[i] = min(d[i - j*j]) + 1 (j*j<=i)`。不断对数字i进行分解，取最小
	* 最终状态：`d[n]`。
* 复杂度：
	* Time：O(n ^ 3/2)
	* Space: O(n)

```java
    public int numSquares(int n) {
        // 状态数组
        int[] d = new int[n + 1];

        // 初始状态
        d[0] = 0;

        // 状态递推
        for (int i = 1; i <= n; i++) {
            // 注：这里要先给d[i]最大值，不然d[i]=0
            d[i] = i;
            for (int j = 1; j*j <= i; j++) 
                d[i] = Math.min(d[i], d[i - j*j] + 1);
        } 
        
        // 最终状态：d[n]
        return d[n];
    }
```
