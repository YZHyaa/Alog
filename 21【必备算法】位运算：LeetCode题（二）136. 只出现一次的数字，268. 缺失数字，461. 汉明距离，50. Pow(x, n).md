上一篇 [位运算：LeetCode（一） 191. 位1的个数，231. 2的幂，190. 颠倒二进制位，371. 两整数之和](https://blog.csdn.net/weixin_43935927/article/details/109036052)
## [136. 只出现一次的数字¹](https://leetcode-cn.com/problems/single-number/)

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

### 解法一：暴力法
* 思路：对于每个数字，都在整个数组找一遍看有无相同元素
* 复杂度
	* Time：O(n^2)
	* Space：O(1)
```java
public int singleNumber(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            boolean flag = true;
            for (int j = 0; j < nums.length; j++) {
                if (nums[i] == nums[j] && i != j) {
                    flag = false;
                    continue;
                }
            }
            if (flag) return nums[i];
        }

        return nums[0];
    }
```

### 解法二：空间换时间（Set）
* 思路：上面是双重循环来判断是否有重复元素，那可不可以通过引入一个容器来降低时间复杂度呢？可以的，这里很适合使用Set。依次将所有元素加入set，然后判断是否有重复的。那不重复的元素怎么求呢？可以通过数字特征来求，求所有元素的和 sum，再求所有不重复元素的和 uniqueSum，则不重复元素=2*uniqueSum-sum。
* 复杂度：
	* Time：O(n)，只用遍历一遍
	* Space：O(n)，要引入一个set来保存所有数据

```java
public int singleNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int sum = 0, uniqueSum = 0; 
        for (int i = 0; i < nums.length; i++) {
            // Set.add()，返回boolean（当前元素是否添加成功（不存在））
            if (set.add(nums[i])) uniqueSum += nums[i];
            sum += nums[i];
        }
        // 除一个数字出现一次外，其余数字都出现两次
        // 所以 唯一的数 = 2 * 出现一次数字和 - 当前所有数字和
        return 2 * uniqueSum - sum;
    }
```

### 解法三：位运算（异或）
* 思路：任何数异或它自己等于0
* 复杂度
	* Time：O(n)
	* Space：O(1)
```java
// Time：O（n）， Space：O（1）
public int singleNumber(int[] nums) {
        int res = 0;
    	// 1.a ^ a = 0
        // 2.交换律                  =====> 所有数异或的结果就是唯一
        for (int i = 0; i < nums.length; i++) res ^= nums[i];
        return res;
    }
```

## [268. 缺失数字¹](https://leetcode-cn.com/problems/missing-number/)

给定一个包含 `0, 1, 2, ..., n` 中 *n* 个数的序列，找出 0 .. *n* 中没有出现在序列中的那个数。

**示例 1:**

```
输入: [3,0,1]
输出: 2
```

**示例 2:**

```
输入: [9,6,4,2,3,5,7,0,1]
输出: 8
```

**说明:**
你的算法应具有线性时间复杂度。你能否仅使用额外常数空间来实现?

### 1.解法一：暴力法

* 思路：先排序，再遍历，判断 i 是否等于 nums[i]
* 复杂度
  * Time：O（n）
  * Space：O（1）

```java
public int missingNumber(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) 
            if (nums[i] != i)
                return i;
        return nums.length;
    }
```

### 解法二：位运算（异或）

* 思路：还是判断 nums[i] 是否等于 i，但这里采用相同的两个数异或（^）结果为0 + 交换律
* 复杂度
  * Time：O（n）
  * Space：O（1）

```java
 public int missingNumber(int[] nums) {
        // 注：这里一定要初始化为len，因为在遍历数组时 i 不会等于len
        int res = nums.length;
        for (int i = 0; i < nums.length; i++) res = res ^ i ^ nums[i];
        return res;
    }
```

## [461. 汉明距离¹](https://leetcode-cn.com/problems/hamming-distance/)

两个整数之间的[汉明距离](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E8%B7%9D%E7%A6%BB)指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 `x` 和 `y`，计算它们之间的汉明距离。

**注意：**
0 ≤ `x`, `y` < 231.

**示例:**

```
输入: x = 1, y = 4

输出: 2

解释:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

上面的箭头指出了对应二进制位不同的位置。
```

### 解法一：位运算（异或）

* 思路：异或就是不进位加法，即11=0 , 00=0，10=1。所以 x ^ y 得到的结果有几个 1，就代表有多少位不同
* 复杂度
  * Time：O（n），n与x，y的二进制位数有关
  * Space：O（1）

```java
class Solution {
    public int hammingDistance(int x, int y) {
    	// x^y的结果中，如果有相同位，此时num相应位置已经为0。所以现在统计1的个数
        int num = x ^ y; 
        int count = 0;
        while (num > 0) {
            if ((num & 1) == 1) count++;
            num >>= 1;
        }
        return count;
    }
}
```

优化：java提供了 bitCount 方法，统计一个数字的二进制中，有几个1

```java
class Solution {
    public int hammingDistance(int x, int y) {
        return Integer.bitCount(x ^ y);
    }
}
```

## [50. Pow(x, n)²](https://leetcode-cn.com/problems/powx-n/)

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数。

**示例 1:**

```
输入: 2.00000, 10
输出: 1024.00000
```

**示例 2:**

```
输入: 2.10000, 3
输出: 9.26100
```

**示例 3:**

```
输入: 2.00000, -2
输出: 0.25000
解释: 2-2 = 1/22 = 1/4 = 0.25
```

**说明:**

- -100.0 < *x* < 100.0
- *n* 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

### 解法一：暴力法（超时）

- 思路：循环，每次都乘x
  - 注意：若 n < 0，需要做处理
- 复杂度：
  - Time：O（n）
  - Space：O（1）

```java
public double myPow(double x, int n) {
        double res = 1;
    	// 转换成long是因为 int[-2147483648,2147483647] 而2147483648超出了int范围
        long N = Math.abs((long)n);
        for (int i = 0; i < N; i++) 
            res *= x;
        return n > 0 ? res : 1 / res;
}
```

```java
public double myPow(double x, int n) {
        double pow = 1;
        for (int i = 0; i < Math.abs(n); i++) {
            pow = n >= 0 ? pow * x : pow / x;
        }
        return pow;
}
```

### 解法二：位运算

- 思路：例如 x ^ 11 = ？
	1. 11 = 1011，即8421码
	2. x ^ 11 = x^8 * x^2 * x^1，即加权相乘，权 = x ^ 8421... = 之前一位的平方
- 复杂度：
	- Time：O(logn），因为>>等于/2二分
	- Space：O(1)
```java
public double myPow(double x, int n) {
        double res = 1;
        // 注：转换成long是因为 int[-2147483648,2147483647] 而2147483648超出了int范围
        long N = Math.abs((long)n);
        while (N != 0) {
            // 与 0001取余得最低位
            if ((N & 1) == 1) 
                // 权值相乘
                res *= x;
            // 右移，准备得到下一位
            N >>= 1;   
            // 平方，得到下一位的权值
            x *= x;
        }
        return n > 0 ? res : 1 / res;
    }
```



