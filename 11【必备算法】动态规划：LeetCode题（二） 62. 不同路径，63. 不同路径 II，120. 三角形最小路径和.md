

## [62. 不同路径²](https://leetcode-cn.com/problems/unique-paths/)

一个机器人位于一个 *m x n* 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？

![img](https://img-blog.csdnimg.cn/img_convert/a9d710311ab12af6970636a006576dc1.png)

例如，上图是一个7 x 3 的网格。有多少可能的路径？

**示例 1:**

```
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右
```

**示例 2:**

```
输入: m = 7, n = 3
输出: 28
```

**提示：**

- `1 <= m, n <= 100`
- 题目数据保证答案小于等于 `2 * 10 ^ 9`
### 解法一：分治（超时）
* 思路：分解模型（==>求起点到每个格子的有几种路径）。递推公式，`f(x, y) = f(x - 1, y) + f(x, y - 1)`。
	
>下面以m=3，n=2举例，**自顶向下**从终点（3,2）出发，求到起点（1,1）的走法，可得到树形如图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201020145855880.png#pic_left)     <IMG SRC="https://img-blog.csdnimg.cn/20201020150832488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_left" WIDTH="50%">
>可以看出，该问题转化为求（2,2）和 （3,1）到起点（0,0）的走法有多少种，然后求和。

* 复杂度：
  * Time：O（2 ^ n），有指数级的节点
  * Space：O（2 ^ n），每个节点都要保存

```java
public int uniquePaths(int m, int n) {
       // termination：从上图可以看到只要行（m）或列（n）等于 1时，该方案就没有选择了（无分叉），即返回1（一种方案）
       // subproblem和mergeresult直接写递推公式即可。
       return (m == 1 || n == 1) ? 1 : uniquePaths(m - 1, n) + uniquePaths(m, n - 1);
}
```
从上图仅3行2列就可以看到（2,1）和 （2,2）存在重复，所以一定想办法解决。
### 解法二：记忆递归
* 思路：由于重复的节点过多，所以可以用一个数组保存下所有中间结果，相当于剪枝
* 复杂度：
  * Time：O（n），一种节点只用算一次，与动归几乎没区别（0 ms）
  * Space：O（n）

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] visited = new int[m + 1][n + 1]; // 这里需要一个二维的状态数组
        return uniquePaths(m, n, visited);
    }
	
	// 在写递归函数时传入状态数组
    private int uniquePaths(int m, int n, int[][] visited) {
        if (m == 1 || n == 1) return 1;
        // 如果已经计算过该节点了，就不再进入递归函数计算，直接取值就行
        if (visited[m][n] == 0) 
            visited[m][n] = uniquePaths(m - 1, n, visited) + uniquePaths(m, n - 1, visited);
        // 返回结果直接通过状态数组返回
        return visited[m][n];
    }
}
```

### 解法三：动态规划

* 思路：分解且存在重复问题。**自底向上**，从起点（0,0）开始分析：

  * 状态数组：`d[m][n]`。起点（0,0）到（m,n）位置有多少种走法。
  
  	>这里注意，因为是数组下标，所以（m,n）表示m+1行n-1列。
  * 初始状态：`d[i][1]=1,d[1][i]=1`。第一行和第一列到起始位置（0,0）的走法只有1种。
  * 状态方程：`d[m][n]= d[m - 1][n] + d[m][n - 1]`。从递推公式很容易得到。
  * 最终状态：`d[m-1][n-1]`。返回第m行n列到起点的走法。

* 复杂度

  * Time：O（m * n），与总的格子个数有关（0 ms）
  * Space：O（m * n），二维数组要保存每个格子

```java
public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        // 状态数组
        int[][] res = new int[m][n];
    	// 初始状态
        for (int i = 0; i < m; i++) res[i][0] = 1;
        for (int i = 0; i < n; i++) res[0][i] = 1;
        // 状态方程
        for (int i = 1; i < m; i++) 
            for (int j = 1; j < n; j++) 
                res[i][j] = res[i - 1][j] + res[i][j - 1];
    	// 最终状态
        return res[m - 1][n - 1];
}
```

另外，这里的二维状态数组可以优化成一维数组。

## [63. 不同路径 II²](https://leetcode-cn.com/problems/unique-paths-ii/)

一个机器人位于一个 *m x n* 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

![img](https://img-blog.csdnimg.cn/img_convert/a9d710311ab12af6970636a006576dc1.png)

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

**说明**：*m* 和 *n* 的值均不超过 100。

**示例 1:**

```
输入:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

### 解法：动态规划

* 思路：分解模型且存在重复问题（==>求起点到每个格子的有几种路径）。递推公式，`f(x, y) = grid[x, y] ? 0 : f(x - 1, y) + f(x, y - 1)`。
    * 状态数组：`d[m][n]`。起点（0,0）到（mn）位置有多少种走法。这里注意，因为是数组下标，所以（mn）表示m+1行n-1列。
    * 初始状态：同样是初始第一行`d[0][i]` 和第一列`d[i][0]`作为递推依据(同上题)，但是不能直接就初始化为1，而是要根据`d[0][0]`和之前位置是否有石头来判断
	    * `d[0][0] = obstacleGrid[0][0] == 1 ? 0 : 1`
	    * `d[i][0] = obstacleGrid[m][n] == 1 ? 0 : d[i-1][0]`，`d[0][i]`同理
    * 状态方程：`d[m][n] = obstacleGrid[m][n] == 0 ? 0 : d[m - 1][n] + d[m][n - 1]`。与上题类似，只不过多了一个判断，如果存在障碍物，那么`d[m][n]=0`，否则根据前一状态递推（同上题）。
    * 最终状态：`d[m-1][n-1]`

```java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0) return 0;
       
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        // 状态数组
        int[][] d =new int[m][n];
    	// 初始状态
        d[0][0] = obstacleGrid[0][0] == 1 ? 0 : 1;
        for (int i = 1; i < m; i++) d[i][0] = obstacleGrid[i][0] == 1 ? 0 : d[i - 1][0];
        for (int i = 1; i < n; i++) d[0][i] = obstacleGrid[0][i] == 1 ? 0 : d[0][i - 1];
        // 状态方程
        for (int i = 1; i < m; i++) 
            for (int j = 1; j < n; j++)
				 // 需要判断是否是障碍物格
                d[i][j] = obstacleGrid[i][j] == 1 ? 0 : d[i - 1][j] + d[i][j - 1];
        // 最终状态
        return d[m - 1][n - 1];
}
```
本题也可以将二维状态数组优化成一维。

## [120. 三角形最小路径和²](https://leetcode-cn.com/problems/triangle/)

给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

自顶向下的最小路径和为 `11`（即，**2** + **3** + **5** + **1** = 11）。

**说明：**

如果你可以只使用 *O*(*n*) 的额外空间（*n* 为三角形的总行数）来解决这个问题，那么你的算法会很加分。

### 解法一：分治（超时）
分解模型（==>求到每个节点的最小路和）。递推公式，`f(x, y) = min (f(x+1, y), f(x, y+1))`。有了递推公式就很容易得到分治代码：

```java
class Solution {
    // 这里传入的参数是List<List>>，所有由两点注意：
    // 1.list获取大小：list.size() 
    // 2.list获取指定元素：list.get(idx)，而嵌套List<List>>就是list.get(row).get(col)
    public int minimumTotal(List<List<Integer>> triangle) {
        if (triangle == null || triangle.size() == 0) return 0;
        return help(0,0,triangle);
    }

    private int help(int x, int y, List<List<Integer>> triangle) {
    	// 如果达到了三角形最后一行，那么返回就行了。
        if (y == triangle.size() - 1) return triangle.get(y).get(x);
        return Math.min(help(x, y + 1, triangle), help(x + 1, y + 1, triangle)) + triangle.get(y).get(x);
    }
}
```

### 解法二：记忆递归
* 思路：既然有很多重复的计算，那么就通过一个状态数组来保存所有中间状态
* 复杂度：
	* Time：O(n^2) 
	* Space：O(n^2)
```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        if (triangle == null || triangle.size() == 0) return 0;
        int m = triangle.size();
        // 创建一个二维状态数组，用于在递归函数中记录状态
        return help(0, 0, new int[m][m], triangle);
    }
	
    private int help(int level, int idx, int[][] visit, List<List<Integer>> triangle) {
        if (level == triangle.size() - 1) return triangle.get(level).get(idx);
        // 如果状态数组没有当前层要求的状态再去分治求解
        if (visit[level][idx] == 0) {
            int left = help(level + 1, idx, visit, triangle);
            int right = help(level + 1, idx + 1, visit, triangle);
            visit[level][idx] = Math.min(left, right) + triangle.get(level).get(idx);
        }
        // 直接通过状态数组返回
        return visit[level][idx];
    }
}
```

### 解法三：动态规划

* 思路：自底向上，从最后一行（已知）出发
  * 状态数组：`d[i][j]`。从最后一行到位置（i,j）的最小路径和
  * 初始状态：`d[i][最后一行] = triangle.get(m - 1).get(i)`。只有最后一行可以确定最小路径就是自己
  * 状态方程：`d[i][j] = min(d[i + 1][j + 1] + d[i + 1][j]) + tri(i,j)`
  * 最终状态：`d[0][0]`
* 复杂度
  * Time：O（n ^ 2)
  * Space：O（n^ 2）

```java
public int minimumTotal(List<List<Integer>> triangle) {
        int m = triangle.size();
    	// 状态数组
        int d[][] = new int[m][m];
    	// 初始化基准值：最后一行放入状态数组
        for (int i = 0; i < m; i++) 
            d[m - 1][i] = triangle.get(m - 1).get(i);
    	// 开始遍历，从倒数第二行开始，所以 i - 2
        for (int i = m - 2; i >= 0; i--) 
            // 注 ：这里类似三角形，所以,j <= i
            for (int j = 0; j <= i; j++) 
                d[i][j] = Math.min(d[i + 1][j], d[i + 1][j + 1]) + triangle.get(i).get(j);
        return d[0][0];
}
```

