## [78. 子集²](https://leetcode-cn.com/problems/subsets/)

给定一组**不含重复元素**的整数数组 *nums*，返回该数组所有可能的子集（幂集）。

**说明**：解集不能包含重复的子集。

**示例:**
```
输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

### 解法：回溯

- 思路：树形问题，并存在回退。树如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015163009543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)


  - 递归函数：`void f （int idx, List<Integer> list, List<List<Integer>> res, int[] nums)`
	  - level：idx。可以从上图看出，划分层的是当前元素在数组的位置
	  - var：list，res。list保存当前层的数们，res保存最终结果
	  - info：nums。
  - 函数体：
    - termination：当走到数组最后一个元素时结束，即 `idx == nums.length`
    - process：要么加上当前层元素，要么不加，即 `list || list.add(nums[i])`
    - down：二叉树，`f (idx + 1, list, nums, res)`
    - **revert**：因为是list直接add，若不clear则会影响到上一层，即 `list. remove(list.size() - 1)`
- 复杂度
  - Time：O（n * 2ⁿ），一共2ⁿ个状态（节点），每种状态需要O(n)时间来构造子集
  - Space：O（n），层变量list的空间代价是O(n)

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        if (nums == null || nums.length == 0) return new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        subsets(0, new ArrayList<Integer>(), res, nums);
        return res;
    }

    private void subsets(int idx, List<Integer> list, List<List<Integer>> res, int[] nums) {
        if (idx == nums.length) {
            // 因为add的是引用，而num有通信的作用，每一层都会用，会一直改变
            res.add(new ArrayList<Integer>(list));
            return;
        }
        // 不加当前数字
        subsets(idx + 1, list, res, nums);
        
        // 加当前数字
        list.add(nums[idx]);
        subsets(idx + 1, list, res, nums);
        list.remove(list.size() - 1); // 需要回退
    }
}
```

## [46. 全排列²](https://leetcode-cn.com/problems/permutations/)

给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**示例:**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### 解法：回溯

* 思路：树形问题，并存在回退。树如下图
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015183012199.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
	* 递归函数：`void f( List<Integer> num, List<List<Integer> res, int[] nums)`
		* level：不用专门定义，通过层变量就能解决
		* var：num，res。不用多说啥，可以通过num的大小判断是否到叶节点
		* info：nums
	* 函数体：
		* termination：num.size() == nums.length
		* process：给num添加它没有的元素
		* down：最大是nums.length叉
		* revert：集合，没啥说的 `num.remove(num.size()-1)`
* 复杂度
  * Time：O（n * n!）
  * Space：O（n）

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        dfs(new ArrayList<>(), res, nums);
        return res;
    }

    public void dfs(List<Integer> num, List<List<Integer>> res, int[] nums) {
        if (num.size() == nums.length) {
            // 因为add的是引用，而num有通信的作用，每一层都会用，会一直改变
            res.add(new ArrayList<>(num));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
        	// 只有num不包含位置i的元素时才下探
            if (num.contains(nums[i])) continue;
            num.add(nums[i]);
            dfs(num, res, nums); 
            num.remove(num.size() - 1); // 回退
        }
    }
}
```
这里再给出另一种写法，思路也很简单
```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();

        List<Integer> num = new ArrayList<>();
        for (int n : nums) num.add(n);

        dfs(0, num, res);
        return res;
    }

    public void dfs(int start, List<Integer> num, List<List<Integer>> res) {
        if (start == num.size()) {
            res.add(new ArrayList<>(num));
        } else {
            for (int i = start; i < num.size(); i++) {
                Collections.swap(num, i, start);
                dfs(start + 1, num, res);
                Collections.swap(num, i, start);
            }
        }
    }
}
```

## [51. N皇后³](https://leetcode-cn.com/problems/n-queens/)

*n* 皇后问题研究的是如何将 *n* 个皇后放置在 *n*×*n* 的棋盘上，并且使皇后彼此之间不能相互攻击。

![img](https://img-blog.csdnimg.cn/img_convert/0242897a3a17b699ec318bfcbeb17e3b.png)

上图为 8 皇后问题的一种解法。

给定一个整数 *n*，返回所有不同的 *n* 皇后问题的解决方案。

每一种解法包含一个明确的 *n* 皇后问题的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

**示例:**

```
输入: 4
输出: [
 [".Q..",  // 解法 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // 解法 2
  "Q...",
  "...Q",
  ".Q.."]
]
解释: 4 皇后问题存在两个不同的解法。
```

### 解法：回溯*
因为每个位置可能放棋子，所以这也是一个树形问题，并且存在放置位置错误的回退。递归树太大就不画了。具体内容见代码注释，其实主要问题还是在于判断当前位置是否有效，回溯的逻辑很好理解。

```java
public class Solution {
    public List<List<String>> solveNQueens(int n) {
    	// 初始化空棋盘
        char[][] board = new char[n][n];
        // 初始化棋盘，就是个遍历二维数组的过程
        for(int i = 0; i < n; i++)
            for(int j = 0; j < n; j++)
                board[i][j] = '.';
        List<List<String>> res = new ArrayList<List<String>>();
        // 回溯
        dfs(board, 0, res);
        return res;
    }
    
    // 递归函数
    private void dfs(char[][] board, int colIndex, List<List<String>> res) {
    	// termination。最后一行都走完了，说明找到了一嘴，把它加入res
        if(colIndex == board.length) {
        	// 将二维数组转化为List
            res.add(construct(board));
            return;
        }
        
        // process。本层逻辑
        for(int i = 0; i < board.length; i++) {
        	// 判断当前位置是否有效 
            if(validate(board, i, colIndex)) {
            	// 尝试在当前位置放置棋子
                board[i][colIndex] = 'Q';
                // down。进入下一层继续找
                dfs(board, colIndex + 1, res);
                // revert。状态回退
                board[i][colIndex] = '.';
            }
        }
    }
    
    // 判断[x][y]是否能放置棋子
    private boolean validate(char[][] board, int x, int y) {
        for(int i = 0; i < board.length; i++) {
            for(int j = 0; j < y; j++) {
            	// 判断当前位置的右上角和左上角有没有棋子
                if(board[i][j] == 'Q' && (x + j == y + i || x + y == i + j || x == i))
                    return false;
            }
        }
        
        return true;
    }
    
    // 将数组转化为list，为了最后返回的结果
    private List<String> construct(char[][] board) {
        List<String> res = new LinkedList<String>();
        for(int i = 0; i < board.length; i++) {
            String s = new String(board[i]);
            res.add(s);
        }
        return res;
    }
}
```

## [36. 有效的数独²](https://leetcode-cn.com/problems/valid-sudoku/)

判断一个 9x9 的数独是否有效。只需要**根据以下规则**，验证已经填入的数字是否有效即可。

1. 数字 `1-9` 在每一行只能出现一次。
2. 数字 `1-9` 在每一列只能出现一次。
3. 数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。

![img](https://img-blog.csdnimg.cn/img_convert/fec49e65ee2305a2309a0de7b88c2ea0.png)

上图是一个部分填充的有效的数独。

数独部分空格内已填入了数字，空白格用 `'.'` 表示。

**示例 1:**

```
输入:
[
  ["5","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
输出: true
```

**示例 2:**

```
输入:
[
  ["8","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
输出: false
解释: 除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。
     但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。
```

**说明:**

- 一个有效的数独（部分已被填充）不一定是可解的。
- 只需要根据以上规则，验证已经填入的数字是否有效即可。
- 给定数独序列只包含数字 `1-9` 和字符 `'.'` 。
- 给定数独永远是 `9x9` 形式的。
### 解法：暴力循环
说在前面，这个题是为了给下面解数独做准备。好了，看解法：

* 思路：依次验证 行重复 -> 列重复 -> 单位重复
  * 重复：使用set保存数据，若add失败则表示已经存在
  * 行列单位：
    * 行验证：for（行1-9）{ for（列1-9）}
    * 列验证：for（列1-9）{ for（行1-9）}
    * 单位验证：for（单位1-9）{ for（格子1-9）}
* 复杂度：
	* Time：O(1)，内外循环次数确定
	* Space：O(1)，set容量确定
```java
public boolean isValidSudoku(char[][] board) {
        Set<Character> set = new HashSet<>();
    	// 由于所有的for都是1-9所以可以合并到一个
        for (int i = 0; i < 9; i++) {
            // set只有一个，每次使用前需要清空
            set.clear();
            // 行验证
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.' ) continue;
                // add失败就是已经有了，返回false
                if (!set.add(board[i][j])) return false;
            }
            set.clear();
            
            // 列验证
            for (int j = 0; j < 9; j++) {
                // 【j】【i】，就是列不变行变，即验证列重复
                if (board[j][i] == '.') continue;
                if (!set.add(board[j][i])) return false;
            }
            set.clear();
            
            // 单位验证
            for (int j = 0; j < 9; j++) {
                // row，col不能直接用i j，需要计算：先算出第一个单位的，然后平移即可
                // 0单位：row：j/3, col：j%3
                // 1-8单位：+ 3*（）
                int row = j / 3 + (i / 3) * 3;
                int col = j % 3 + (i % 3) * 3;
                if (board[row][col] == '.') continue;
                if (!set.add(board[row][col])) return false;
            }
        }
        return true; 
}
```
## [37. 解数独³](https://leetcode-cn.com/problems/sudoku-solver/)
编写一个程序，通过填充空格来解决数独问题。

一个数独的解法需遵循如下规则：

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
空白格用 '.' 表示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015223355173.png#pic_center)
一个数独。
![http://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png](https://img-blog.csdnimg.cn/20201015223409675.png#pic_center)
答案被标成红色。

提示：

给定的数独序列只包含数字 1-9 和字符 '.' 。
你可以假设给定的数独只有唯一解。
给定数独永远是 9x9 形式的。

## 解法：回溯*
```java
public class Solution {
    public void solveSudoku(char[][] board) {
        if(board == null || board.length == 0)
            return;
        solve(board);
    }
    
    public boolean solve(char[][] board){
        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                if(board[i][j] == '.'){
                    for(char c = '1'; c <= '9'; c++){//trial. Try 1 through 9
                        if(isValid(board, i, j, c)){
                            board[i][j] = c; //Put c for this cell
                            
                            if(solve(board))
                                return true; //If it's the solution return true
                            else
                                board[i][j] = '.'; //Otherwise go back
                        }
                    }
                    
                    return false;
                }
            }
        }
        return true;
    }
    
    private boolean isValid(char[][] board, int row, int col, char c){
        for(int i = 0; i < 9; i++) {
            if(board[i][col] != '.' && board[i][col] == c) return false; //check row
            if(board[row][i] != '.' && board[row][i] == c) return false; //check column
            if(board[3 * (row / 3) + i / 3][ 3 * (col / 3) + i % 3] != '.' && 
board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false; //check 3*3 block
        }
        return true;
    }
}
```
