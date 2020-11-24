## [322. 零钱兑换²](https://leetcode-cn.com/problems/coin-change/)

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 `-1`。 

**示例 1:**

```
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```

**示例 2:**

```
输入: coins = [2], amount = 3
输出: -1
```

**说明**:
你可以认为每种硬币的数量是无限的。

### 解法：动态规划
* 思路：分解模型&&重复子问题（==>求分解出的每个金额最少需要多少硬币组合）
	* 状态数组：` d[i][j]`。 前 i 种硬币凑成金额 j 需要的最少硬币
	* 初始状态：
		* ` d[0][j]=Integer.MAX_VALUE`。没有硬币那就组不出 j，但这里不能用-1因为如果取min，那么结果一定全是-1
		* ` d[i][0]=0`。凑出金额0要0个硬币（由于数组默认初始值是0，所以不用再显式声明）
	* 状态方程：`d[i] = min(d[i-1][j], d[i][j-c[i-1]] + 1)`。构成金额j就两种方式
	* 最终状态：`d[coins.len][amount]`。len个硬币组成最终金额amout
* 复杂度：
	* Time：O(n * amount)
	* Space：O(n * amount)
		
		
```java
public int coinChange(int[] coins, int amount) {
        // 状态数组
        int[][] d = new int[coins.length + 1][amount + 1];
        // 初始状态          
        for (int j = 0; j <= amount; j++) d[0][j] = Integer.MAX_VALUE;
        // 状态递推
        for (int i = 1; i <= coins.length; i++) {
            for (int j = 1; j <= amount; j++) {
                int useCurCoin = j >= coins[i - 1] ? d[i][j - coins[i - 1]] : Integer.MAX_VALUE;
                if (useCurCoin != Integer.MAX_VALUE) useCurCoin += 1;
                d[i][j] = Math.min(d[i - 1][j], useCurCoin);
            }
        }
        // 最终状态：d[coins.len][amount]
        return d[coins.length][amount] == Integer.MAX_VALUE ? -1 : d[coins.length][amount];
    }
```


## [518. 零钱兑换 II²](https://leetcode-cn.com/problems/coin-change-2/)

给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。  


**示例 1:**

```
输入: amount = 5, coins = [1, 2, 5]
输出: 4
解释: 有四种方式可以凑成总金额:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

**示例 2:**

```
输入: amount = 3, coins = [2]
输出: 0
解释: 只用面额2的硬币不能凑成总金额3。
```

**示例 3:**

```
输入: amount = 10, coins = [10] 
输出: 1
```

**注意**:

你可以假设：

- 0 <= amount (总金额) <= 5000
- 1 <= coin (硬币面额) <= 5000
- 硬币种类不超过 500 种
- 结果符合 32 位符号整数

### 解法一：分治（超时）
* 思路：分解模型（==>求分解出的每个金额可以有多少种组合方案）。递推公式，`f(n) += f(n-coins[i])  i∈[0, coins.length)`。

```java
class Solution {
    public int change(int amount, int[] coins) {
        return changeConis(amount, 0, coins);
    }
    
	// start是防止重复答案（1,2,2）与 （2,2,1）
    // 比如在同一层，1 -> 2 // 2 -> 1
    private int changeConis(int money, int start, int[] coins) {
        if (money == 0) return 1;
        if (money < 0) return 0;

        int res = 0;
        for (int i = start; i < coins.length; i++) 
            res += changeConis(money - coins[i], i, coins);

        return res;
    }
}
```

### 解法二：动态规划
* 思路：
	* 状态数组：`d[i][j]`。使用前 i 种面值的硬币（即c中0~i-1的元素），凑成金额 j 的组合数
	* 初始状态：
		* ` d[i][0]=1`,。组成金额0只有一种方式
		* `d[0][i]=0`。没有硬币时组合方式为0（注：由于数组默认初始值是0，所以不用再显式声明）
	* 状态方程：`d[i][j] = d[i - 1][j] + d[i][j - c[i-1]]     (j - c[i-1] >= 0)`。i 种面值的组合 = i-1的组合 + i 组成 j-c[i]，（例如，`d[3][5] = d[2][5] + d[3][0]`）
	* 最终状态：`d[coins.len][amount]`。
* 复杂度：
	* Time：O（n * amount)
	* Space：O（n * amount)
		
>**最优子结构 = 选择 || 淘汰**。这里的逻辑树其实存在淘汰重复分支的思路，与爬楼梯问题一样



```java
public int change(int amount, int[] coins) {
        // 1.状态方程
        int[][] d = new int[coins.length + 1][amount + 1];
        // 2.初始状态
        for(int i = 0; i <= coins.length; i++) d[i][0] = 1;
        // 3.状态递推      
        for (int i = 1; i <= coins.length; i++) {
            for (int j = 1; j <= amount; j++) {
                // 注：因为要保证j - coins[i-1] >= 0，所以先判断 d[i][j - c[i-1]]
                int userCurCoin = j >= coins[i - 1] ? d[i][j - coins[i - 1]] : 0;
                d[i][j] = d[i - 1][j] + userCurCoin;
            }
        }
        // 4.最终状态：d[coins.len][amount]
        return d[coins.length][amount];
    }
```
## [72. 编辑距离³](https://leetcode-cn.com/problems/edit-distance/)

给你两个单词 *word1* 和 *word2*，请你计算出将 *word1* 转换成 *word2* 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

1. 插入一个字符
2. 删除一个字符
3. 替换一个字符

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

### 解法：动态规划
* 思路：分解模型&&重复子问题（==>求word1变成word2中间串的最小编辑距离）
	*  状态数组：`d[i][j]`。word1.sub(0,i) 与 word2.sub(0,j) 的最小编辑距离，也可以理解成word1的前i个字符和word2的前j个字符已经相同了
	* 初始状态：`d[i][0] = i`,` d[0][j] = j`。任意一个word为空则编辑距离等于另一个word长度
	* 状态数组：`d[i][j] = w1.charAt(i-1) == w2.charAt(j-1) ? d[i-1][j-1] : min(d[i-1][j-1],d[i-1][j],d[i][j-1]) +1`。若两个串最后一个字母相同，直接不管，否则换/删/增，再操作数+1：
		* 改：`d[i][j] = d[i-1][j-1] + 1`。将word1的当前字符(i)改到与word2(j)相同，所以i，j都减一
		* 删：`d[i][j] = d[i-1][j] + 1`。将word1的当前字符(i)删除，所以只有 i 减一
		* 增：`d[i][j] = d[i][j-1] + 1`。将word1加上当前字符，所以只有 j 减一
	* 最终状态： `d[w1.len-1][w2.len-1]`。
* 复杂度：
	* Time：O(m*n)
	* Space：O(m*n)

```java
class Solution {

    public int minDistance(String word1, String word2) {
        int m = word1.length() + 1, n = word2.length() + 1; // +1是状态还要保存空串情况
        // 状态数组
        int[][] d = new int[m][n];
        
        // 初始状态
        for (int i = 0; i < m; i++) d[i][0] = i;
        for (int j = 0; j < n; j++) d[0][j] = j;
        
        // 状态递推。递推过程本质就是一个填表的过程！
        for (int i = 1; i < m; i++) 
            for (int j =1; j < n; j++) 
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) 
                    d[i][j] = d[i - 1][j - 1];
                else
                    d[i][j] = min(d[i - 1][j - 1],d[i - 1][j], d[i][j - 1]) + 1;
                    
        // 4.最终状态：d[w1.len-1][w2.len-1]
        return d[m - 1][n - 1];
    }
	
	// 辅助函数，用来求三个数的最小值
    private int min(int a, int b, int c) {
        return Math.min(Math.min(a, b), c);
    }
}
```
