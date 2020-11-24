## [198. 打家劫舍¹](https://leetcode-cn.com/problems/house-robber/)

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你**在不触动警报装置的情况下，**能够偷窃到的最高金额。

**示例 1:**

```
输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2:**

```
输入: [2,7,9,3,1]
输出: 12
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 
```

### 解法一：分治（超时）

分解模型（==>求偷每个房子所能带来的最大收益）。递推公式，`f(n) = max(f(n-1), f(n-2)+nums[n])`
> f(n-1)表示不偷第n个房子，因为如果不偷就没有收益，即=f(n-1)。而如果偷了就是f(n-2)+nums[n]

```java
class Solution {
    public int rob(int[] nums) {
        return tryRob(0, nums);
    }

    private int tryRob(int idx, int nums[]) {
        if (idx == nums.length) return 0;
        int res = 0;
        for (int i = idx; i < nums.length; i++)
        	// 这里res就代表不偷，i+2代表偷下一家
            res = Math.max(res, nums[i] + tryRob(i + 2, nums));
        return res;
    }
}
```

### 解法二：记忆递归（超时）

```java
class Solution {
    public int rob(int[] nums) {
        return tryRob(0, nums,new int[nums.length + 1]);
    }

    private int tryRob(int idx, int nums[], int[] memo) {
        if (idx >= nums.length) return 0;
        if (memo[idx] == 0) {
            int res = 0;
            for (int i = idx; i < nums.length; i++)
                res = Math.max(res, nums[i] + tryRob(i + 2, nums, memo));
            memo[idx] = res;
        }
        return memo[idx];
    }
}
```

### 解法三：动态规划

* 思路：
  * 状态定义：`d[i]`。偷到第 i 个房子时的最大收益。
  * 状态初始：`d[0] = nums[0]`，`d[1] = max(nums[0],nums[1])`。因为在递推时需要两个状态，所以还要提前求出d[1]，即偷第二间房的收益。
  * 状态递推：`d(i) = max(d(i - 1), d(i - 2) + nums[i])`。要么不偷（与前一房相同），要么偷（前两房+nums[i])。
  * 最终状态：`d[len-1]`。

```java
public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];

        int[] memo = new int[nums.length];
        // 初始状态
        memo[0] = nums[0];
        memo[1] = Math.max(nums[0], nums[1]);
        // 状态递推
        for (int i = 2; i < nums.length; i++) 
            memo[i] = Math.max(memo[i - 1], memo[i - 2] + nums[i]);
        // 返回最终状态
        return memo[nums.length - 1];
}
```

可以将一维状态数组优化成两个状态变量，因为只需要前两个状态（`d[i-1]`, `d[i-2]`）即可：

```java
// Space = O（1）
public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
		// 将状态数组优化成两个变量
        int pre1 = nums[0];
        int pre2 = Math.max(nums[0], nums[1]);
        // 状态递推过程不变
        for (int i = 2; i < nums.length; i++) {
            int cur = Math.max(pre2, pre1 + nums[i]);
            pre1 = pre2;
            pre2 = cur;
        }
        // 最终状态保存在pre2中  
        return pre2;
}
```

## [213. 打家劫舍 II²](https://leetcode-cn.com/problems/house-robber-ii/)

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都**围成一圈，**这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你**在不触动警报装置的情况下**，能够偷窃到的最高金额。

**示例 1:**

```
输入: [2,3,2]
输出: 3
解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```

**示例 2:**

```
输入: [1,2,3,1]
输出: 4
解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

### 解法：动态规划

上一题是线性，而本题是环形 ==> 可以转化为两个线性数组（[0, len-1), [1, len)），分别求它们的最大收益（上题的代码），然后取最值。

```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
        
        return Math.max(
        	// 情况一：nums（0，len-1）
        	myrob(Arrays.copyOfRange(nums, 0, nums.length - 1)), 
            // 情况二：nums(1, len)
            myrob(Arrays.copyOfRange(nums, 1, nums.length)));
    }
    
	// 线性排列求解，其实就是上一题的代码
    private int myrob(int[] nums) {
        // 这里不用状态数组，两个变量就够了
        // 注：两个变量递推模板：tmp = cur; cur = ? ; pre = tmp;
        int pre = 0, cur = 0, tmp;
        for (int i = 0; i < nums.length; i++) {
            tmp = cur;
            cur = Math.max(cur, pre + nums[i]);
            pre = tmp;
        }
        return cur;
    }
}
```
