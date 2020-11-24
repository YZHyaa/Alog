在看二分查找的LeetCode题之前，先看看二分查找的基础知识：

**使用二分查找的条件：**
1. 可以通过索引访问（链表不行）
2. 有序
3. 存在上下界

**二分查找的实质：**

* 二分查找不是一上来就去找指定值，而是不**断进行定位**缩小范围的过程
* 主要用来对一些暴力迭代进行**优化**
* 复杂度：O（logN）

**Java模板：**

```java
int left = 0, right = arr.length - 1;
while (left <= right) { // 这里是 <=，即arr[left=right]也要进入判断逻辑
    int mid = left + (right - left) / 2; // 等于（left+right）/2，这么写是防止溢出
    if (target == arr[mid]) return mid;
    else if (target < arr[mid]) right = mid - 1;
    else left = mid + 1;
}
```

* 结束条件 left <= right：可以通过【1,2,3,4,5】找 2 来验证，若无等于那么会忽略掉查找值在边界的情况
* 避免溢出：在求mid时，需要int + int 但可能俩int都是int_MAX_VALUE（2147483647），会溢出，因此要避免加乘
  * 加化减：mid = left + (right - left )  / 2
  * 乘强转：（double）mid * mid

**模板变式：**

上面是直接在数组中二分查找，然后根据arr[mid]大小判断分区。变式：
* 若将自然数0-num看成数组，则也可以用二分查找（left=0，right=num）
* 判断分区就不能直接通过大小比了，因为大小是确定的。

示例参见下面平方相关的两个例题（69,367）...
```java
// 找x的平方根
public int mySqrt(int x) {
		// 这里虽然不是数组，但也可以将1-x看做数组
        int left = 1, right = x;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            // 判断找到的条件是：mid*mid<=x && (mid+1)*(mid+1)>x
            // 注：转换成double是防止溢出
            if ((double)mid * mid <= x && (double)(mid + 1) * (mid + 1) > x) return mid;
            else if ((double)mid * mid > x) right = mid - 1;
            else left = mid + 1;
        }
        return 0;
}
```
## [69. x 的平方根¹](https://leetcode-cn.com/problems/sqrtx/)

实现 `int sqrt(int x)` 函数。

计算并返回 *x* 的平方根，其中 *x* 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**示例 1:**

```
输入: 4
输出: 2
```

**示例 2:**

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

### 解法一：暴力迭代

* 思路：依次从0开始寻找，结束条件：`i * i == x 或 (i + 1) * (i + 1) > x `。两点注意：
    * (i + 1)(i + 1) 不能>=，因为结果会大1    
    * i * i 也会拿int来保存，会发生溢出，int【-2147483648~2147483647】
* 复杂度
  * Time：O（n），97ms
  * Space：O（1）

```java
public int mySqrt(int x) {
        for (int i = 1; i <= x; i++) {
            // 这里将结果强转为double，防止溢出
            if ((double)i * i == x || (double)(i + 1) * (i + 1) > x) return i;
        }
        return 0;
    }
```

### 解法二：二分查找

* 思路：既然能暴力迭代那么看看是否符合二分查找条件：数组，有序，边界
* 复杂度
  * Time：O（logN），2ms
  * Space：O（1）

```java
public int mySqrt(int x) {
		// 这里虽然不是数组，但也可以将1-x看做数组
        int left = 1, right = x;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            // 判断找到的条件是：mid*mid<=x && (mid+1)*(mid+1)>x
            // 注：转换成double是防止溢出
            if ((double)mid * mid <= x && (double)(mid + 1) * (mid + 1) > x) return mid;
            else if ((double)mid * mid > x) right = mid - 1;
            else left = mid + 1;
        }
        return 0;
}
```

## [367. 有效的完全平方数¹](https://leetcode-cn.com/problems/valid-perfect-square/)

给定一个正整数 *num*，编写一个函数，如果 *num* 是一个完全平方数，则返回 True，否则返回 False。

**说明：**不要使用任何内置的库函数，如  `sqrt`。

**示例 1：**

```
输入：16
输出：True
```

**示例 2：**

```
输入：14
输出：False
```

### 解法一：暴力迭代
* 思路：没啥说的，就是从1开始找平方根
* 复杂度：
	* Time：O(n)
	* Space：O(1)
```java
 public boolean isPerfectSquare(int num) {
 		// 从1开始找平方根
        for (int i = 1; i <= num; i++) {
            if ((double)i * i == num) return true;
            // 可以极大优化时间复杂度
            else if ((double)i * i > num) return false;
        }
        return false;
    }
```

### 解法二：二分查找
* 思路：与上一题差不多
* 复杂度：
	* Time：O(logn)
	* Space：O(1)
```java
 public boolean isPerfectSquare(int num) {
        int left = 1, right = num;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            // 找到的条件：mid * mid == num
            // 注：转为double是防止溢出
            if ((double)mid * mid == num) return true;
            else if((double)mid * mid < num) left = mid + 1;
            else right = mid - 1;
        }
        return false;
}
```

## [33. 搜索旋转排序数组²](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 `-1` 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 *O*(log *n*) 级别。

**示例 1:**

```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```

**示例 2:**

```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```

### 解法：二分查找

* 思路：要求了O（logn），相当于说明了要二分查找；但是对于此题不能直接二分查找，在判断区间时要分成有序边和无序边来处理
* 复杂度
	* Time：O(logn)
	* Sapce：O(1)
```java
public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return -1;
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (target == nums[mid]) return mid; // 找到了
            
		    // 左边有序，右边无序(可能)
		    // 比如[4,5,6,7,0,1]，所以arr[left]<arr[mid]，即左边[4,5,6]有序，右边[7,0,1]无序
            if (nums[left] <= nums[mid]) {
            	// 照理说只用判断target < nums[mid]就行。但看上例，如果target=0就判断错了，所以还要 target>=nums[left]
                if (target < nums[mid] && target >= nums[left]) right = mid - 1;
                else left = mid + 1;
            // 右边有序，左边无序(可能)
            }else { 
                if (target > nums[mid] && target <= nums[right]) left = mid + 1;
                else right = mid - 1;
            }
        }
        return -1;
}
```

## [240. 搜索二维矩阵 II²](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

编写一个高效的算法来搜索 *m* x *n* 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

### 解法一：暴力迭代
* 思路：没啥说的，就是遍历二维数组
* 复杂度：
	*  Time：O（n * m）
	* Space：O（1） 
```java
public boolean searchMatrix(int[][] matrix, int target) {
		// 遍历二维数组时，可以理解为数组的数组
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                if (matrix[i][j] == target) return true;
            }
        }
        return false;
    }
```

### 解法二：二分查找

* 思路：**有规律排列的查找问题 -----> 二分查找**。本题与之前习题不一样之处就在于是一个二维数组，怎么办呢？从右上角（`[0][j-1]`）开始找：    

	1. 如果 target < `[i][j]`，行不变，列左移
	2. 如果 target > `[i][j]`，列不变，行下移
	3. 相等则返回
* 复杂度：
	* Time：O（m + n）
	*  Space：O（1）
```java
public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix.length == 0 || matrix[0].length == 0) return false;
        int m = matrix.length, n = matrix[0].length;
        // 得到右上角位置，i行j列
        int i = 0, j = n - 1;
        while (i < m && j >= 0) {
            if (target < matrix[i][j]) j--; // 左移
            else if (target > matrix[i][j]) i++; // 下移
            else return true;
        }
        return false;
    }
```
