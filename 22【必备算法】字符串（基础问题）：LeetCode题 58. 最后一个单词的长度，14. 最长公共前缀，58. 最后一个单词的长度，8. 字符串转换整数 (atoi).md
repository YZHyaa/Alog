## [58. 最后一个单词的长度¹](https://leetcode-cn.com/problems/length-of-last-word/)
给定一个仅包含大小写字母和空格 `' '` 的字符串 `s`，返回其最后一个单词的长度。如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。

如果不存在最后一个单词，请返回 0 。

**说明**：一个单词是指仅由字母组成、不包含任何空格字符的 **最大子字符串**。

**示例:**

```
输入: "Hello World"
输出: 5
```
### 解法：顺推
* 思路：不是找出最后一个字符串后再求长度，而是直接找到最后一个单词起始索引，然后用长度减去就行
* 复杂度：
	* Time：O(n）
	* Space：O(1)

```java
// 关键测试用例："A" = 1, " A" = 1 ,"A " = 1
public int lengthOfLastWord(String s) {
        if(s.length() == 0) return 0;
		
    	// 寻找去掉尾部空格的长度
    	// 1.去掉首尾空格的长度
        int length = s.trim().length();
        // 2.首部空格不能去。所以，如果首部有空格，则将首部空格的长度加上
        for (int i = 0; i < s.length() ; i++) {
            if (s.charAt(i) != ' ') break;
            length++;
        }
		
    	// 寻找最后一个空格位置
    	// 注：-1也保证了 “A”=1
        int lastSpaceIdx = -1;
        for (int i = 0; i <length; i++) {
            if (s.charAt(i) == ' ') lastSpaceIdx = i;
        }
    	// 最后单词长度 = （length - 1） - (lastSpaceIdx + 1) + 1
        return length - lastSpaceIdx - 1;
}
```
## [14. 最长公共前缀¹](https://leetcode-cn.com/problems/longest-common-prefix/)

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1:**

```
输入: ["flower","flow","flight"]
输出: "fl"
```

**示例 2:**

```
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

**说明:**

所有输入只包含小写字母 `a-z` 。

### 1.顺推
* 思路：依次寻找每个字符，寻找的方式是遍历所有字符串，如果相同位置存在不同字符则返回
* 
```java
public String longestCommonPrefix(String[] strs) {
        if (strs.length < 1) return "";
    	// 找最短串，因为公共最长子串的最长情况=最短串
        int minLen = Integer.MAX_VALUE;
        for (int i = 0; i < strs.length; i++) 
            minLen = Math.min(minLen, strs[i].length());
        // 挨个遍历所有字符
        for (int i = 0; i < minLen; i++) 
            // 判断所有字符串中是否相同
            for (int j = 0; j < strs.length - 1; j++) 
            	// 相同位置不同字符，返回结果
                if (strs[j].charAt(i) != strs[j + 1].charAt(i)) 
                    return strs[j].substring(0, i); // subString[O,i)                                  
        return strs[0].substring(0, minLen);
    }
```



## [709. 转换成小写字母¹](https://leetcode-cn.com/problems/to-lower-case/)

实现函数 ToLowerCase()，该函数接收一个字符串参数 str，并将该字符串中的大写字母转换成小写字母，之后返回新的字符串。

**示例 1：**

```
输入: "Hello"
输出: "hello"
```

**示例 2：**

```
输入: "here"
输出: "here"
```

**示例** **3：**

```
输入: "LOVELY"
输出: "lovely"
```

### 解法：ASCII码

* 思路：大写字母转小写直接ASCII+32
* 复杂度：
	* Time：O(n)
	* Space：O(1)

```java
public String toLowerCase(String str) {
        if (str.length() == 0) return "";
		// 由于字符串是不变的，因此新建一个数组来存
    	// 注：这里可以直接新建一个数组，但是toCharArray就只用修改大写字母的地方
        char[] c = str.toCharArray();
        for (int i = 0; i < str.length(); i++) {
            // 判断哪个是大写字母
            // 注：这里不用记具体ASCII值，只要char进行比较就是用ASCII了
            if (str.charAt(i) >= 'A' && str.charAt(i) <= 'Z') 
                c[i] = (char)(str.charAt(i) + 32);
            else 
                c[i] = str.charAt(i);
        }
    	// 将char[]转String，也可以直接new String
        return String.valueOf(c);
    }
```

## [8. 字符串转换整数 (atoi)²](https://leetcode-cn.com/problems/string-to-integer-atoi/)

请你来实现一个 `atoi` 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

- 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
- 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
- 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0 。

**提示：**

- 本题中的空白字符只包括空格字符 `' '` 。
- 假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

 

**示例 1:**

```
输入: "42"
输出: 42
```

**示例 2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3:**

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4:**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

**示例 5:**

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

### 解法：ASCII

* 思路：本题就是根据要求一点一点判断，没什么要说的地方，就是细节。从字符转化为数字：c-'0'。步骤见注释。
* 复杂度
	* Time：O(n)
	* Space：O(1)

```java
public int myAtoi(String str) {
        int start = 0, i = 0, n = str.length();
        boolean negative = false;
    	// 1.寻找start位置
    	// 1.1 去处空格
        while (i < n && str.charAt(i) == ' ') i++;
        if (i == n) return 0;
	    // 1.2 正负判断
        if (str.charAt(i) == '+') {
            i++;
        } else if (str.charAt(i) == '-') {
            i++;
            negative = true;
        }
        // 1.3 去除前置0
        while (i < n && str.charAt(i) == '0') i++;
        start = i;
    
		// 2.寻找end位置
        // 2.1 寻找 '0' - '9'，这里就去除了示例3情况
        while (i < n && str.charAt(i) >= '0' && str.charAt(i) <= '9') i++;
        if (start == i) return 0;
		// 2.2 越界判断，位数大于10一定越界
        if (i - start > 10) {
            if (negative) return Integer.MIN_VALUE;
            else return Integer.MAX_VALUE;
        }
		
    	// 3.字符串转换
    	// 注：这里先用long，是因为可能转出来的数越界了如999999999
        long res = 0;
    	// 3.1 进行转换：得到每一位数字(charAt(i) - '0')。核心一步！！！
    	// 注：是从最高位开始转换的，因此res * 10
        for (int j = start; j < i; j++) 
            res = res * 10 + (str.charAt(j) - '0');
    	// 3.2 正负判断
        res = negative ? -res : res;
		// 3.3 越界判断
        if (res < Integer.MIN_VALUE) return Integer.MIN_VALUE;
        else if (res > Integer.MAX_VALUE) return Integer.MAX_VALUE;
    	// 注：这里要转回int
        else return (int) res;
    }
```





