## [771. 宝石与石头¹](https://leetcode-cn.com/problems/jewels-and-stones/)


 给定字符串`J` 代表石头中宝石的类型，和字符串 `S`代表你拥有的石头。 `S` 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。

`J` 中的字母不重复，`J` 和 `S`中的所有字符都是字母。字母区分大小写，因此`"a"`和`"A"`是不同类型的石头。

**示例 1:**

```
输入: J = "aA", S = "aAAbbbb"
输出: 3
```

**示例 2:**

```
输入: J = "z", S = "ZZ"
输出: 0
```

**注意:**

- `S` 和 `J` 最多含有50个字母。
-  `J` 中的字符不重复。

### 解法一：双重循环

* 思路：通过遍历J，不断判断S中的字符判断是否出现在J中
* 复杂度：
	* Time：O(m*n)
	* Space：O(1)
```java
public int numJewelsInStones(String J, String S) {
        int count = 0;
        for (int i = 0; i < J.length(); i++) 
            for (int j = 0; j < S.length(); j++) 
                if (J.charAt(i) == S.charAt(j)) count++;
        return count;
    }
```

### 解法二：散列表

* 思路：因为key（大小写字母）只有52个，而且有顺序规律，所以可以用散列表记录所有宝石及在S中出现的次数。这里再说一下，散列表就是数组，只不过是索引有规律的，可以扩容。
* 复杂度：
	* Time：O(n)
	* Space：O(1)，数组大小确定，与数据规模无关
```java
public int numJewelsInStones(String J, String S) {
        int count = 0;
    	// 这里只用数组就够了，不用map容器，因为数组索引就能表示key，不用专门存储key
    	// 注：1.这里数组的初始化大小如果是52就小了，因为A=65 a=97中间是32个元素 --> 58
    	//     2.为了节省内存，所以用boolean，因为只是做标记而已，没必要int
    	//     3.若是要保存所有ASCII，这里就是要new [256]
        boolean[] c = new boolean[58];
        for (int i = 0; i < J.length(); i++) {
            // 这里要 - 'A'
            c[J.charAt(i) - 'A'] = 1;
        }
        for (int i = 0; i < S.length(); i++) {
            if (c[S.charAt(i) - 'A']) count++;
        }
        return count;
    }
```

## [387. 字符串中的第一个唯一字符¹](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

**案例:**

```
s = "leetcode"
返回 0.

s = "loveleetcode",
返回 2.
```

**注意事项**：您可以假定该字符串只包含小写字母。

### 解法一：双重循环

* 思路：对每个字符都遍历一遍字符串，看有没有重复的
* 复杂度
  * Time：O（n²）
  * Space：O（1）

```java
public int firstUniqChar(String s) {
        if (s.length() == 0) return -1;
        if (s.length() == 1) return 0;
        for (int i = 0; i < s.length(); i++) {
            boolean flag = true;
            for (int j = 0; j < s.length(); j++) {
                // 注：这个 j != i 很关键，因为这是从头到尾遍历，会把当前位置也
                if (j != i && s.charAt(i) == s.charAt(j)) {
                    flag = false;
                    break;
                }
            }
            
            if (flag) return i;
        }
        return -1;
}
```

### 解法二：散列表

* 思路：与771题有相似之处，通过hash表保存每个字符的出现次数，然后再一次循环判断是否只有一个
* 复杂度
  * Time：O（n）
  * Space：O（1），确定大小的空间；注：若要保存全部字符就要256个了

```java
public int firstUniqChar(String s) {
        if (s.length() == 0) return -1;
        int[] c = new int[26];
        for (int i = 0; i < s.length(); i++) 
            // -'a'
            c[s.charAt(i) - 'a']++;
        for (int i = 0; i < s.length(); i++) 
            if (c[s.charAt(i) - 'a'] == 1)
                return i;
        return -1;
    }
```

