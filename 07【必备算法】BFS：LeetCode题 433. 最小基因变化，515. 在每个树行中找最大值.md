## [433. 最小基因变化²](https://leetcode-cn.com/problems/minimum-genetic-mutation/)

一条基因序列由一个带有8个字符的字符串表示，其中每个字符都属于 `"A"`, `"C"`, `"G"`, `"T"`中的任意一个。

假设我们要调查一个基因序列的变化。**一次**基因变化意味着这个基因序列中的**一个**字符发生了变化。

例如，基因序列由`"AACCGGTT"` 变化至 `"AACCGGTA" `即发生了一次基因变化。

与此同时，每一次基因变化的结果，都需要是一个合法的基因串，即该结果属于一个基因库。

现在给定3个参数 — start, end, bank，分别代表起始基因序列，目标基因序列及基因库，请找出能够使起始基因序列变化为目标基因序列所需的最少变化次数。如果无法实现目标变化，请返回 -1。

**注意:**

1. 起始基因序列默认是合法的，但是它并不一定会出现在基因库中。
2. 所有的目标基因序列必须是合法的。
3. 假定起始基因序列与目标基因序列是不一样的。

**示例 1:**

```
start: "AACCGGTT"
end:   "AACCGGTA"
bank: ["AACCGGTA"]

返回值: 1
```

**示例 2:**

```
start: "AACCGGTT"
end:   "AAACGGTA"
bank: ["AACCGGTA", "AACCGCTA", "AAACGGTA"]

返回值: 2
```

**示例 3:**

```
start: "AAAAACCC"
end:   "AACCCCCC"
bank: ["AAAACCCC", "AAACCCCC", "AACCCCCC"]

返回值: 3
```

### 解法：BFS（层序遍历）

* 思路
  * 错误思路：将start与end进行比较，找出不同位置字母，然后修改看是否是在bank中
    * 问题：无论是顺序还是逆序比较，都会出现因为修改顺序而出现问题，参考例3
  * 正确思路：一次一次变，记录合法变化，并在此基础上继续变化 --> 节点：变化出来的字符串们   --> 最大分叉：length * 4  --> BFS（层序：记录次数）
    * 注意：
      * 剪枝：可见树已经很大了，因此通过一个set记录所有遍历过的集合，若有就不当节点了
      * 生成新的字符串：原字符串数组化 --> new String
* 复杂度
  * Time：O（ 4^len），与节点个数成正比
  * Space：O（ 4^len），存储每个节点的字符串数组

```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {
        if (start.equals(end)) return 0;
        
        Queue<String> queue = new LinkedList<>(){{ offer(start); }};
        Set<String> bankSet = new HashSet<>() {{ for (String s : bank) add(s); }};
        Set<String> visitedSet = new HashSet<>() {{ add(start); }};
        char[] charArr = new char[]{'A','C','G','T'};
        int step = 0;
		
		// 1.开始BFS
        while (!queue.isEmpty()) {
            int size = queue.size();
            // 2.遍历当前层
            for (int i = 0; i < size; i++) {
                String tmp = queue.poll();
                if (tmp.equals(end)) return step;
                // 注意：先变成字符数组，后面才能改变，然后生成新字符串
                char[] tmpArr = tmp.toCharArray();
                // 3.将下一层节点加入队列：此处是将字符串所有字符都变
                for (int j = 0; j < tmpArr.length; j++) {
                    // 3.1 记录当前位置的字符
                    char old = tmp.charAt(j);
                    // 3.2 修改当前字符，尝试进入下一层
                    for (char c : charArr) {
                        tmpArr[j] = c;
                        String change = new String(tmpArr);
                        // 修改后的字符在bank中，且没有出现过就加入队列（剪枝）
                        if (bankSet.contains(change) && !visitedSet.contains(change)) {
                            queue.offer(change);
                            visitedSet.add(change);
                        }
                    }
                    // 3.3 恢复成原状态，再去遍历下一层
                    tmpArr[j] = old;
                }
            }
            // 4.step++
            step++;
        }
        return -1;
    }
}
```
## [515. 在每个树行中找最大值²](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/)

您需要在二叉树的每一行中找到最大的值。

**示例：**

```
输入: 

          1
         / \
        3   2
       / \   \  
      5   3   9 

输出: [1, 3, 9]
```

### 解法：BFS（层序遍历）

* 思路：正常层序遍历，在每一层遍历时加入比较即可
* 复杂度
  * Time：O（n），与节点个数正比
  * Space：O（n），每个节点都要入队

```java
 public List<Integer> largestValues(TreeNode root) {
        if (root == null) return new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>() {{ offer(root); }};
        List<Integer> res = new ArrayList<>();

        while (!queue.isEmpty()) {
            // 注意：为了保证max初始值是层第一个节点，这里用int最小值，peek也行但是多了一步操作
            int max = Integer.MIN_VALUE, size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                max = Math.max(node.val, max);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res.add(max);
        }
        return res;
}
```
另外，BFS算法在二叉树中还有大量应用，相关LeetCode题参考
* [【数据结构】二叉树：LeetCode题（三）226. 翻转二叉树，104.二叉树的最大深度，111.二叉树的最小深度](https://blog.csdn.net/weixin_43935927/article/details/109063596)
* [【数据结构】二叉树：LeetCode题（四）100. 相同的树，98. 验证二叉搜索树，28. 对称的二叉树](https://blog.csdn.net/weixin_43935927/article/details/108944238)
