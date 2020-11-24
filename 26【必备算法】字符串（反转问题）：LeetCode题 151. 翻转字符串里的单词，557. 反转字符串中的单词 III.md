在看本文之前，最好先看前一篇 [字符串（反转问题）：LeetCode题 344. 反转字符串，541. 反转字符串 II，917. 仅仅反转字母](https://blog.csdn.net/weixin_43935927/article/details/109122374)。然后再看本篇反转字符串的单词。
## [151. 翻转字符串里的单词²](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

给定一个字符串，逐个翻转字符串中的每个单词。

**示例 1：**

```
输入: "the sky is blue"
输出: "blue is sky the"
```

**示例 2：**

```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

**示例 3：**

```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

**说明：**

- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

### 解法：双指针（模板）
思路也很简单，就是将所有单词先构造成一个字符串数组String[]，然后再用反转模板，最后再将数组组装成字符串。
```java
public String reverseWords(String s) {
    	// 1.分割出单词们：将字符串去除首尾空格后，根据空格分隔
    	// 注：这里用了正则表达式 \S表空白符，+表示多个，所以 \\S+
        String[] help = s.trim().split("\\s+");
    	// 2.对分割出的单词数组翻转
        int m = 0, n = help.length - 1;
        while (m < n) {
            String tmp = help[m]; help[m++] = help[n]; help[n--] = tmp;
        }       
    	// 3.组装字符串
        String res = "";
        for (int i = 0; i < help.length; i++) 
            res += help[i] + " ";
    	// 注：最后的结构要去除最后一个空格
        return res.trim();
    }
```
因为上面解法大量调用了库函数（split，trim）所以还能优化...

## [557. 反转字符串中的单词 III¹](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)

给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

**示例 1:**

```
输入: "Let's take LeetCode contest"
输出: "s'teL ekat edoCteeL tsetnoc" 
```

**注意**：在字符串中，每个单词由单个空格分隔，并且字符串中不会有任何额外的空格。

### 解法：双指针（模板）
跟151题差不多，还是先将单词构造成字符串数组String[]，然后再逐单词反转
```java
public String reverseWords(String s) {
    	// 1.分割出单词们
        String[] help = s.split("\\s+");
        String res = "";
    	// 2.逐个翻转单词
        for (int i = 0; i < help.length; i++) {
            char[] c = help[i].toCharArray();
            int m = 0, n = c.length - 1;
            while (m < n) {
                char tmp = c[m]; c[m++] = c[n]; c[n--] = tmp;
            }
            // 3.组装结果
            res = res + new String(c) + " ";
        }
    	// 注：去除末尾空格
        return res.trim();
    }
```
