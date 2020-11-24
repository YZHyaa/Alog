**分治：递归的基础上多了一步对结果的合并**
```python
def divide_conquer(problem, param1, param2, ...): 
  # recursion terminator 
  if problem is None: 
	print_result 
	return 

  # prepare data 
  data = prepare_data(problem) 
  subproblems = split_problem(problem, data) 
 
  # conquer subproblems 
  subresult1 = self.divide_conquer(subproblems[0], p1, ...) 
  subresult2 = self.divide_conquer(subproblems[1], p1, ...) 
  subresult3 = self.divide_conquer(subproblems[2], p1, ...) 
  …

  # process and generate the final result 
  result = process_result(subresult1, subresult2, subresult3, …)
	
  # revert the current level states
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

### 解法一：暴力重复（超时）

* 思路：循环，每次都乘x
  * 注意：若 n < 0，需要做处理
* 复杂度
  * Time：O（n）
  * Space：O（1）

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

### 解法二：分治


- 思路：例如2^10 = 2 ^5 * 2 ^ 5，每个次方其实等于其实等于其半次的平方，因此先算个半次方的值然后再合并。如下图求 2的10次方的树形图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201016171459158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
==> 递推公式：f(n) = f(n/2) * f(n/2)。从而得到递归函数如下：
  - 递归函数：`double f （double x, int n)`
	  - res：double。给上层返回当前x^n的值。
	  - level：n。当前层要求x的n次方，求到0次方时到达叶节点。
	  - info：x。基数，比如上面的 2。
  - 函数体：
    - termination：当求到0次方时开始返回合并结果，即 `n == 0`
    - subproblem：将求 n 次方问题分解成 x的 n/2 次方，即 `f(x, n/2)`
    - mergeresult：将下层求的半平方合并，这里需要注意，如果n是奇数次方，那么要在合并时要再乘个基数x
* 复杂度
  * Time：O（log n）
  * Space：O（log n）每一次计算，我们需要存储 x ^ n/2的结果。 我们需要计算O(logn) 次，所以空间复杂度为 O(logn) 。

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1.0;
        return n > 0 ? fastPow(x, n) : fastPow(1 / x, -n);
    }

    private double fastPow(double x,int n) {
        if (n == 0) return 1.0;
        double half = fastPow(x,n/2);
        return n % 2 == 0 ? half * half : half * half * x;
    }
}
```

### 解法三：位运算

* 思路：例如 x ^ 11 = ？
  1. 11 = 1011，即8421码
  2. x ^ 11 = x^8 * x^2 * x^1，即加权相乘，权 = 之前一位的平方
* 复杂度
  * Time：O（logn）
  * Space：O（1）

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

## [169. 多数元素¹](https://leetcode-cn.com/problems/majority-element/)

给定一个大小为 *n* 的数组，找到其中的多数元素。多数元素是指在数组中出现次数**大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例 1:**

```
输入: [3,2,3]
输出: 3
```

**示例 2:**

```
输入: [2,2,1,1,1,2,2]
输出: 2
```

### 解法一：Hash表

* 思路：用一个Hash表记录所有元素的出现次数，然后做比较
* 复杂度
  * Time：O（n）
  * Space：O（n），最差是只有两个相同元素即O（n-1）

```java
public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        Map<Integer,Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (!map.containsKey(nums[i])) map.put(nums[i],1);
            else map.put(nums[i],map.get(nums[i]) + 1);     
        }
    	// 这里不能用lamada的foreach，不能出现非final变量，所以不能return key
    	// idea快捷键 map.keySet().for
        for (Integer key : map.keySet()) 
            if (map.get(key) > nums.length/2) return key;
        return -1;
}
```

### 解法二：分治*

```java
class Solution {
    private int countInRange(int[] nums, int num, int lo, int hi) {
        int count = 0;
        for (int i = lo; i <= hi; i++) {
            if (nums[i] == num) {
                count++;
            }
        }
        return count;
    }

    private int majorityElementRec(int[] nums, int lo, int hi) {
        // base case; the only element in an array of size 1 is the majority
        // element.
        if (lo == hi) {
            return nums[lo];
        }

        // recurse on left and right halves of this slice.
        int mid = (hi-lo)/2 + lo;
        int left = majorityElementRec(nums, lo, mid);
        int right = majorityElementRec(nums, mid+1, hi);

        // if the two halves agree on the majority element, return it.
        if (left == right) {
            return left;
        }

        // otherwise, count each element and return the "winner".
        int leftCount = countInRange(nums, left, lo, hi);
        int rightCount = countInRange(nums, right, lo, hi);

        return leftCount > rightCount ? left : right;
    }

    public int majorityElement(int[] nums) {
        return majorityElementRec(nums, 0, nums.length-1);
    }
}
```
