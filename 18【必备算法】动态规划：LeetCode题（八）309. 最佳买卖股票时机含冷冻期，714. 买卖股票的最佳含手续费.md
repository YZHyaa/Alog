看这篇之前一定要先看之前两篇：
* [【必备算法】动态规划：一个思路解决六道股票问题](https://blog.csdn.net/weixin_43935927/article/details/109791453)
* [【必备算法】动态规划：LeetCode（七）121. 买卖股票的最佳时机，122. 买卖股票的最佳时机 II，123. 买卖股票的最佳时机 III，188. 买卖股票的最佳时机 IV](https://blog.csdn.net/weixin_43935927/article/details/109786398)

本篇只是给出具体动归代码实现而已...



## [309. 最佳买卖股票时机含冷冻期²](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

给定一个整数数组，其中第 *i* 个元素代表了第 *i* 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**示例:**

```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

```java
public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) return 0;
		
		// 状态定义
		// 注：与前面的122题一样，不限次数，二维状态就够了
        int[][] mp = new int[n][2];
		
		// 初始状态
        mp[0][0] = 0;
        mp[0][1] = -prices[0];
		
		// 状态递推
		// dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        // dp[i][1] = Math.max(dp[i - 1][1], dp[i - 2][0] - prices[i]);
        for (int i = 1; i < n; i++) {
            mp[i][0] = Math.max(mp[i-1][0], mp[i-1][1] + prices[i]);
            mp[i][1] = Math.max(mp[i-1][1], (i >= 2 ? mp[i-2][0] : 0) - prices[i]); // 前两天买时直接买
        }
		
		// 最终状态
        return mp[n-1][0];
    }
```

## [714. 买卖股票的最佳含手续费²](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

给定一个整数数组 `prices`，其中第 `i` 个元素代表了第 `i` 天的股票价格 ；非负整数 `fee` 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**注意：**这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

**示例 1:**

```
输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
输出: 8
解释: 能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

**注意:**

- `0 < prices.length <= 50000`.
- `0 < prices[i] < 50000`.
- `0 <= fee < 50000`.


```java
public int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        if (n < 2) return 0;
		
		// 状态定义
		// 注：与前面的122题一样，不限次数，二维状态就够了
        int[][] mp = new int[n][2];
		
		// 初始状态
        mp[0][0] = 0;
        mp[0][1] = -prices[0];
		
		// 状态递推
		// dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i] -fee);
        // dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        for (int i = 1; i < n; i++) {
            mp[i][0] = Math.max(mp[i-1][0], mp[i-1][1] + prices[i] - fee); // 与前面122题的区别就在于卖出股票后要收个fee
            mp[i][1] = Math.max(mp[i-1][1], mp[i-1][0] - prices[i]);
        }
		
		// 最终状态
        return mp[n-1][0];
    }
```
可以将上面的状态数组优化成两个状态变量，因为在上面递推的过程中，所需要的状态只有 `mp[i-1][0]` 和 `mp[i-1][1]`，从而空间复杂度可以从O(n)优化成O(1)。
```java
 public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) return 0;

        // 状态定义+初始状态
        int mp_0 = 0, mp_1 = -prices[0];
		
		// 状态递推
        for (int i = 1; i < n; i++) {
            int tmp = mp_0;
            mp_0 = Math.max(mp_0, mp_1 + prices[i] -fee); // -fee
            mp_1 = Math.max(mp_1, - prices[i]);
        }
		
		// 最终状态
        return mp_0;
    }
```
