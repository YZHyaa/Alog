## [22. 括号生成²](https://leetcode-cn.com/problems/generate-parentheses/)

给出 *n* 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且**有效的**括号组合。

例如，给出 *n* = 3，生成结果为：

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

### 解法：DFS
* 思路：这很显然是一个树形，如图
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201013191530776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
	* 递归函数：`void dfs(int left, int right, String str, List<String>res, int n) `
		* 层变量（var）：str。 每层可以通过一个字符串进行通信，即上一层的字符串str在本层加上"("或")"。
		* 层级（level）：left，right。因为要判断括号的合法性，并且给出的已知是几对括号。
		* 已知（info）：res，n。题目已知n对括号，res保存最终结果
	* 函数体：
		* 结束条件（termination）：左右括号个数达到n对，即 left == n 且 right == n
		* 本层逻辑（process）：需要验证括号合法性，不能直接 param+()。验证左括号是 left<n，验证右括号是 right<left
		* 下探（down）：如上图，是二叉

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        generate(0, 0, "", res, n);
        return res;
    }

    public void generate(int left, int right, String str,List<String> res, int n) {
        if (left == n && right == n) {
            res.add (param);
            return;
        }
        if (left < n) generate(left + 1, right, str+ "(", res, n);
        if (right < left) generate(left, right + 1, str+ ")", res, n);
    }
}
```

## [17. 电话号码的字母组合²](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-MeqMGlum-1602662514437)(https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png)\]](https://img-blog.csdnimg.cn/20201014193907356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

**示例:**

```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

### 解法：DFS

* 思路：每个按键都有3/4种可能，是一个3（或4）叉树 --> DFS
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201014201041631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

  * 递归函数：`void dfs (int idx，String str，String digits，List<String> res)`
	  * var：str。同上题
	  * level：idx。处理到输入串的哪个字符了
	  * info：digits，res。
  * 函数体：
    * termination：所有输入的字符处理完就完了，即 idx == digits.length()
    * process：上层传下来的字符串加上当前按键任一字符，即 s + for（当前按键的字母）
    * down：三叉或四叉
* 复杂度
  * Time：O（3^n * 4 ^ m），输入的号码有几个是3字符，有几个是4字符
  * Space：O（3^n * 4 ^ m），所有结果都要保存到res

```java
class Solution {
	// 这里声明一个全局变量来保存按键与字母的映射关系
    Map<Character, String> map = new HashMap<>() {{
        put('2', "abc");
        put('3', "def");
        put('4', "ghi");
        put('5', "jkl");
        put('6', "mno");
        put('7', "pqrs");
        put('8', "tuv");
        put('9', "wxyz");
    }};

    public List<String> letterCombinations(String digits) {
        if (digits == null || digits.length() == 0) return new ArrayList<>();
        List<String> res = new ArrayList<>();
        dfs(0, "", res, digits);
        return res;
    }

    public void dfs(int idx, String str, List<String> res, String digits) {
        if (idx == digits.length()) {
            res.add(str);
            return;
        }
        String number = this.map.get(digits.charAt(idx));
        for (int i = 0; i < number.length(); i++) {
            dfs(idx + 1, str + number.charAt(i), res, digits);
        }
    }
}
```







## [200. 岛屿数量²](https://leetcode-cn.com/problems/number-of-islands/)

给定一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

**示例 1:**

```
输入:
11110
11010
11000
00000

输出: 1
```

**示例 2:**

```
输入:
11000
11000
00100
00011

输出: 3
```

### 解法：DFS（二维数组）

* 思路：搜索 1 相连的格子，将相连的 1 置为 visited 
* 复杂度：
  * Time：O（m*n）
  * Space：O（m*n）
* 补充：二维数组`int[][] arr`，比如 [[1,2],[3,4],[5,6]]。可以理解成数组的数组
  * 一维数组个数（行）：`arr.length`
  * 一维数组长度（列）：`arr[0].length`
  * 定位到具体元素：`arr[i][j]`

```java
class Solution {
    public int numIslands(char[][] grid) {
        // 边界判断：二维数组为空，里面的一维数组为空
        if (grid == null || grid.length == 0 || grid[0] == null || grid[0].length == 0) return 0;
        int res = 0;
        // 用来保存所有已经访问过的位置
        boolean[][] visited = new boolean[grid.length][grid[0].length];
        // 1.遍历二维数组
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[i].length; j++) {
                // 2.找到是1，且还没有被访问过的点
                if (grid[i][j] == '1' && !visited[i][j]) {
                    res++;
                    // 3.深度优先遍历，找到相连的1
                    dfs(i, j, visited, grid);
                }
            }
        }
        return res;
    }
	
    private void dfs(int x, int y, boolean[][] visited, char[][] grid) {
        // 3.1 termination：当前坐标（x，y），当前是否是0，当前是否已经被遍历过
        int m = grid.length, n = grid[0].length
        if (x < 0 || x >=m  || y < 0 || y >= n  || grid[x][y] == '0' || visited[x][y]) return;
        // 3.2 process：将当前 1 置为visited
        visited[x][y] = true;
        // 3.3 向上下左右再去dfs
        dfs(x - 1, y, visited, grid);
        dfs(x + 1, y, visited, grid);
        dfs(x, y - 1, visited, grid);
        dfs(x, y + 1, visited, grid);
    }
}
```








