写在前面，判断是不是回文的逻辑其实很简单，就是借助双指针不断判断首尾相应位置元素是否相等，java模板如下：
```java
boolean isPalindrom(String str, int left, int right) {
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) return false;
	    left++; right--;
    }
    return true;
}
```
---
## [125. 验证回文串¹](https://leetcode-cn.com/problems/valid-palindrome/)

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

**说明**：本题中，我们将空字符串定义为有效的回文串。

**示例 1:**

```
输入: "A man, a plan, a canal: Panama"
输出: true
```

**示例 2:**

```
输入: "race a car"
输出: false
```

### 解法：双指针（模板）
本题的话，除了核心逻辑回文外，还要处理两个问题：
1. 只对字母进行判断，符号和空格跳过
2. 在进行字母相等判断时要忽略大小写

对于这两个问题，可以直接调用String提供的方法，也可以自己封装处理方法。我下面的代码没有用String提供的字符串方法。

```java
class Solution {
    public boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
        	// 首先判断首尾位置是否合法
            if (!isAplh(s.charAt(i))) i++;
            else if (!isAplh(s.charAt(j))) j--;
            // 合法后，判断是否回文，即相应位置元素是否相等
            else if (equalsIgnoreCase(s.charAt(i),s.charAt(j))) { i++; j--;} 
            else return false;
        }
        return true;
    }
	
	// 判断是否为字母
	// 思路：直接用字符比大小进行，两个区间
    private boolean isAplh(char c) {
        return (c >= 'A' && c <= 'Z') || (c >= 'a' && c <= 'z') || (c >= '0' && c <= '9');
    }
	
	// 字母相等判断，忽略大小写。
	// 思路：若出现大写，则转化为小写，体现在代码上就是ASCII+32
    private boolean equalsIgnoreCase(char a, char b) {
        if (a >= 'A' && a <= 'Z') a += 32;
        if (b >= 'A' && b <= 'Z') b += 32;
        return a == b;
    }
}
```
这里再把调用String的toLowerCase的解法写一下：
```java
class Solution {
    public boolean isPalindrome(String s) {
        // 使用了String 的 toLowerCase方法。因为要将所有字母都变成小写，所以会多出来一次遍历操作。
        s = s.toLowerCase();
        int i = 0, j = s.length() - 1;
        while (i < j) {
            if (!isAplh(s.charAt(i))) i++;
            else if (!isAplh(s.charAt(j))) j--;
            // 此处直接比较就行，不用再写一个忽略大小写的比较函数
            else if (s.charAt(i) == s.charAt(j)) { i++; j--;} 
            else return false;
        }
        return true;
    }

    private boolean isAplh(char c) {
        return ((c >= 'a' && c <= 'z') || (c >= '0' && c <= '9'));
    }
}
```
这个解法是的执行结果果然更长，3ms。

## [680. 验证回文字符串 Ⅱ¹](https://leetcode-cn.com/problems/valid-palindrome-ii/)

给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。

**示例 1:**

```
输入: "aba"
输出: True
```

**示例 2:**

```
输入: "abca"
输出: True
解释: 你可以删除c字符。
```

**注意:**

1. 字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。

### 解法一：枚举法（超时）
* 思路：很粗暴，将所有删一个字符的情况枚举出来，然后再调用上题的逻辑判断是否回文。
* 复杂度：
	* Time：O（n²)
	* Space：O（n）
```java
class Solution {
    public boolean validPalindrome(String s) {
        char[] c = s.toCharArray();
        // 枚举去掉一个字符的所有结果，然后判断是否回文
        for (int i = 0; i < c.length; i++) {
            // 将删除位置置为‘ ’，在判断回文时会跳过
            c[i] = ' ';
            if (isPalindrome(new String(c))) return true;
            c[i] = s.charAt(i);
        }
        return false;
    }

    public boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            if (!(s.charAt(i) >= 'A' && s.charAt(i) <= 'Z')) i++;
            else if (!(s.charAt(j) >= 'A' && s.charAt(j) <= 'Z')) j--;
            else if (s.charAt(i) == s.charAt(j)) { i++; j--;} 
            else return false;
        }
        return true;
    }
}
```

### 解法二：枚举两边

* 思路：若可以回文，那么前后的相应位置一定相同 ---> 不断将前后不对应的位置删除，然后判断是否能成为回文串。若某次删除不能使回文，那么返回false。
* 复杂度：
 	* Time：O(n)
 	* Space：O(1)  
```java
class Solution {
    public boolean validPalindrome(String s) {
        int l = 0, r = s.length() - 1;
        // 双指针，从前后开始遍历
        while (l < r) {
            // 找到前后不对应的位置
            if (s.charAt(l) != s.charAt(r)) 
                // 删除 前 / 后 看是否能回文
                // 注：所有经过循环的都是已经对应的位置，因此可以理解成删除的是一个新串的首尾，
                //     即可以直接在当前串进行操作，通过索引模拟删除
                return isPalindrome(s, l + 1, r) || isPalindrome(s, l, r - 1);
            l++; r--;
        }
        return true;
    }
	// 这里只有小写字母，因此判断逻辑就是基本的双指针判断即可
    public boolean isPalindrome(String s, int l, int r) {
        while (l < r) {
            if (s.charAt(l) != s.charAt(r)) return false;
            l++; r--;
        }
        return true;
    }
}
```
## [647. 回文子串²](https://leetcode-cn.com/problems/palindromic-substrings/)

给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被计为是不同的子串。

**示例 1:**

```
输入: "abc"
输出: 3
解释: 三个回文子串: "a", "b", "c".
```

**示例 2:**

```
输入: "aaa"
输出: 6
说明: 6个回文子串: "a", "a", "a", "aa", "aa", "aaa".
```

### 解法一：枚举法（超时）
枚举出来所有子串，然后判断是否回文，回文就保存。
### 解法二：枚举中心

* 思路：以每个元素都作为中心，向两边进行扩展
  * 向两边扩展的次数，就是以当期字符作为中心的回文串数
  * 注：这里要考虑中心是 单个元素 / 两个元素 这两种情况
* 复杂度
  * Time：O（n ^ 2)
  * Space：O（1）

```java
class Solution {
    public int countSubstrings(String s) {
        if (s == null || s.length() == 0) return 0;
        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            // 1.以当前字符为中心
            count += expand(s, i, i);
            // 2.以当前字符和相邻字符为中心
            count += expand(s, i, i+1);
        }
        return count;
    }

    private int expand(String s, int left, int right) {
        int count = 0;
        // 注：因为是向两边扩散，所以这里的条件是 left >= 0, right < len
        while (left >= 0 && right < s.length()) {
            if (s.charAt(left) != s.charAt(right)) return count;
            // 扩散一次就代表有一个以当前为中心的回文串
            count++;
            left--; right++;
        }
        return count;
    }

}
```
## [5. 最长回文子串²](https://leetcode-cn.com/problems/longest-palindromic-substring/)

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

### 解法一：枚举法（超时）
枚举出来所有子串，然后判断是否回文，然后记录长度求最长。
### 解法二：枚举中心

* 思路：计算以每个字符(i)为中心的回文串长度(len) --> 再用start标记起始位置即可（start=i-(len-1)/2)
* 复杂度
  *  Time：O（n²）
  * Space：O（1）

```java
class Solution {
    
    public String longestPalindrome(String s) {
        int start = 0, maxLen = 0;
        for (int i = 0; i < s.length(); i++) {
            // 中心奇数 aba
            int len1 = countLen(s, i, i);
            // 中心偶数 abba
            int len2 = countLen(s, i, i+1);
            int len = Math.max(len1, len2);
            // 更新start，-1是因为len包含了当前字符
            if (len > maxLen) {
                start = i - (len - 1) / 2;
                maxLen = len;
            }
        }
        return s.substring(start, start + maxLen);
    }

    int countLen(String s, int left, int right) {
        // 从中心向两边扩散
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--; right++;
        }
        return right - left - 1;
    }
}
```


