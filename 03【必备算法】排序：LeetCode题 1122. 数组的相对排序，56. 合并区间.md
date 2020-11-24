在看排序相关LeetCode题前，先放个传送门 [排序：图解十大排序算法及Java实现（详细）](https://blog.csdn.net/weixin_43935927/article/details/108952871)...
## [1122. 数组的相对排序¹](https://leetcode-cn.com/problems/relative-sort-array/)

给你两个数组，`arr1` 和 `arr2`，

- `arr2` 中的元素各不相同
- `arr2` 中的每个元素都出现在 `arr1` 中

对 `arr1` 中的元素进行排序，使 `arr1` 中项的相对顺序和 `arr2` 中的相对顺序相同。未在 `arr2` 中出现过的元素需要按照升序放在 `arr1` 的末尾。

**示例：**

```
输入：arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
输出：[2,2,2,1,4,3,3,9,6,7,19]
```

**提示：**

- `arr1.length, arr2.length <= 1000`
- `0 <= arr1[i], arr2[i] <= 1000`
- `arr2` 中的元素 `arr2[i]` 各不相同
- `arr2` 中的每个元素 `arr2[i]` 都出现在 `arr1` 中

### 解法：计数排序

* 思路：这里可以借鉴计数排序的思想，不清楚的童鞋可以参考面给出的链接。

  1. 入桶(buckets)：桶号 = arr1[i]，桶元素 = arr1[i]出现次数 --> 桶间是有序的（桶号是递增的）

  2. 入排序数组(ans)：根据arr2，将桶依次取出放入排序数组；然后桶元素--，直到桶取完

     注：这里与计数排序不同，计数排序需要通过累加得到下标。而这里的有序不是1-1001，有自己的顺序，因此直接对着取出即可

     ​	---> 一般计数排序也可以遍历1-n取出，但n若过大则时间就态复杂了，所以提前要计算好索引

  3. 剩余处理：将没有出现在arr2的元素，逐桶取出即可
* 复杂度：
	* Time：O(n)
	* Space：O(n)

```java
public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int[] buckets = new int[1001];
        int[] ans = new int[arr1.length];
    	// 1.入桶
        for (int i = 0; i < arr1.length; i++) {
            buckets[arr1[i]]++;
        }  
        int idx = 0;
    	// 2.入排序数组
        for (int i = 0; i < arr2.length; i++) {
            while (buckets[arr2[i]] > 0) {
                ans[idx++] = arr2[i];
                buckets[arr2[i]]--;
            }
        }
    	// 3.剩余处理
        for (int i = 0; i < 1001; i++) {
            while (buckets[i] > 0) {
                ans[idx++] = i;
                buckets[i]--;
            }
        }
        return ans;
}
```

## [56. 合并区间²](https://leetcode-cn.com/problems/merge-intervals/)

给出一个区间的集合，请合并所有重叠的区间。

**示例 1:**

```
输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2:**

```
输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

### 解法：排序+双指针

* 思路：这里不是遍历合并，而是要根据start，end去创建
  1. 排序：根据这些数组的首元素来排序；通过Array.sort排序二维数组时需要传入比较器
  2. 初始区间：保存初始区间intervals[0]（start，end）
  3. 创建区间：
     * 若当前区间start <= 当前end，则当前区间可与之前合成一个区间；更新end
     * 若当前区间start > 当前end，则要创建新区间了；创建后更新start，end
  4. 剩余处理：创建最后一个区间

```java
public int[][] merge(int[][] intervals) {
    	// 注：这里是通过List<int[]>来保存二维数组的；
    	// 通过List#add很方便；但在最后返回时要toArray(new int[0][])表示放到一个二维数组
        List<int[]> res = new ArrayList<>();
        if(intervals.length == 0 || intervals == null) return res.toArray(new int[0][]);
    	// 1.排序
    	// 这里通过lamada用函数式接口，实现了Comptor接口；表示比较两个一维数组i1，i2通过i[0]比较
        Arrays.sort(intervals, (i1, i2) -> i1[0] - i2[0]);
    	// 2.初始区间
        int start = intervals[0][0], end = intervals[0][1];
    	// 3.创建区间
        for (int[] arr : intervals) {
            if (arr[0] <= end) {
                end = Math.max(end, arr[1]);
            } else {
                res.add(new int[]{start, end});
                start = arr[0];
                end = arr[1];
            }
        }
    	// 4.剩余处理
        res.add(new int[]{start, end});
        return res.toArray(new int[0][]);
    }
```

