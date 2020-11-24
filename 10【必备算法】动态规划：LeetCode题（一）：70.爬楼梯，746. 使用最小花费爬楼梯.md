
##  [70.爬楼梯¹](https://leetcode-cn.com/problems/climbing-stairs/)


假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意**：给定 *n* 是一个正整数。

**示例 1：**
```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

**示例 2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```


### 解法一：分治（超时）
* 思路：分解模型（==> 求爬到每个楼梯有多少种爬法）。递推公式，`f(n)  = f(n-1) + f(n-2)`。
* 复杂度
	* Time：O(2^n)，递归树有`2^n`个节点，每个节点都要计算
	* Space：O(2^n)
```java
public int climbStairs(int n) {
        return climb(n);
}

public int climb(int n) {
    if (n == 1 || n == 0) return 1;
    return climb(n-1) + climb(n-2);
}
```

![](https://img-blog.csdnimg.cn/20201019162630424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

由于递归的重复计算过多，导致递归树重复节点过多，故而超时。
### 解法二：记忆递归
* 思路：**空间换时间**，引入一个数组记录每次产生的中间结果。后面在每次调用函数时如果数组中有，就直接返回，若没有则计算并放入数组。
* 复杂度：
	* Time：O(n)，此时递归树只有n个节点，因为不存在重复计算
	* Space：O(n)

```java
public int climbStairs(int n) {
    	// 定义数组，保存中间结果
        int[] tmp = new int[n + 1];
        return climb2(n,tmp);
}

public int climb2(int n, int[] tmp) {
        if (n == 1 || n == 0) return 1;
    	// 若数组中有就直接返回
        if (tmp[n] != 0) return tmp[n];
        // 没有，计算并放入数组    
        tmp[n] = climb2(n - 1,tmp) + climb2(n - 2,tmp);
        return tmp[n];
}
```
### 解法三：动态规划
* 分解模型&&重复子问题。
	* 状态数组：`d[i]`。到每个台阶i有多少种走法。
	* 初始状态：`d[0]=1, d[1]=1`。上第零级（d[0]本质是一个辅助变量）和第一级的走法确定。
		>注意，与斐波那契数列的区别，本题是 1,2,3,5；斐波那契是 0,1,1,2,3,5
	* 状态方程：`d[i] = d[i-1] + d[i-2]`。由上面递推公式就可以得到。
	* 最终状态：`d[n]`
* 复杂度
	* Time：O(n)
	* Space：O(n)，需要一个状态数组

```java
public int climbStairs(int n) {
		// 状态数组。注意这里大小是n+1
        int[] d = new int[n+1];
        // 初始状态
        d[0] = 1; d[1] = 1;
        // 状态递推
        for (int i = 2; i <= n; i++) 
            d[i] = d[i-1] + d[i-2];
        // 最终状态
        return d[n];
    }
```
不难发现，在状态递推时始终用到的变量只有两个（d[i],d[i-1],d[i-2]），所以我们可以把状态数组进行优化，优化成三个状态变量(f1,f2,f3)。从而，时空间复杂度从O(n)降到O(1)。
```java
// Space：O(1)
public int climbStairs(int n) {
       int f1 = 0 , f2 = 1 ,f3 = 0;
       for (int i = 0; i < n; i++) {
           f3 = f2 + f1;
           f1 = f2;
           f2 = f3;
       } 
       return f3;
}
```
## [746. 使用最小花费爬楼梯¹](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)

数组的每个索引做为一个阶梯，第 `i`个阶梯对应着一个非负数的体力花费值 `cost[i]`(索引从0开始)。

每当你爬上一个阶梯你都要花费对应的体力花费值，然后你可以选择继续爬一个阶梯或者爬两个阶梯。

您需要找到达到楼层顶部的最低花费。在开始时，你可以选择从索引为 0 或 1 的元素作为初始阶梯。

**示例 1:**

```
输入: cost = [10, 15, 20]
输出: 15
解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。
```

 **示例 2:**

```
输入: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
输出: 6
解释: 最低花费方式是从cost[0]开始，逐个经过那些1，跳过cost[3]，一共花费6。
```

**注意：**

1. `cost` 的长度将会在 `[2, 1000]`。
2. 每一个 `cost[i]` 将会是一个Integer类型，范围为 `[0, 999]`。

### 解法：动态规划

* 思路：分解模型&&重复子问题（==>求上到每一级台阶的最小花费）。递推公式，`f(n) = min(f(n-1), f(n-2)) + cost[n]`。
	* 状态数组：`d[i]`。爬到第 i 层的最小花费。
	* 初始状态：`d[0]=cost[0], d[1]=cost[1]`。注意，这里dp[1] = cost[1]，不是min（cost[1],cost[0])。
	* 状态方程：`d[i] = cost[i] + min(d[i - 1], d[i - 2])`。没什么说的了。
	* 最终状态：`min(d[len-1], d[len-2]`。对于跨到第n层，可以从d[n-1]跨一步，或者从d[n-2]跨两步，取小的。
* 复杂度：
	* Time：O(n)
	* Space：O(n)

```java
public int minCostClimbingStairs(int[] cost) {
        // 状态数组
        int[] dp = new int[cost.length];
        // 状态初始
        dp[0] = cost[0]; dp[1] = cost[1];
        // 状态递推
        for (int i = 2; i < cost.length; i++) {
            dp[i] = cost[i] + Math.min(dp[i - 1], dp[i - 2]);
        }
    	// 最终状态。返回的是min（dp[len - 1], dp[len - 2])，不是直接返回
        return Math.min(dp[cost.length - 1], dp[cost.length - 2]);
    }
```

从上面代码可以看出，在状态递推的过程中，只会用到两个变量（d[i]-1和d[i-2]），所以可以将状态数组优化成两个状态变量（pre1, pre2）。

```java
// Space：O(1)
public int minCostClimbingStairs(int[] cost) {
        int pre1 = cost[1], pre2 = cost[0];
        for (int i = 2; i < cost.length; i++) {
            int tmp = pre1;
            pre1 = cost[i] + Math.min(pre1, pre2);
            pre2 = tmp;
        }
        return Math.min(pre1, pre2);
    }
```


