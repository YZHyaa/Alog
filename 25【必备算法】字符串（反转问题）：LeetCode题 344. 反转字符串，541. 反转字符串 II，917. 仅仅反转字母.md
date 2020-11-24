写在前面，反转**数组**的思路很简单，就是借助双指针不断将首尾相应位置的字符交换。Java模板如下：
```java
public void reverseArray(char[] arr) {
        int i = 0, j = s.length - 1;
        while (i < j) {
            char tmp = arr[i]; 
            // 注意这里的i++ j--
            arr[i++] = arr[j]; 
            arr[j--] = tmp;
        }
    }
```
若是要反转字符串，就要将字符串先转换为一个或多个数组，然后再用上面模板。

---
## [344. 反转字符串¹](https://leetcode-cn.com/problems/reverse-string/)

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `char[]` 的形式给出。

不要给另外的数组分配额外的空间，你必须**原地修改输入数组**、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 [ASCII](https://baike.baidu.com/item/ASCII) 码表中的可打印字符。

**示例 1：**

```
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```

**示例 2：**

```
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```

### 解法：双指针（模板）
```java
public void reverseString(char[] s) {
        if (s == null || s.length == 0) return;
        int i = 0, j = s.length - 1;
        while (i < j) {
            char tmp = s[i]; 
            // 注意这里的i++ j--
            s[i++] = s[j]; 
            s[j--] = tmp;
        }
    }
```
## [541. 反转字符串 II¹](https://leetcode-cn.com/problems/reverse-string-ii/)

给定一个字符串 `s` 和一个整数 `k`，你需要对从字符串开头算起的每隔 `2k` 个字符的前 `k` 个字符进行反转。

- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

**示例:**

```
输入: s = "abcdefg", k = 2
输出: "bacdfeg"
```

**提示：**

1. 该字符串只包含小写英文字母。
2. 给定字符串的长度和 `k` 在 `[1, 10000]` 范围内。

### 解法：双指针（模板）

这里虽然说了什么 剩余 k 情况、2k情况，但实际上就是找到翻转点后翻转 k 个字符。另外，反转时注意 多余 k 和 少于 k 的情况。
```java
public String reverseStr(String s, int k) {
        char[] res = s.toCharArray();
        for (int i = 0; i < res.length; i++) {
            // 反转点：寻找规律得，所有要翻转的串的位置是 2k 的整数倍（0, 4）
            if (i % (2 * k) == 0) {
                // 反转k个字符
                // 注意：这里只用实际翻转少于 k 的情况考虑，即 i + k >= length
                int m = i, n = i + k >= res.length ? res.length - 1 : i + k - 1;
                while (m < n) {
                    char tmp = res[m]; res[m++] = res[n]; res[n--] = tmp;
                }
            }
        }
        return new String(res);
    }
```
## [917. 仅仅反转字母¹](https://leetcode-cn.com/problems/reverse-only-letters/)

给定一个字符串 `S`，返回 “反转后的” 字符串，其中不是字母的字符都保留在原地，而所有字母的位置发生反转。 


**示例 1：**

```
输入："ab-cd"
输出："dc-ba"
```

**示例 2：**

```
输入："a-bC-dEf-ghIj"
输出："j-Ih-gfE-dCba"
```

**示例 3：**

```
输入："Test1ng-Leet=code-Q!"
输出："Qedo1ct-eeLg=ntse-T!"
```

### 解法：双指针（模板）
还是反转问题，但是首尾的双指针定位不再是直接 `i++；j--`，而是要判断如果非字母就跳过。其实很好理解。
```java
public String reverseOnlyLetters(String S) {
        int i = 0, j = S.length() - 1;
        char[] c = S.toCharArray();
        while (i < j) {
            // 跳过非字符。
            // 注：在判断条件这里不能 （！（   ||   ））因为 || 与 ！同优先级，所以要加括号
            if (!((c[i] >= 'A' && c[i] <= 'Z') || (c[i] >= 'a' && c[i] <= 'z'))) i++;
            else if (!((c[j] >= 'A' && c[j] <= 'Z') || (c[j] >= 'a' && c[j] <= 'z'))) j--;
            // 反转
            else {
                char tmp = c[i]; c[i++] = c[j]; c[j--] = tmp;
            }
        }
        return new String(c);
    }
```

错误写法：这里想一次找到 i j ，反而增加了判断逻辑

```java
public String reverseOnlyLetters(String S) {
        int i = 0, j = S.length() - 1;
        char[] c = S.toCharArray();
        while (i < j) {
            // 这里的想法是直接找到 i
            while(! ((c[i] >= 'A' && c[i] <= 'Z') || (c[i] >= 'a' && c[i] <= 'z')))
                i++;
            // 直接找到 j
            while(! ((c[j] >= 'A' && c[j] <= 'Z') || (c[j] >= 'a' && c[j] <= 'z'))) j--;
            // 交换
            char tmp = c[i]; c[i++] = c[j]; c[j--] = tmp;
        }
        return new String(c);
    }
```
下一篇：[字符串（反转问题）：LeetCode题 151. 翻转字符串里的单词，557. 反转字符串中的单词 III](https://blog.csdn.net/weixin_43935927/article/details/109124658)。
